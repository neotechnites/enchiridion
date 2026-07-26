# Lane CONSTANTS — nestor deep review (2026-07-26)

Charter: `work/steer-nestor-deep-review.md` §Lane charters #1 (lesson class A — underived
defaults). Repo read only; nothing built, nothing run against prod keys.

**Scope covered:** all 6 crates (engine, streak, volbook, house, lock, weather) + nestor_bin
+ `nestor.toml` + `data/volbook_calib.json`. 13,278 LOC, 517 raw numeric literals in
non-test code, curated to **100 behavioural constants** (below).

**Deployment context (matters for every lane):** the running process (pid 72360,
`./target/release/nestor run`) predates the current source — `logs/run.log` still emits
`price_above_gate` skips, a `Skip` variant that no longer exists, and the last live order
went out at `limit_placed: 44` (the superseded gate), not the 46¢ ceiling.
`data/streak_week1.jsonl` contains **zero** `streak_maker_rest` records. **The entire
rest-at-40 / backstop-at-46 constant set has never executed live.** The on-disk binary
(built 12:27) does contain it; the next restart arms it.

---

## Findings (most severe first)

### F1 — CRITICAL. House bypasses the risk layer entirely, and its resting quotes trip the $2 divergence breaker, which halts streak and volbook too.

`crates/house/src/strategy.rs:462-464` — `place_leg` calls
`eng.kalshi.place_resting_limit_raw(...)` **directly**. It never touches `risk.evaluate`,
never `reserve`s, never books a fill into `RiskManager`. Contrast streak's maker leg
(`crates/engine/src/strategy.rs:364-384`), which reserves through risk so `expected_cash`
stays honest.

`crates/engine/src/reconcile.rs:26,252-260` — `DIVERGENCE_THRESHOLD_USD = 2.0`; on breach
the reconcile task calls `eng.risk.lock().halt()` — a **persisted, sticky** halt on the one
shared `RiskManager`.

**Failure scenario.** Operator sets `HOUSE_PROBE=1 HOUSE_SIZE=2` (explicitly inside the
`clamp(1,5)` at `house/strategy.rs:144`). `quote_legs` (`house/signal.rs:60-66`) always
posts `bid = mid−1` and `ask = 99−mid`, so **each pair costs exactly 98¢ × size regardless
of mid**. Two books (KXAPRPOTUS, KXCPIYOY) × 2 contracts × 98¢ = **$3.92** of cash Kalshi
reserves against resting orders. Within ≤60s the reconcile task reads `balance_cents` =
$102.36 against `expected_cash` = $106.03 → Δ$3.67 > $2.00 → **HALT**. Streak and volbook
are dead until an operator runs `resume`. At `HOUSE_SIZE=1` the margin is $1.96 against a
$2.00 threshold *and* a standing +$0.25 baseline offset (see below) — four cents of
headroom.

Second half of the same defect: any house **fill** is real money that never enters
`RiskManager`. Bankroll, drawdown, `day_spent`, the daily-loss limit and the divergence
breaker are all computed as if it never happened. The `-$20` probe stop lives in a separate
in-memory `ProbeLedger` (F6).

Current tape shows a **persistent Δ$0.25** (`logs/run.log`, every pass: `real $106.28 vs
expected $106.03`) with zero open positions and zero resting orders — i.e. 12.5% of the
threshold is already consumed by an unexplained standing offset that nobody has reconciled.

**Cheapest decisive check.** Demo probe P1 below: place one 1-contract GTC resting limit,
read `/portfolio/balance` before / while resting / after cancel. If resting orders debit
available balance (they do on Kalshi), the arithmetic above is decisive with no further
work. Cost: one demo order.

---

### F2 — HIGH. `exec_lock` is held across an unbounded network call; a volbook IOC can delay streak's maker post by up to 30s past the `MIN_REST_SECS` decision that authorised it.

`crates/engine/src/strategy.rs:307` takes `exec_lock` for the whole
`evaluate → place → verify → on_fill` sequence. Inside that critical section,
`strategy.rs:674` does `self.kalshi.fills(&order.ticker).await` — **not** wrapped in
`in_window`. `crates/engine/src/kalshi.rs` sets no per-call timeout, so this inherits the
shared client's **30s** (`crates/engine/src/lib.rs:30`). Its result is used for nothing but
an `eprintln!` recon-mismatch line (`strategy.rs:685`).

`crates/streak/src/strategy.rs:992` evaluates `exec::maker_eligible(now, t0)` **before**
calling `place_maker` → `place_resting`, which then blocks on `exec_lock`. The eligibility
test is never re-checked after the lock is acquired.

**Failure scenario.** 13:00 ET: volbook's T-3h window for a metal daily overlaps a streak
4-streak at the 15-min boundary. Volbook's `execute_attempt` places its IOC, then its recon
fills GET stalls 28s behind a Kalshi 5xx. Streak decided `maker_eligible` at T0+1 (44s of
runway) but acquires the lock at T0+29. It posts a 40¢ bid with 16s of runway — or, on a
derive-fourth entry discovered at T0+12, with **4s** of runway, silently violating
`MIN_REST_SECS = 5` and paying two round-trips (create + cancel at T0+45) for a bid that
never saw a full 1 Hz poll. Worse tail: the lock wait consumes the entry window outright
and `place_resting` posts a bid whose `expiration_ts = T0+60` is already ~expired.

**Cheapest decisive check.** Zero-cost code read confirms the structure. To size it: log
`Instant::now()` delta around the `exec_lock.lock().await` in `place_resting` and read one
day of tape. To kill it: wrap the recon fills GET in `in_window` (it is already best-effort)
and re-assert `maker_eligible` after the lock.

---

### F3 — HIGH. The backstop's cancel is not deadline-bounded, and the first backstop IOC has no entry-window guard.

`crates/streak/src/strategy.rs:1271` — `eng.kalshi.cancel_order(&leg.order_id).await`, no
`in_window`, 30s client budget.
`crates/streak/src/strategy.rs:1332` → `taker_leg` → `strategy.rs:1624-1626` places
**attempt 1 unconditionally**. The window guard (`strategy.rs:1631-1634`,
`ttc < MIN_TTC_SECS + 3 → break`) only runs *between* attempts.

**Failure scenario.** The 45s cancel round-trip stalls 30s. It returns at T0+75 with
`reduced_by == count` (nothing filled → "backstop is safe"), and an IOC at the 46¢ ceiling
is sent at **T0+75** — 15s outside the 60s window the 52% win rate, the 24% dip probability
and the 21%-at-t≈45-55s taker fill rate were all measured on. The ladder guard then breaks
after attempt 1 (ttc 825 < 843), so exactly one un-modelled $3.68 position is opened on a
regime the policy never priced. `MAKER_EXPIRY_SECS = 60` does not protect this — it bounds
the *maker* leg, not the taker leg fired after it.

Note the ladder arithmetic itself is **clean and measured** (see Verified Clean) — the
exposure is entirely in the unbounded cancel that precedes it.

**Cheapest decisive check.** One line, no measurement needed: `if leg.close_unix - now <
signal::MIN_TTC_SECS + 3 { drop_leg; log no_backstop_out_of_window; return }` before the
`taker_leg` call, plus `in_window(...)` on the cancel. To size the risk instead: demo probe
P2 (cancel RTT distribution).

---

### F4 — HIGH. Volbook gets 2 attempts spanning 2 seconds of a 3600-second entry window, then the rung is dead for the life of the process.

`crates/volbook/src/strategy.rs:43-44` (`MAX_ENTRY_ATTEMPTS = 2`, `RETRY_SPACING_MS = 1000`)
against `crates/volbook/src/strategy.rs:270` — `if !self.first_time(m.ticker.clone()) {
return; }`, checked **before** the attempt loop and never re-armed. The `seen` set is never
cleared. Entry window is `entry_ttc_lo_secs = 9000 .. entry_ttc_hi_secs = 12600` = **3600s**
(`data/volbook_calib.json`), scanned every 60s (`nestor_bin/src/main.rs:427`) — 60 passes
available, 1 used.

The stated derivation ("the wing NO books carry deep resting size, so a cross at/under the
ceiling normally fills at once; a single retry covers a transient partial without chasing")
is also **contradicted by the code**: the loop breaks on any non-`Missed` outcome, so a
partial fill is *never* retried. The retry only covers a total miss.

**Failure scenario.** At T-3h a market maker pulls the NO ask on the KXGOLDD 3400 rung for
five seconds. Both IOCs at the 94¢ ceiling return `fill_count 0` → `Missed`. The ticker is
now permanently in `seen`; the remaining 59 passes never look at it again. Because the pool
is **ranked by `gap_pp` descending** (`strategy.rs:127-132`), the rung most likely to be
lost this way is the day's highest-edge one. The record reads `reject_reason:
missed_fill` — indistinguishable in the ledger from "no edge existed".

**Cheapest decisive check.** `data/volbook.jsonl` does not exist (volbook has never
produced a record), so there is nothing on disk to test. Decisive check is probe P3: one
paper Mon-Wed T-3h hour logging the NO ask and resting NO size at the ceiling for every
qualifying rung once per pass; the fraction of rungs at-or-below the ceiling at pass 1 but
not pass 2 is the EV being forgone.

---

### F5 — MED→HIGH. `MAX_LIVE_BANKROLL = 100.0` is already below the live account ($106.03). A fresh-state restart is now impossible, and any attempt halts on the first reconcile pass.

`crates/engine/src/config.rs:28` and the range check at `config.rs:71-76`.
`data/state.json`: `{"bankroll": 106.03, "peak": 106.03, ...}`.
`nestor.toml:9`: `bankroll = 100.0`.

Today the guard is inert — it only validates the *seed*, and `load_or_init`
(`risk.rs:181-192`) loads the existing state file instead. So the cap bounds nothing.

**Failure scenario.** `data/state.json` is lost (disk incident, a stray `rm`, a bad
`NESTOR_STATE_PATH`). The operator restarts with `--fresh-state` and the true balance:
`NESTOR_BANKROLL=106.03` → `resolve_bankroll` **bails** ("out of range (0, $100.00]"). To
start at all they must seed ≤$100, which sets `peak = 100.00` against a real $106.28 —
whereupon the first reconcile pass computes `expected_cash = 100.00` vs `real_cash =
106.28`, Δ$6.28 > $2.00, and **halts immediately**. The bot cannot be recovered from a lost
ledger without hand-editing a constant.

**Cheapest decisive check.** Read-only, 30 seconds, no keys (the `resume` path returns
before the Kalshi client is built): `NESTOR_ENV=live NESTOR_BANKROLL=106.03
NESTOR_STATE_PATH=/tmp/x.json ./target/release/nestor resume` → observe the bail.

---

### F6 — MED. The house probe's −$20 hard stop and its whole ledger are in-memory only; a restart re-arms the full loss budget.

`crates/house/src/strategy.rs:120,149` — `state: Mutex<HouseState>` built from
`HouseState::default()`, no `StateStore`, nothing persisted.
`crates/house/src/signal.rs:31` — `HARD_STOP_CENTS = -2000.0` (−$20, i.e. **19% of the
$106.03 account**), enforced only against that volatile ledger.

**Failure scenario.** The probe runs to −$18 over a morning, the process is restarted for a
deploy, and the ledger resets to zero — a fresh −$20 of headroom on an account that has no
other record of the first $18 (F1: house fills never reach `RiskManager`). Two restarts in
a day = −$60 with every kill-switch reading "0.0% drawdown, halted=false", exactly as
`logs/run.log` reports today.

**Cheapest decisive check.** Code read only; already confirmed. No probe needed.

---

### F7 — MED. The volbook bucket carrying 42% of the corpus prices its ceiling at 97¢, one cent under the risk layer's hard reject at 98¢.

`crates/volbook/src/signal.rs:103-107` (`willingness_to_pay_cents`) × the `[0.05,0.10)`
bucket in `data/volbook_calib.json` (`n=142`, `touch_obs = 0.0`, shrunk `touch = 0.0083`)
→ ceiling **97**. `crates/engine/src/risk.rs:294`: `if s.limit_cents <= 2 || s.limit_cents
>= 98 { return Err(Rejection::PriceOutOfBand) }` — a band derived for 15-min crypto
markets, never re-derived for a deep-wing NO buy.

**Failure scenario.** `scripts/volbook_calib.py` is re-run with more data (or `shrink_k`
is retuned from 20) and the lowest bucket's shrunk touch lands below 0.0052 → ceiling 98 →
**every entry in the strategy's largest and richest bucket is rejected `risk:PriceOutOfBand`**.
It is logged as a risk rejection, so it reads as a cap problem, not a calibration event, and
the sleeve appears to simply stop finding edge.

**Cheapest decisive check.** Already run (offline, over `data/volbook_calib.json`): all six
metal buckets → ceilings 97/95/90/81/81/68. Add an assertion in `Calib::load` that every
bucket ceiling is in `[3,97]` and fail loudly on load rather than silently at order time.

---

### F8 — MED. Volbook sizes paper at the observed ask and live at the ceiling — the same decision books a different number of contracts in the two modes.

`crates/volbook/src/strategy.rs:305-309`. Risk sizes with `contracts_for(stake, limit)`
(`sizing.rs:8`), so at a $4 flat stake: live limit 94 → **4 contracts**; paper limit 80 →
**5 contracts**. Paper P&L — the number that will decide whether the sleeve gets sized — is
systematically ~25% larger than live on the same fill. Streak has the same mode split
(`exec.rs:177-185`) but there it is deliberate and documented for *price* honesty; the
*size* consequence is undocumented in both.

**Cheapest decisive check.** The arithmetic above. Cheapest fix: log both counts in the
paper record so the forward comparison is reconstructable.

---

### F9 — MED. `CANCEL_RETRY_GRACE_SECS = 240` and the `off_book` fills poll were sized for "every pass", but the poll cadence drops to 12s for most of that grace.

`crates/streak/src/strategy.rs:75` (grace 240s) against `strategy.rs:90-100`
(`next_poll_delay`: 1s only while `into_window ∈ [0,75) ∪ [825,900)`, else
`(900−into_window).clamp(1,12)`).

A leg that fails its cancel at T0+45, or goes `off_book` on a 404, is worked at 1 Hz only
until T0+75 — then at 12s intervals until the grace expires at T0+300. The comments at
`strategy.rs:1351-1356` and `1381-1383` ("keep polling", "retried next pass") assume the
1 Hz cadence. Consequence: a filled-but-not-yet-visible maker leg's booking, and the loud
give-up alert at grace expiry, can each be up to 12s late; and the number of cancel retries
inside the 240s grace is ~22, not ~240.

**Cheapest decisive check.** Simulate `next_poll_delay` over one window offline (pure
function, already unit-tested) and count passes in `[T0+75, T0+300]`. No network.

---

### F10 — MED. `recent_closed(series, 3*3600, 12)` is a zero-slack coupling, and its failure mode is un-alarmed.

`crates/streak/src/strategy.rs:429`. 3h ÷ 900s = exactly 12 windows, limit exactly 12. The
detector needs 4 settled *plus* enough history to survive the previous window not having
settled. If Kalshi ever returns the still-open market inside that window, paginates
differently, or lists a second market per 15-min close, `settled_windows()` can yield fewer
than 4 and **every scan returns `Skip::InsufficientHistory` forever**.

`crates/streak/src/strategy.rs:136` maps `InsufficientHistory` (and `NoStreak`,
`NotConsecutive`) to `None` in `skip_kind`, so it is **excluded from the repeat-skip alarm
and never logged**. A total signal outage would be indistinguishable from a quiet market.

**Cheapest decisive check.** Count distinct `close_unix` values in one `recent_closed`
response (a single unauthenticated GET) and assert ≥ 5 settled; alarm below it.

---

## Inventory

**100 behavioural constants** (named `const`s + config fields + inline cadences, timeouts,
retry counts, thresholds, clamps and gate literals; excludes array indices, `0/1/2/100`
price arithmetic, and format-string widths).

| Group | Count | Derived | Judgment | UNDERIVED |
|---|---|---|---|---|
| streak (`exec.rs`, `signal.rs`, `derive.rs`, `strategy.rs`) | 28 | 15 | 8 | 5 |
| volbook (`strategy.rs`, `signal.rs`, `calib.json`) | 13 | 7 | 3 | 3 |
| house (`signal.rs`, `strategy.rs`, `report.rs`) | 17 | 0 | 7 | 10 |
| engine (`strategy.rs`, `risk.rs`, `net.rs`, `ws.rs`, `reconcile.rs`, `kalshi.rs`, `lib.rs`, `config.rs`) | 25 | 5 | 10 | 10 |
| risk config (`nestor.toml` + `RiskConfig::default`) | 8 | 2 | 4 | 2 |
| parked (lock, weather, calibrate) | 9 | 5 | 2 | 2 |
| **Total** | **100** | **34** | **34** | **32** |

### DERIVED (34) — evidence cited in-code or in the vault
`MAKER_PRICE_CENTS 40`, `TAKER_CEILING_CENTS 46`, `TAKER_CEILING_MAX 48`,
`TAKER_CEILING_MIN 40`, `BACKSTOP_AT_SECS 45`, `MAKER_EXPIRY_SECS 60`, `MIN_REST_SECS 5`,
`MAX_ENTRY_ATTEMPTS 4`, `RETRY_SPACING_MS 2000` (all `streak/exec.rs`, cited to
`verify-streak-execution` + note 39 + `verify-streak-retry`); `WINDOW_SECS 900`,
`MIN_TTC_SECS 840`, streak length 4 (`streak/signal.rs`, redirect 2026-07-23);
`AVG_WINDOW_SECS 60`, `MIN_SAMPLES 40`, `MIN_SPAN_SECS 50` (`streak/derive.rs`, Kalshi BRTI
spec + 1 Hz sampling); volbook `wing_lo 0.05`, `wing_hi 0.35`, `ttc_lo 9000`, `ttc_hi 12600`,
`weekday_gate [Mon,Tue,Wed]`, 6 bucket edges, `shrink_k 20` (corpus, `verify-b9-widened`);
taker-fee coefficient `0.07` + ceil-per-order (`risk.rs:127`, exchange spec);
`FAST_WINDOW_SECS 75`, `SAMPLE_WINDOW_SECS 75`, `SPOT_RETENTION_SECS 150` (settlement-lag
measurement); ws bids-only `100 − best_opposite_bid` convention; `is_settleable`
determined/finalized set (item 6, demo-verified); coid `-r{n}` suffix scheme
(demo `order_already_exists` 409); `PAPER_DEFAULT_BANKROLL 1000`; lock `z_min 4.0`,
`price_lo/hi 93/97`, `CHECKPOINTS[8]`, `TRADEABLE_MAE_MAX 2.0`, tradeable-6 city list.

### JUDGMENT (34) — reasoned in a comment, not measured
`DERIVE_MARGIN 0.0005` (comment explicitly says "deliberately conservative STARTING value;
will be CALIBRATED" — treat as a live TODO), `IN_WINDOW_TIMEOUT 5s`, `flat_usd 4.0`,
`fraction 0.05`, `cluster_cap_frac 0.15`, `daily_loss_limit_frac 0.15`, `MAX_SETTLED 1000`,
`WORST_CASE_ENTRY_CENTS 99`, `REQUOTE_MID_MOVE_CENTS 1`, `GAP_THROUGH_METRIC_CENTS −3`,
`GAP_THROUGH_STOP_CENTS −5`, `MARKOUT_HORIZON_SECS 60`, house quote offset ±1 and
`clamp(3,97)`, `DEFAULT_SIZE 1`, volbook `margin_cents 2.0`, volbook `RETRY_SPACING_MS
1000`, `RECONCILE_TICK 1000ms`, net backoff `2^n cap 60`, ws `backoff 3·2^n cap 60`,
`compress loop 3600s`, `reconcile loop 60s`, `bias 1.5` placeholder, weather month table,
and the remainder of the parked-sleeve literals.

### UNDERIVED (32) — no derivation, or a derivation the arithmetic contradicts
Flagged with the failure each invites; the ten most consequential are F1-F10 above. Full list:

| Constant | Location | Failure it invites |
|---|---|---|
| `DIVERGENCE_THRESHOLD_USD 2.0` | `reconcile.rs:26` | F1 — comment claims it "absorbs sub-cent fee rounding"; it does not absorb house's resting-order cash reservation, and $0.25 of it is already consumed by an unexplained standing offset |
| house `size` clamp `1..5` | `house/strategy.rs:144` | F1 — size ≥2 guarantees a global halt |
| `HARD_STOP_CENTS −2000` | `house/signal.rs:31` | F6 — 19% of bankroll, in-memory only, re-arms on restart |
| `ORDER_TTL_SECS 75` | `house/signal.rs:13` | "load-bearing safety property", but Kalshi expiry is lazy (~2-3 min, demo-measured) — the real unsupervised window after a failed `pull_quotes` cancel is ~3 min, not 75s. `pull_quotes` removes the quote from state *before* attempting the cancel (`house/strategy.rs:376-390`), so a failed cancel orphans a leg with no retained order_id and no fill detection |
| `REQUOTE_STALE_SECS 60` | `house/signal.rs:22` | no derivation; 60 < 75 TTL is coherent but arbitrary |
| `MIN_SPREAD_CENTS 2` | `house/signal.rs:15` | "at 1¢ the edge collapses" — unmeasured; the probe exists to measure exactly this |
| `CATALYST_HALF_WIDTH_SECS 900` | `house/signal.rs:17` | protocol assertion; and `HOUSE_CATALYST_TS` defaults to **empty**, so the gate is off unless an operator remembers to set it |
| potus band `[30,70]`, cpi band `[10,90]` | `house/strategy.rs:66-77` | vehicle selection unvalidated; `series` field is annotated "UNDERIVED — confirm against live /markets" in the source itself |
| house loop `3s` | `main.rs:284,449` | 3s vs a 60s requote trigger = 20 no-op passes per requote, each a markets + orderbook + fills GET |
| report promote threshold `+0.6¢` | `house/report.rs:130` | the promote/kill number, asserted not derived |
| `MAX_LIVE_BANKROLL 100.0` | `config.rs:28` | F5 |
| `MAX_CONSEC_ERRORS 5`, `MAX_CONSEC_AUTH_FAILS 5` | `strategy.rs:20,23` | no derivation; 5 signed failures at a 1 Hz cadence is ~5s to a sticky halt |
| `BALANCE_CACHE_TTL 5s` | `strategy.rs:29` | a stale-by-5s balance gates the affordability check; fail-open on read failure |
| client `timeout 30s` / `connect 10s` | `lib.rs:30-31` | F2, F3 — half a 15-min window, and it is the *only* bound on `cancel_order`, `fills`, `orderbook`, `positions`, `balance_cents` |
| clock-skew alert `30s` | `reconcile.rs:70` | Kalshi signature windows are tighter than 30s; a 20s skew 401s everything without alerting |
| ws `WANT_TTL_SECS 300` | `ws.rs:44` | 300s > a 900s market life is fine, but 300s of subscription slack across 2 series is untested against the ws subscription limit |
| fills timestamp grace `2_000ms` | `kalshi.rs:870` | a 2s backward window on fill matching; with retry coids it can attribute an earlier attempt's fill |
| page limits `1000` / `200` / `200` | `kalshi.rs:339,523,625` | silent truncation; `resting_orders` at 200 bounds the orphan sweep |
| price band `<=2 \|\| >=98` | `risk.rs:294` | F7 |
| `max_portfolio_frac 0.50` | `nestor.toml:18` | $53 on $106; never binds before the cluster cap at current sizing, so it is untested |
| `daily_budget_usd 60.0` | `nestor.toml:15` | $60 > the $53 portfolio cap, so it can only bind cumulatively; measured signal rate is 3-5/day × $4 = $12-20, so it has never bound (see Verified Clean) |
| `max_drawdown_frac 0.30` | `nestor.toml:16` | $31.81 of loss to halt on a $106 account, and it is **only evaluated inside `settle()`** (`risk.rs:538`) — open losing positions never trip it |
| `CANCEL_RETRY_GRACE_SECS 240` | `streak/strategy.rs:75` | F9 |
| `PREV_NOT_SETTLED_ALARM 2` / `ANY_REASON_ALARM 5` | `streak/strategy.rs:128-129` | thresholds counted per *pass*, not per second — at 1 Hz they fire ~2s into any stall; observed 20-23/day in `logs/run.log`, tolerable today but purely by luck of the derive-fourth reset path |
| lazy poll `clamp(1,12)` | `streak/strategy.rs:95` | F9 |
| `recent_closed(3*3600, 12)` | `streak/strategy.rs:429` | F10 |
| volbook `MAX_ENTRY_ATTEMPTS 2` | `volbook/strategy.rs:43` | F4 |
| volbook loop `60s` | `main.rs:427` | 60 passes of a 3600s window, only the first of which can ever enter a given rung (F4) |
| volbook paper-vs-live limit | `volbook/strategy.rs:305-309` | F8 |
| `PREFIX`/`WS_PATH`/endpoint hosts | `kalshi.rs:26,29` | REALITY-lane |

---

## Verified clean (with how)

1. **Backstop reservation release.** `streak/strategy.rs:1325` calls `drop_leg` (which
   releases the cap reservation, `strategy.rs:1450-1456`) **before** `taker_leg` re-sizes
   through `evaluate` at the ceiling. `exec.rs:226-240` asserts the arithmetic: $4 flat →
   10 contracts @ 40¢ ($4.00) for the maker leg, **8 contracts @ 46¢ ($3.68)** for the
   backstop — under budget, never over. The charter's "does the cap release the difference"
   question resolves cleanly: the reservation is released in full and the backstop is sized
   fresh. **Verified by reading the release ordering + the existing unit test.**

2. **The 4×2s ladder does land in-window — measured, not asserted.** `exec.rs:203-212` only
   tests the idealised `45 + 6 + 1 < 60`. Against the live tape
   (`data/streak_week1.jsonl`, three records with `attempts: 4`): `ts_signal → final
   ts_submit` = **7.34s, 7.86s, 7.61s**. So from `BACKSTOP_AT_SECS = 45` the last IOC leaves
   at ~**T0+52.9**, inside both the `MIN_TTC_SECS + 3` guard (which bites at T0+57) and
   `MAKER_EXPIRY_SECS = 60`. **The charter's question — "does the last retry actually land
   in-window?" — is YES, with three live measurements.** The exposure is F3's unbounded
   cancel *before* the ladder, not the ladder.

3. **`STREAK_CEILING` clamp.** `exec.rs:128-132` + tests: `[40,48]`, garbage → 46, never
   above the researcher's ceiling, shading down allowed. Unit-tested, correct.

4. **Streak cluster arithmetic.** Both coins share `cluster = streak-{close_unix}`
   (`strategy.rs:1054,1618`), so simultaneous BTC+ETH = 2 × $4 = **$8** against
   `0.15 × $106.03 = $15.90` of cluster room. Two legs fit; a third would be truncated by
   `stake.min(cluster_room)`, not silently dropped.

5. **House quote-leg arithmetic.** `quote_legs(mid)` always sums to exactly 98¢
   (`(mid−1) + (99−mid)`), and `clamp(3,97)` keeps both legs inside the risk band for every
   mid in `[3,99]`. Unit-tested at `house/signal.rs:232-250`. Arithmetically correct — it is
   the *bypass* of `evaluate` (F1) that makes the clamp moot.

6. **`next_poll_delay` never oversleeps a boundary.** `(900 − into_window).clamp(1,12)`
   caps the lazy sleep at the distance to the next boundary. Pure and unit-tested.

7. **Backoff caps.** `net::backoff_delay_secs` (2,4,8,16,32,60,60…) and `ws::backoff_secs`
   (3,6,12,24,48,60…) both saturate without overflow; `backoff_sleep` takes the max of
   base / exponential / server `Retry-After`. Unit-tested.

8. **Daily budget vs. realised signal rate.** `daily_budget_usd = 60` against the measured
   streak signal rate from `data/streak_week1.jsonl`: **1, 5, 4, 2 signals/day** over
   07-23…07-26 (12 total, both coins). At $4 flat that is $4-20/day against a $60 budget —
   the budget has never bound and cannot produce the time-of-day selection bias I went
   looking for. **Hypothesis tested and killed on-disk.**

9. **Maker/taker coid namespaces do not collide.** Maker uses
   `streak-{ticker}-m40` (`strategy.rs:1047`); taker uses `streak-{ticker}[-r{n}]`
   (`engine/strategy.rs:152-158`). No duplicate-coid 409 between a maker leg and its own
   backstop on the same market.

---

## Proposed probes (design only — NOT run, per the ground rules)

**P1 — Does a resting order debit `/portfolio/balance`? (decides F1, and re-baselines the
$0.25 standing offset.)** Demo account. Read `balance_cents`. Place ONE `good_till_canceled`
resting limit BUY, 1 contract @ 20¢, `expiration_ts = now + 30`, on any liquid demo market
where 20¢ is far from the ask (so it rests, not crosses). Read `balance_cents` again ~2s
later. Cancel. Read a third time. **Deliverable:** the three balances. If balance₂ =
balance₁ − 20¢, F1's arithmetic is confirmed and `DIVERGENCE_THRESHOLD_USD` must either
account for house's resting notional or house must route through `risk.reserve`. Cost: one
demo order, ~40 seconds.

**P2 — Cancel round-trip distribution (sizes F3).** Demo account. Place 20 resting limits
(1 contract, 5¢, `expiration_ts = now + 120`) and cancel each, recording
`ts_before_ms`/`ts_after_ms` per cancel and the HTTP status. **Deliverable:** p50 / p95 /
max cancel RTT, and the 404 rate. Sets the correct `in_window` deadline for
`streak/strategy.rs:1271` and tells us whether the T0+75 tail in F3 is a real tail or a
theoretical one. Cost: 20 demo orders + 20 cancels, ~2 minutes.

**P3 — Wing-fill persistence across passes (sizes F4).** Paper / read-only, no orders.
During one Mon-Wed metal T-3h hour, on every 60s pass log for each qualifying rung: ticker,
`no_ask_cents`, `ceiling_cents`, and the resting NO size at-or-below the ceiling from
`orderbook`. **Deliverable:** for rungs whose ask was at/below the ceiling on pass *k*, the
fraction still at/below on pass *k+1..k+5*. That number is the EV `MAX_ENTRY_ATTEMPTS = 2`
plus the permanent dedupe is currently discarding, and it also tells us whether a re-armable
dedupe (`{ticker}|{pass}`) or a longer ladder is the right fix. Cost: read-only GETs on one
existing scan cadence.

---

*Lane CONSTANTS complete. Findings F1-F10 all carry file:line, a concrete inputs → wrong
money outcome scenario, and a decisive check. Two adversarial hypotheses were tested against
disk and KILLED (daily-budget truncation; ladder overrunning the window) — both are recorded
under Verified Clean with the numbers.*
