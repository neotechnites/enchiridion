# MONEYPATH lane — chaos review of the order lifecycle state machines (2026-07-26)

Charter: `work/steer-nestor-deep-review.md` §Lane charters #3 (lesson class C —
execution truth / the ledger must never believe fiction). Repo read-only; no orders run.

Scope traced: `crates/streak/src/{strategy,exec,signal}.rs`, `crates/house/src/{strategy,signal}.rs`,
`crates/volbook/src/strategy.rs`, `crates/engine/src/{strategy,risk,reconcile,kalshi,net}.rs`,
`nestor_bin/src/main.rs`. Tape checked: `data/streak_week1.jsonl` (12 `streak_signal` records,
**every one has `entry_path: null`** → the maker/backstop machinery has NEVER run live; all
findings below are pre-emptive, which is the point of the lane). `data/state.json`:
bankroll 106.03, peak 106.03, day_spent 0, open [].

---

## FINDINGS (most severe first)

### F1 — CRITICAL. A partially-filled maker leg leaves the remainder RESTING, and every path that could later book it refuses.

`crates/streak/src/strategy.rs:1510-1512` (`settle_maker_fill`) books the fill and calls
`drop_leg` — it never cancels the unfilled remainder. The doc comment above it
(`:1505-1509`) reasons only about *not topping up*; nothing cancels.

Scenario (concrete): T0+18s an incoming seller lifts 4 of our 10 resting contracts at 40¢.
Next 1 Hz pass, `poll_leg_fill` (`:1461-1501`) returns count=4 → `settle_maker_fill` books
4 @ 40¢, `drop_leg` removes the leg from `self.maker` and releases the reservation. **6
contracts are still live on the book** until `expiration_ts` = T0+60 — and Kalshi enforces
expiry LAZILY (~2-3 min sweep, the crate's own comment at `exec.rs:69-71`). The dip
continues at T0+33s and the remaining 6 fill.

Money outcome — the second fill is *permanently* invisible:
- `RiskManager::on_fill_actual_fee` refuses it: one-open-position-per-ticker guard,
  `crates/engine/src/risk.rs:386-393`.
- `RiskManager::adopt_orphan` also refuses it: `if count <= 0 || self.has_open(ticker)`,
  `risk.rs:442`. Reconcile's orphan adoption (`reconcile.rs:211-238`) therefore cannot
  rescue it either.
- `settle()` removes the position by ticker with the LOCAL count 4 (`risk.rs:493-495`), so
  6 contracts' P&L (±$6.00 at settlement, $2.40 of stake) never enters bankroll,
  `day_loss`, or the drawdown kill-switch.

Second-order: with the leg dropped, $2.40 of exchange-held collateral has no reservation →
`expected_cash` overstates real cash by $2.40 (`risk.rs:467`) → the divergence breaker
(`reconcile.rs:252`, threshold $2.00) trips a STICKY halt on the next 60s pass for a reason
no log explains.

Partial fills on a 10-lot resting bid are the normal case, not an exotic one.

**Cheapest check:** demo — rest 10 contracts 1¢ inside the ask on a live KXBTC15M window
(the same setup `work/verify-house-truth.md` used to get 5/5 fills in 4.7-45.9s), let it
partial, then `GET /portfolio/orders?status=resting&ticker=...` and confirm
`remaining_count > 0` for that order_id after the first fill lands. No code needed.

---

### F2 — CRITICAL. The house sleeve places real orders entirely outside the risk layer, so the global kill-switch does not stop it.

`crates/house/src/strategy.rs:452-487` (`place_leg`) calls
`eng.kalshi.place_resting_limit_raw(...)` DIRECTLY. There is no `risk.evaluate`, no
`reserve`, no `on_fill*`. `detect_fills` (`:492-589`) books fills only into the crate's own
`ProbeLedger` (`house/src/signal.rs:131-185`).

`House::run` (`:204-228`) checks only `self.state.halted` — its OWN sticky flag. It never
reads `eng.risk`. Both tasks share one `Engine` in `run` (`nestor_bin/src/main.rs:437-452`
spawns house; `:475` runs streak in the foreground).

Scenario: the drawdown kill-switch trips (`risk.rs:538`), or reconcile's divergence breaker
halts (`reconcile.rs:259`), or 5 consecutive placement failures halt
(`engine/strategy.rs:240-250`). Streak and volbook stop instantly (`evaluate` returns
`Rejection::Halted`, `risk.rs:288`). **House keeps quoting and filling real money at 3s
cadence, indefinitely**, until its own −$20 ledger stop or −5¢ markout stop fires. That is
the definition of a blind kill-switch.

**Cheapest check:** grep — `rg 'eng\.risk|evaluate|Rejection' crates/house/` returns nothing
(confirmed). Decisive without a probe.

---

### F3 — CRITICAL. The divergence breaker rests on an UNVERIFIED assumption about resting-order collateral, and the maker leg makes it fire ~75% of the time it rests.

`reconcile.rs:241-265`: `divergence = |real_cash − expected_cash|`,
`expected_cash = bankroll − total_at_risk` where `total_at_risk` INCLUDES reservations
(`risk.rs:250-254`, `467`). Threshold $2.00 (`reconcile.rs:26`). `real_cash` is
`GET /portfolio/balance` → `parse_balance` reads the flat `balance` field
(`kalshi.rs:1194-1199`).

The whole thing assumes Kalshi's `balance` DEBITS collateral the moment a GTC order rests.
Nothing in the vault proves this: `work/verify-house-truth.md:100-130` established fee,
resting, cancel and 404 semantics but never balance-vs-resting-order. Note 36's live-state
line does not cover it either.

Scenario if the assumption is wrong (balance = total, debited only at fill): streak posts
10 @ 40¢ = $4.00 reserved at T0+1s and rests to T0+45s. Reconcile runs every 60s
(`main.rs:397`), so a 44s reservation window collides with a reconcile pass ~73% of the
time. `real_cash − expected_cash = +$4.00 > $2.00` → **sticky HALT** requiring a manual
`nestor resume`. Every signal, all day.

Same breaker, second scenario that fires whichever way the collateral question resolves:
house's two-sided quoting eventually holds 1 YES **and** 1 NO on the same ticker. Kalshi
reports a *net* position; `parse_positions` drops net-zero rows (`kalshi.rs:1029-1031`), so
the matched pair is never adopted — but ~$0.98 of real cash left the account. Three matched
pairs ≈ $2.94 > $2.00 → permanent, self-inflicted, unexplained sticky halt of the whole bot.

**Cheapest check (probe P1 below):** on demo, `GET /portfolio/balance` → POST 1 contract
GTC resting @40¢ with `expiration_ts = now+120` → `GET /portfolio/balance` → cancel →
`GET /portfolio/balance`. Three reads, one $0.40 order. Decides F3 outright.

---

### F4 — HIGH. A failed settled-markets fetch `?`-returns BEFORE maker supervision, so a transient error can leave a bid uncancelled past its window.

`streak/strategy.rs:548` — `let raw = self.settled_for(eng, series, window_id, force).await?;`
— sits ABOVE `self.supervise_makers(...)` at `:561`. `settled_for` calls
`recent_closed` (`kalshi.rs:363-386`), which is NOT wrapped in `in_window` and therefore
uses the client's 30s timeout (`engine/src/lib.rs:28-33`).

This directly contradicts the invariant the code claims for itself at `:556-560` ("MAKER
SUPERVISION runs BEFORE the open-markets fetch on purpose: that fetch can time out, and a
timed-out pass must never be able to skip a backstop deadline"). The guard was placed
against the wrong fetch.

Scenario: at T0+30s a 429 storm (or a 30s hang) hits `recent_closed`. Note that during the
settlement-lag phase `force` is set every pass (`:547`, `:617`), so this call is uncached
and repeated ~1/s in exactly the entry window. The pass returns `Err`; `main.rs:477-493`
applies exponential backoff (`backoff_sleep`), so the next pass may be 2-8s later, and it
fails too. `supervise_makers` never runs across T0+45 → no cancel, no backstop. The 40¢
bid rests to `expiration_ts` T0+60, and Kalshi's expiry is LAZY (~2-3 min) → the bid can
fill at T0+150s, on a signal whose 60s edge is long gone — "a position we never decided to
take", the exact thing `MAKER_EXPIRY_SECS` exists to prevent (`exec.rs:66-72`). Plus the
backstop IOC (+4.3¢ EV) is forgone.

**Cheapest check:** move the call or read the code path once more — it is decidable by
inspection (done). To size the exposure, count `streak: scan error` lines in `logs/run.log`
that land inside a T0..T0+45 window.

---

### F5 — HIGH. House's orphan sweep cancels EVERY resting order on the account, including streak's live maker leg — and streak reads the resulting 404 as "it filled".

`house/strategy.rs:161-180` (`cancel_all_house_orders`) calls `resting_orders(None)` — no
ticker/series filter — and cancels every id returned. Callers: the startup sweep
(`:208-210`) and, critically, `House::halt` (`:191`), which can fire at ANY time from the
−$20 ledger stop (`:307`) or the −5¢ gap-through stop (`:621`).

Contrast: streak's own sweep is correctly series-filtered (`streak/strategy.rs:510-513`).
So the damage is one-directional, house → streak.

Scenario: HOUSE_PROBE=1 in `run`. At T0+20s a KXAPRPOTUS fill marks out −5¢ → `halt` →
`cancel_all_house_orders` cancels streak's 40¢ KXBTC15M bid. Streak's next
`supervise_one` at T0+45 issues its own cancel → **HTTP 404** → the code's documented
inference (`:1276-1289`) is "before `expiration_ts` the only way a bid leaves the book is by
FILLING, so a 404 means it filled" → `mark_gone` → `off_book = true` → **the backstop is
withheld forever for that episode** (`:1254-1263`, `:1357-1364`), the $4 reservation is held
until `expiration_ts + 240s` (`CANCEL_RETRY_GRACE_SECS`, `:75`), and at that point a false
CRITICAL alert fires ("left the book but no fill ever appeared — check positions",
`:1370-1378`).

Money: the entry is forgone entirely (maker leg gone, backstop suppressed) and $4 of daily
budget + portfolio room is frozen for ~5 minutes. The "404 ⇒ filled" inference is sound
only while nothing else can cancel our orders; house makes that false.

**Cheapest check:** inspection (done) — `resting_orders(None)` vs streak's
`.filter(|o| SERIES.iter().any(|s| o.ticker.starts_with(s)))`.

---

### F6 — HIGH. House throws away the cancel response and rotates order ids without ever reconciling fills, so real fills vanish from its ledger and its −$20 stop.

Two places drop quote state and cancel without reading truth:
- `pull_quotes` (`house/strategy.rs:375-391`): removes `st.quotes[ticker]` FIRST, then
  cancels, and the cancel result is only `eprintln!`-ed on error. `reduced_by` is never read.
- `requote` (`:395-449`): cancels the old legs (`:413-415`, result discarded with `let _ =`),
  then overwrites the entry with `booked_bid: 0, booked_ask: 0, since_ms: now` (`:427-441`),
  so the fills-delta baseline resets and the OLD order ids are never queried again
  (`detect_fills` matches on `Some(oid)`, `:524`).

The engine already has the right tool and streak uses it: `parse_cancel_reduced_by`
(`kalshi.rs:948-952`, demo-verified 2026-07-26) at `streak/strategy.rs:1300-1322`. House
does not call it anywhere.

Scenario: `/portfolio/fills` lags the matching engine by seconds (the crate's own comment,
`streak/strategy.rs:1296-1299`). House quotes at 3s cadence and re-quotes on a 1¢ mid move
(`house/signal.rs:19,94-101`) — on a 30-70¢ POTUS book that is most passes. Bid leg fills 1
contract at 12:00:01; `detect_fills` at 12:00:02 sees nothing (fills lag); the mid ticks 1¢
so `requote` cancels and replaces at 12:00:03. **That fill is never booked**: not in
`ProbeLedger.realized/fees`, not in `pending` (so no markout), not in the −$20
`hard_stop_breached` mark (`house/signal.rs:182-184`), not in `house_fill` records.

Money: (a) the −$20 hard stop computes on a ledger missing an unbounded share of the
positions it is supposed to stop; (b) metric 1 (fills per quote-hour) is systematically
understated, i.e. a false PROMOTE/KILL verdict on the probe (class C + class D). The
gate paths are worse still — `run_book` calls `pull_quotes` for `no_two_sided_book`
(`:269`), `catalyst_window` (`:280`) and `spread_lt_2c` (`:290`) all BEFORE `detect_fills`
(`:298`), so a fill that lands just before any gate closes is lost by construction, not by race.

**Cheapest check:** cross-join `data/house_probe.jsonl` `house_fill` records against a
demo `GET /portfolio/fills` for the same tickers/day — any fills row with no matching
`house_fill` line is a lost fill. Zero code changes.

---

### F7 — HIGH. Restart mid-window: `swept` is set before the sweep's network read, so a single failed read means the sweep never happens, and the re-entry then runs two live legs on one ticker.

`streak/strategy.rs:493-509`: `*swept = true` is written at `:498`, BEFORE
`eng.kalshi.resting_orders(None).await` at `:503`. On `Err` the function just returns
(`:505-508`) — and the one-shot guard is already burned, so no later pass retries it.

Scenario: crash at T0+15s with a live 40¢ bid. Restart at T0+25s; the startup
`resting_orders` GET returns 429/503/timeout → no sweep, the pre-crash bid is still live and
unsupervised (`seen` and `maker` are in-memory only). The fresh process re-detects the same
market (`seen` is empty), `enter` → `place_maker` with the SAME deterministic coid
`streak-{ticker}-m40` (`:1047`) → Kalshi 409 `order_already_exists` (empirically proven,
note 36) → `place_resting` takes the non-2xx branch (`engine/strategy.rs:446-454`) →
`RestError { may_be_resting: false }` → streak "falls back to taker" (`:1139-1159`) and
sends an IOC at 46¢, which fills.

Money outcome: we now hold a taker position AND a live 40¢ bid that can also fill. The
second fill hits `on_fill_actual`'s duplicate guard (`risk.rs:386`) and `adopt_orphan`'s
`has_open` guard (`risk.rs:442`) → real contracts invisible to the ledger, the drawdown
kill-switch and settlement. Same terminal state as F1.

Sub-finding (fires even when the sweep SUCCEEDS): that maker-path 409 is NOT classified as a
benign duplicate. The taker path does classify it (`engine/strategy.rs:627-636`); the maker
path calls `note_order_failure(Some(409))` unconditionally, and `is_retryable_status(409)`
is false (`net.rs:67-69`), so it feeds the sticky-halt breaker. Five in-window restarts →
global HALT.

**Cheapest check:** inspection (done) for the ordering; for the 409 classification, the
demo test `kalshi::tests::demo_duplicate_coid_behavior` already proves the 409 — only the
maker-path handling needs the same `benign` branch.

---

### F8 — MED-HIGH. The settleability guard plus a market-fetch failure can hold a divergence the exchange has already paid out.

`reconcile.rs:49-59` (`is_settleable`) deliberately refuses to settle a market whose
`result` is populated while `status` is still `active`/`closed` ("anomalous — wait"), and
`:117-123` `continue`s past any market whose GET failed. In both cases the position stays in
`state.open` at its entry stake, but if Kalshi has already credited the payout, `real_cash`
has jumped while `expected_cash` has not.

Scenario: 10 contracts @ 40¢ win. Exchange credits $10. `market()` GET 5xx's on this pass →
skip. Divergence = $10 − $6 booked... concretely `|real − expected| = $10.00 > $2.00` →
sticky HALT (`reconcile.rs:252-260`) with the message "state/exchange disagree", when in
fact nothing disagrees except the ordering of two reads. Note 36 records the
`closed → finalized+result (~10s) → settled-filter lags 36s+` progression, so a
result-present/status-not-yet-terminal window demonstrably exists.

**Cheapest check (probe P3):** demo — hold 1 contract to settlement, poll
`GET /markets/{t}` (status+result) and `GET /portfolio/balance` every 2s across the
transition. Establishes whether cash lands before our gate opens.

---

### F9 — MED. Orphan adoption races streak's own fill booking and re-files the position under the wrong strategy and cluster.

`reconcile.rs:204-238` runs every 60s and adopts any exchange position absent from local
state. Streak books its maker fill only after `/portfolio/fills` shows it — and the crate
itself documents that fills "lag the matching engine by seconds" (`streak/strategy.rs:1296`).
That lag is the race window, not the 1s poll interval.

Scenario: the bid fills at T0+22s; fills lags 4s; reconcile's `positions()` snapshot lands
at T0+24s → `adopt_orphan` files it as `strategy: "orphan-adopted"`, `cluster:
"orphan-{ticker}"`, `fee: 0.0` (`risk.rs:450-459`). At T0+26s streak's poll finds the fill →
`book_resting_fill` → duplicate guard refuses (`risk.rs:386`).

Money: cost basis is right (`market_exposure_dollars`, `kalshi.rs:1040-1046`) so P&L is
right, but (a) the BTC+ETH "one bet" cluster key `streak-{close_unix}`
(`streak/strategy.rs:1054`) is defeated — the second coin then sizes against an empty
cluster; (b) the maker fee is booked as $0 rather than the exchange figure; (c) every
per-strategy report joining on `strategy` loses the trade.

**Cheapest check:** after the first live maker day, grep `data/settlements.jsonl` for
`"strategy":"orphan-adopted"` on a KXBTC15M/KXETH15M ticker — every hit is this race.

---

### F10 — MED. `place_resting`'s ambiguous-failure path releases the reservation while the order may be live.

`engine/strategy.rs:426-444`: on a network error or the 5s timeout it calls
`release_reservation(reserve_key)` and THEN returns `may_be_resting: true`. Streak correctly
stands down on that ticker (`streak/strategy.rs:1164-1185`), but the $4 that may be committed
on the exchange is no longer reserved, so the other coin's leg in the same window sizes
against money already spent. Bounded at $4 today (cluster cap $15.90 at bankroll $106.03),
but it is the reservation invariant leaking exactly where it matters most.

**Cheapest check:** inspection (done).

---

### F11 — MED. `taker_fee` ceils to a whole cent; the demo proved the exchange ceils to $0.0001.

`risk.rs:127-131`: `(raw * 100.0).ceil() / 100.0`, with the comment claiming
"ceil ... once per ORDER (Kalshi's actual billing)". `work/verify-house-truth.md:104-106`
says the opposite: **"rounding up to $0.0001 / one-hundredth of a cent, NOT up to a whole
cent as the old fee tables state"**, exact on 5/5 demo fills.

Money: the formula is the FALLBACK charged to bankroll whenever `fee_cost` is absent
(`risk.rs:396-400`), and it is written into every participation record as `fee_cents`. A
1-lot backstop at 46¢: true $0.0174, ours $0.02 — 15% overstated. A 10-lot at 40¢: true
$0.1680, ours $0.17. Always conservative (never under-charges), so it drifts the drawdown
kill-switch toward a halt that never happened — the same failure mode the maker-fee fix
(`risk.rs:362-368`) was written to remove.

**Cheapest check:** already decided by `verify-house-truth.md`; this is a code-vs-vault
contradiction, no probe needed.

---

### F12 — MED. House never flattens, so its positions accumulate and are adopted into streak's caps until a months-away settlement.

`house/strategy.rs:584-588` implements "own fill → flatten" as *pull the quote*; there is no
closing trade anywhere in the crate. Each fill is a live position in KXAPRPOTUS (April) or
KXCPIYOY. Reconcile adopts each as an orphan (`reconcile.rs:211-238`), which does
`day_spent += count * entry / 100` (`risk.rs:449`) against the shared $60 daily budget and
adds to `total_at_risk` against the 50% portfolio cap ($53.02 at bankroll $106.03). Those
positions cannot settle until the underlying event resolves, so the consumption is
effectively permanent. Streak's flat sizing (`risk.rs:308-315`) is squeezed by a probe that
is not supposed to touch it.

**Cheapest check:** after any HOUSE_PROBE=1 live day, read `data/state.json` `day_spent` and
count `open` entries with `strategy: "orphan-adopted"` on KXAPRPOTUS/KXCPIYOY tickers.

---

### F13 — MED. The one-episode-per-market dedupe is consumed before risk evaluation, so a transient cap rejection forfeits the window permanently.

`streak/strategy.rs:963` — `if !self.first_time(cur.ticker.clone()) { return Ok(()) }` runs
before any order attempt. If `place_resting` then returns `Rejected(ClusterCapHit)` at
T0+2s because the other coin's leg is reserved (`:1130-1136`), the record is written and the
market is burned — even though the other leg is cancelled at T0+45 and the room returns
with 15s of window left. Missed EV only, but it is a silent one.

---

## VERIFIED CLEAN (and how)

| Property | Evidence |
|---|---|
| Maker and taker coid namespaces cannot collide | maker `streak-{ticker}-m40` (`streak/strategy.rs:1047`) vs taker `streak-{ticker}[-r{n}]` (`engine/strategy.rs:152-158`) — disjoint by construction, retries get distinct ids |
| Backstop size is re-derived at its own price, not inherited from 40¢ | `drop_leg` releases the reservation (`streak/strategy.rs:1325`) BEFORE `taker_leg` → `execute_attempt` → `evaluate` sizes fresh (`risk.rs:307-333`); 8 @ 46¢ = $3.68 ≤ $4 cap; asserted in `exec.rs:226-240` |
| The whole IOC ladder lands inside the entry window | backstop T0+45 (`exec.rs:63`) + 3 × 2s (`exec.rs:87,90`) → last attempt ~T0+51; the loop's own guard breaks at `ttc < MIN_TTC_SECS + 3` = T0+57 (`streak/strategy.rs:1631-1634`, `signal.rs:90,92`: WINDOW 900 / MIN_TTC 840); expiry T0+60 |
| Backstop is never fired on top of a fill | cancel response `reduced_by` is truth (`streak/strategy.rs:1300-1322`, `kalshi.rs:948`); a 404 → `mark_gone`, never an IOC (`:1282-1289`); partial `reduced_by` → withheld (`:1306-1313`) |
| The `off_book` state can never send an order | `supervise_one:1258-1263` routes off-book legs to `mark_gone` before the cancel/backstop branch; the reservation is held until `expiration_ts + 240s` (`:1357-1364`) |
| Streak's startup sweep does not touch other sleeves' orders | series-filtered at `streak/strategy.rs:510-513` (contrast F5) |
| Reservations genuinely bound cluster, daily-flat and portfolio caps, and `expected_cash` | `risk.rs:231-283`, `301-328`, `467`; tests `reservation_consumes_cluster_room_until_released`, `reserved_flat_dollars_eat_the_daily_budget` (`risk.rs:611-671`) |
| Settlement money math is internally consistent with real cash flows | hand-traced 10 @ 40¢ win: fill → bankroll −fee, expected = initial −fee −4 = real; settle → bankroll +$6, expected = initial −fee +6 = real (`risk.rs:493-547`) |
| Cancel-on-flip cannot loop forever after a 404 | `abandon_leg:1427-1435` routes to `mark_gone` and stops re-cancelling |
| Volbook's retry cannot double-book a partial | the loop breaks on anything that is not `Missed` (`volbook/strategy.rs:325`), so only a ZERO-fill IOC retries, under a fresh `evaluate` |
| Volbook restart re-entry cannot double-book | same coid → 409 → `recover_lost_ack` with `benign=true`; the prior fill is excluded by the `since_ms − 2000` window (`kalshi.rs:870`) → `OrderError`, breaker not fed (`engine/strategy.rs:809-821`) |
| Volbook's cluster cap holds with three series racing | candidates are pooled and entered sequentially, ranked by `gap_pp` (`volbook/strategy.rs:127-136`); each IOC resolves synchronously inside `exec_lock` so the next `evaluate` sees the fill |
| The duplicate-fill idempotency guard does prevent double-booking | `risk.rs:386-393` — it works; F1/F7 are about what it does to fills it refuses, not about it failing |

---

## PROPOSED PROBES (design only — NOT run, per the ground rules)

**P1 — resting-order collateral vs `/portfolio/balance`** (decides F3, highest value).
Demo, one $0.40 order:
1. `GET /trade-api/v2/portfolio/balance` → `b0`
2. `POST /trade-api/v2/portfolio/events/orders` — 1 contract, YES bid @ $0.40,
   `time_in_force: good_till_canceled`, `expiration_ts: now+120`,
   `self_trade_prevention_type: taker_at_cross`, on a market whose ask is ≥ 0.60 so it
   cannot fill
3. `GET .../balance` → `b1`
4. `DELETE .../portfolio/events/orders/{id}` → `GET .../balance` → `b2`
Pass = `b0 − b1 == 40` and `b2 == b0`. Fail (`b1 == b0`) means the divergence breaker will
false-HALT on every live maker leg and the reservation must be excluded from `expected_cash`.

**P2 — partial-fill residual** (decides F1). Demo, on a live KXBTC15M window: rest 10
contracts 1¢ inside the best ask (the `verify-house-truth.md` setup that filled 5/5 in
4.7-45.9s), and the moment `/portfolio/fills` shows a partial, `GET
/portfolio/orders?status=resting&ticker=...`. Pass = the order is gone; fail (expected) =
`remaining_count > 0`, proving the residual survives the supervisor dropping the leg.

**P3 — settlement cash-vs-status timing** (decides F8). Demo, hold 1 contract into a 15-min
settlement; poll `GET /markets/{ticker}` (status, result) and `GET /portfolio/balance` every
2s from T−10s to T+120s. Records the exact ordering of (result populated, status
determined/finalized, cash credited) and thus whether `is_settleable`'s conservative gate
opens a divergence window.

---

## SUGGESTED FIX ORDER (no code changed in this lane)

1. F1 — cancel the residual in `settle_maker_fill` before `drop_leg` (or keep the leg alive
   for the remainder and refuse only the *booking*).
2. F2 — house must check `eng.risk` halted state each pass, or route through the risk layer.
3. F3 — run P1 first; the answer decides whether `expected_cash` may include reservations.
4. F4 — move `supervise_makers` above `settled_for`, or make `settled_for` non-fatal.
5. F5 — filter house's sweep to its own series, exactly as streak does.
6. F6 — house must read `reduced_by` and drain fills before discarding an order id.
7. F7 — set `swept` only after a SUCCESSFUL `resting_orders` read; classify the maker-path
   409 as benign.
