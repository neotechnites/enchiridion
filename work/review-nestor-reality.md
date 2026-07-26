# REVIEW: nestor deep review — lane REALITY (class B: spec-vs-reality) — 2026-07-26

Charter: `work/steer-nestor-deep-review.md` §Lane charters #2. Repo read-only, no orders placed,
no prod keys touched. Every exchange-behavior assumption in the tree classified PROVEN (vault
evidence cited) vs INFERRED (docs/reasoning only). 44 assumptions inventoried; 17 PROVEN,
23 INFERRED, 4 CONTRADICTED (code disagrees with our own verified evidence).

**Headline:** the single most dangerous unproven fact in the tree is not in a strategy — it is
`GET /portfolio/balance`'s treatment of resting-order collateral, and the divergence breaker
takes the whole bot down on either answer (F1). Second: the resting-order POST classifies every
non-2xx as "nothing was created", which is false for exactly the code the retry/restart path
produces (F2).

---

## FINDINGS (most severe first)

### F1 — CRITICAL. The divergence breaker's model of `/portfolio/balance` is unproven and is *mutually inconsistent* between streak and house. Either answer HALTS the whole bot.

`crates/engine/src/reconcile.rs:26` (`DIVERGENCE_THRESHOLD_USD = 2.0`), `:241-260`;
`crates/engine/src/risk.rs:250-254` (`total_at_risk = open stakes + reservations`), `:467-469`
(`expected_cash = bankroll − total_at_risk`); `crates/engine/src/strategy.rs:379`
(streak maker leg **reserves** its stake); `crates/house/src/strategy.rs:462-466`
(house **bypasses the risk layer entirely** — grep `eng.risk` across `crates/house/` = 0 hits).

The unproven exchange fact: **does Kalshi deduct the collateral of a RESTING (unfilled) buy order
from the `balance` field?** No probe, no live datapoint. There has never been a resting order
live (note 39: house has posted zero quotes; no `maker_rest` episode yet).

Both answers break something, because the two crates model it oppositely:

| Kalshi behaviour | streak (reserves $4.00) | house (reserves nothing) |
|---|---|---|
| **locks collateral** | real −$4, expected −$4 → Δ 0 ✅ | real −(quote cost), expected 0 → **Δ grows** ❌ |
| **does not lock** | real 0, expected −$4 → **Δ = $4.00** ❌ | Δ 0 ✅ |

Failure scenario A (Kalshi does not lock): first live 4-streak → `place_maker` rests 10 @ 40¢ =
$4.00 reserved at T0, cancelled at T0+45 (`crates/streak/src/exec.rs:63`). Reconcile runs every
60s (`nestor_bin/src/main.rs:397`), so **P(a reconcile pass lands inside the resting window) ≈
45/60 = 75%**. That pass computes |real − expected| = $4.00 + the standing $0.25 (F13) = **$4.25 >
$2.00 → `risk.halt()`, persisted** (`reconcile.rs:252-262`, `risk.rs:472-475`). Streak, volbook
and house all stop trading on the first maker leg, on a Monday with three firsts scheduled.

Failure scenario B (Kalshi does lock — the more likely truth): house's two-sided 1-lot quote costs
`bid_px + (100 − ask_px)` ≈ **$0.98/market** at mid 50¢ (`crates/house/src/signal.rs:60-66`,
`strategy.rs:419-425`). Two books quoted (`H10 econ + H9 political`) ≈ **$1.96**; at
`HOUSE_SIZE=2` it is **$3.92 → HALT**. Independently, every house *fill* moves real cash with no
`expected_cash` move at all (house never books to the risk layer): cumulative house inventory cost
> ~$2.25 → HALT. Reconcile's orphan adoption (`reconcile.rs:206-238`) partially rescues the *fill*
case within 60s by adopting the position into `total_at_risk` — but it cannot rescue the *resting*
case, because a resting order is not a position.

Cheapest decisive check: **P1** (5 demo calls, 30s).

Secondary defect exposed by the same table: house consuming real cash outside the risk ledger
means `bankroll`, `day_spent`, drawdown and the kill-switch are all blind to the house sleeve.
That is MONEYPATH's to own; flagged here because it is the mechanism that turns the balance
question into a halt.

---

### F2 — CRITICAL. `place_resting` treats *every* non-2xx as "the exchange created nothing" and immediately fires a taker IOC. That is false for 409 `order_already_exists` and for 5xx/gateway responses.

`crates/engine/src/strategy.rs:446-453`:

```rust
if !(200..300).contains(&status) {
    self.release_reservation(reserve_key);
    self.note_order_failure(Some(status));
    // The exchange answered: it created nothing. Safe to take instead.
    return RestOutcome::RestError { msg: ..., may_be_resting: false };
}
```

→ `crates/streak/src/strategy.rs:1139-1159` takes that branch straight to `taker_leg(..., ceiling)`.

The taker path *does* classify 409 correctly (`engine/strategy.rs:624-641`, `:809-818`
"benign duplicate `order_already_exists`"). The resting path does not — the same knowledge exists
one function away and is not applied.

Failure scenario (concrete): process restart at T0+20s during a live maker leg (tonight is the VPS
cutover). `seen` is in-memory only (`streak/strategy.rs:302-316`), so the ticker re-signals.
`sweep_orphan_rests` (`streak/strategy.rs:492-527`) reads `/portfolio/orders?status=resting`,
which our own ops ledger records as **eventually consistent** (note 39) — 20s after a POST it may
not list the order, so the sweep cancels nothing and returns. `place_maker` then re-posts with the
**deterministic coid** `streak-{ticker}-m40` (`streak/strategy.rs:1047`). Kalshi returns
**409 duplicate coid** (demo-proven, `verify-streak-retry.md`). Code reads "created nothing" →
sends **IOC 8 @ 46¢** → fills. The original 10 @ 40¢ bid is still resting until
`expiration_ts = T0+60`, enforced lazily (~2-3 min) → it fills too. Two real positions on one
ticker; `RiskManager::on_fill_actual`'s one-position-per-ticker guard (`risk.rs:383-392`) refuses
the second → **$4.00 of live exposure the ledger, the kill-switch and the bankroll cannot see**,
until reconcile's orphan adoption finds it up to 60s later (and books it at worst-case 99¢ if
`market_exposure` is absent).

Same shape for a **502/504** on the resting POST: Kalshi's edge can time out after the matching
engine accepted the order. `may_be_resting: false` asserts it cannot.

Cheapest decisive check: **P2** (4 demo calls) — establishes whether a duplicate coid 409 is
returned when the prior order is (a) still resting and (b) already cancelled, i.e. whether coid
dedupe survives the order's death (the restart case).

---

### F3 — HIGH. "cancel → 404 ⇒ the order filled" is over-read. 404 is also what a *lazily expired* order returns, and the cancel-retry loop is guaranteed to reach that state.

`crates/streak/src/strategy.rs:1282-1289` (deadline cancel), `:1427-1435` (flip cancel).
Comment: *"Before `expiration_ts` the only way a bid leaves the book is by FILLING."*

PROVEN (verify-house-truth.md, "Bonus mechanics"): cancelling an already-filled order → 404
`not_found`. INFERRED: the converse — that 404 ⇒ filled. Other producers of 404 on that path:
the order expired and was swept; an exchange-side cancel (market halt/close); our own startup
sweep cancelled it a moment earlier; an order-index lag (R114 family: `GET /portfolio/orders/{id}`
404s for *seconds* after a 201 that returned that id — the DELETE path's indexing behaviour was
never separately measured).

Failure scenario: the T0+45 cancel fails on a network blip → `mark_cancel_failed`
(`streak/strategy.rs:1384-1416`) sets `cancel_failed` and retries **every scan pass until
`expiration_ts + CANCEL_RETRY_GRACE_SECS` = T0+60+240 = T0+300** (`strategy.rs:75`, `:1385`).
The exchange's lazy sweep expires the order somewhere in T0+60…T0+240. Every retry after that
point returns 404 → read as "it filled" → `mark_gone` → **backstop withheld permanently for that
episode** (correct EV forgone ~4.3¢ × 8), the **cap reservation held for up to 300s**, and at the
end a false CRITICAL page: *"left the book (cancel 404) but no fill ever appeared in
/portfolio/fills — check positions"* (`:1370-1378`). Wrong-money impact: none directly; but it
manufactures exactly the kind of false alarm that trains the operator to ignore the real one, and
it blocks the cluster cap across a window boundary.

Cheapest decisive check: **P3** (1 POST + 1 DELETE + a 200s wait) — place a GTD that cannot fill,
let it expire, then cancel it and read the status/code. Two calls to kill or confirm.

---

### F4 — HIGH. Settlement *cash credit* is assumed simultaneous with `status == "determined"`. Verified n=1 on crypto; entirely unmeasured on metal dailies, where volbook will hold ~11 rungs.

`crates/engine/src/reconcile.rs:41-59` (`is_settleable`: determined/finalized/settled/empty are
all bookable), `:145-149` (`risk.settle` credits the payout into `bankroll`), then `:187` runs the
divergence check **in the same pass**. The assumption is that the exchange has already moved the
cash by the time `/markets/{ticker}` reports `determined`.

Evidence I could find, and it is the only evidence: `logs/run.log` — **2730 consecutive
`divergence check OK` lines, every one at exactly Δ$0.25, including the pass at
2026-07-25T14:00:29Z that settled the live +$6.03 win** (`logs/settlements.jsonl`). No transient
excursion. So for **KXBTC15M, n=1**, payout ≤ determination. That is a real datapoint and it is
one datapoint on one series.

Failure scenario: volbook's first Monday fills ~11 metal rungs (verify-volbook-execution.md §5
predicts mean 11.2 qualifying). They settle together at 21:00Z. If a metal daily's cash credit
lags `determined` by even one 60s reconcile pass, the pass that books them sees
`expected_cash` jump by the full payout while `real_cash` has not moved: at 4 contracts/rung
(≈$4 stake at ~90¢) × 11 rungs, a good day pays **≈$44** → divergence $44 ≫ $2 → **HALT**, on the
evening of volbook's first successful session. The mirror case is just as live: if the exchange
credits *before* `/markets/{ticker}` flips to determined (and we already know the `status=settled`
filter lags the result by ~36s — `kalshi.rs:358-362`), a reconcile pass landing in that window
skips the settlement as `pending` but sees the payout in `balance` → same halt.

Cheapest decisive check: **P5a** — pure observation, zero money: poll `/markets/{gold rung}`
(status + result) at 1 Hz across 21:00Z Monday and record close→determined→finalized latency for
the metal series. Then P5b to tie cash to it.

---

### F5 — HIGH. There is no model of Kalshi's rate limit anywhere in the tree, no client-side limiter, and two of four loops silently ignore 429 backoff. A 429 inside a streak entry window loses the window permanently.

No token bucket, semaphore or concurrency cap exists (`crates/engine/src/lib.rs:28-34` sets only
`timeout(30s)` / `connect_timeout(10s)`; reqwest's per-host pool is unbounded). Measured worst-case
request accounting for the live process (streak + volbook + house + ws + spot sampler + reconcile):

| phase | public req/s | signed req/s | total |
|---|---|---|---|
| lazy (750s of every 900s window) | 1.55 | 0.70 | 2.25 |
| streak entry window (1 Hz, both coins) | 5.4 | 2.7 | 8.1 |
| realistic aligned burst (streak 4-attempt ladder ×2 coins + house requote ×2 books) | ~7 | ~19 | **~26** |
| pathological (add volbook 3-rung entry + reconcile with 20 open positions) | ~20 | ~41 | **~61** |

Kalshi's published basic tier is on the order of 10 read/s and 10 write/s. **~19-41 signed req/s is
plausibly 2-4× over.** We have never measured it.

Failure scenario: a 429 on a streak entry POST → `recover_lost_ack` fires *another* signed GET into
the active limit (`engine/strategy.rs:744-763`) → no fill → `ExecOutcome::OrderError` →
`streak/strategy.rs:1622-1626` breaks the retry ladder immediately (it retries only on `Missed`)
→ and `enter()` already consumed `self.first_time(ticker)` (`streak/strategy.rs:963-965`), so the
ticker is in `seen` **for the process lifetime**. The window is lost silently — no alert, and the
skip-alarm never fires because it is not a `Skip`. Additionally the **house loop
(`nestor_bin/src/main.rs:443-450`) and the volbook loop (`:420-428`) discard the retryable
classification their crates compute and sleep a flat 3s / 60s**, so a 429 storm keeps house
hammering at ~2-4.7 req/s indefinitely, with Retry-After ignored.

Cheapest decisive check: **P6** — demo ramp to first 429, 2 minutes, zero money.

---

### F6 — HIGH. Partial-fill semantics on a GTC resting order are entirely INFERRED, and streak branches a money decision on them.

`crates/streak/src/strategy.rs:1300-1322` branches on `parse_cancel_reduced_by`:
`r >= count` → backstop is safe; `0 < r < count` → a partial landed, withhold the backstop
(`:1306-1314`). PROVEN (verify-house-truth.md): a successful cancel returns
`{"order_id":…,"reduced_by":"1.00","ts_ms":…}` — **on a size-1 order that was fully resting**.
Never observed: `reduced_by` on an order that was *partially* filled; whether a partial fill
produces one fills row or several; whether `fee_cost` is per-row-total on a partial (verify-house-
truth asserts total-per-row from single-fill rows only).

Failure scenario: if Kalshi's `reduced_by` on a partial reports the *filled* quantity rather than
the *remaining* one, the two branches invert: a 10-lot bid with 2 filled would return
`reduced_by = 2` → `2 < 10` → we correctly withhold; but a 10-lot bid with 8 filled returns
`reduced_by = 8` → still `< 10` → withhold, fine. The dangerous inversion is a *fully unfilled*
cancel reporting `reduced_by = 0` (nothing filled, expressed as "0 filled"): `0 < 10` → we
**withhold the backstop on every clean cancel**, and the entire taker backstop leg — 21% of
signals, the larger half of the +30-40% capture gain — never fires, silently. Nothing in the tape
would distinguish that from "the market never came to us".

Related, same root: `settle_maker_fill` (`:1505-1509`) deliberately never tops up a partial. That
is a defensible choice; it is *documented* as costing ~4.3¢ but has never been measured because a
partial has never been seen.

Cheapest decisive check: **P4** — one deliberately-adverse resting quote on demo KXBTC15M (proven
to be picked off in 4.7-45.9s), cancel mid-fill, read `reduced_by` and the fills rows.

---

### F7 — HIGH (CONTRADICTED). `taker_fee` ceils to the whole cent; our own demo probe proved Kalshi ceils to $0.0001. And the taker fill path debits the *formula* while the exchange's number sits unused in the same struct.

`crates/engine/src/risk.rs:127-131`:
```rust
let raw = 0.07 * count as f64 * p * (1.0 - p);
(raw * 100.0).ceil() / 100.0        // ceil to the next WHOLE CENT
```
vs `work/verify-house-truth.md` Q3: *"Taker fee = `ceil(0.07·P·(1−P)·C, $0.0001)` — exact on 5/5
(note: rounding up to $0.0001 / one-hundredth of a cent, **NOT up to a whole cent as the old fee
tables state**)."* The doc comment at `risk.rs:123-126` still asserts whole-cent is "Kalshi's actual
billing". Over-charge ≤ $0.0099 per order.

Worse, the *taker* fill path never uses the exchange number: `crates/engine/src/strategy.rs:693-697`
calls `on_fill_actual(&order, filled, avg_price)` with **no fee argument**, so the bankroll is
debited by the formula — while `placed.actual_fee_cents` (parsed from `average_fee_paid`,
`kalshi.rs:756-765`) is passed into the `FillReport` two lines later and only logged. The *resting*
path does it correctly (`strategy.rs:515-533` → `on_fill_actual_fee`). Asymmetric.

Failure scenario: pure accounting drift against the $2.00 divergence budget — ≤$0.0099/order is
slow, but it is one-directional and the budget already carries a standing $0.25 (F13). It also
means the streak week-1 tape's `fee_cents` (`streak/strategy.rs:1514`, `:1742`) and volbook's
(`volbook/strategy.rs:356`, `:393`) are systematically wrong in the direction that *understates*
measured EV — i.e. it will make a good policy look worse than it is.

Cheapest decisive check: already proven; this is a code-vs-evidence contradiction, not a probe.

---

### F8 — MED-HIGH. Volbook assumes the crypto taker fee schedule applies to metal dailies, and computes its per-bucket ceiling with a fee evaluated at a different price than the researcher's fixed point.

`crates/volbook/src/signal.rs:82-84` (`fee_cents(p) = 7·p·(1−p)`, un-ceiled, per contract),
`:103-107` (`willingness_to_pay_cents` evaluates the fee at the *fair NO price*, then `.floor()`s).
verify-volbook-execution.md §6 flagged this and could not check it (repo untouched). Now checked:
the crate's ceilings differ from the verdict document's fixed point —
bucket 0.05-0.10 → crate **97¢** vs doc 96.96¢; **bucket 0.30-0.35 → crate 68¢ vs doc 68.66¢**.
That is the *only* bucket where the ceiling actually binds (11 of 12 historical rejects sit there),
and the crate is 0.66¢ **tighter**, so it rejects marginally more of the one bucket whose realized
EV is already the worst (−13.32c on n=9, se 16.6). Direction is conservative; magnitude small.

Separately: no evidence anywhere that non-crypto series carry the same fee schedule. All 12
verified fee rows in the vault are KXBTC15M on demo.

Failure scenario: if metal dailies carry a different (e.g. flat, or higher) taker fee, every
per-bucket ceiling is mispriced. At 90¢ entry the fee term is only 0.63¢, so a 2× schedule shifts
the ceiling ~0.6¢ — small — but the *booked* P&L per rung would be wrong by that amount × 11 rungs
× every day, and the EV attribution for the promote/kill decision would be wrong.

Cheapest decisive check: **P7** — one 1-lot IOC on a demo KXGOLDD wing rung, read `fee_cost`.

---

### F9 — MED. The websocket has no client-side liveness check, and a seq gap tears down every sid. Prod produced 12 gaps in the first 8 minutes with backoff escalating to 60s ≥ the entire entry window.

`crates/engine/src/ws.rs:424-428` — the client only *replies* to a server `Ping`; it never sends
one and has **no read timeout / idle deadline**. A half-open socket is detected only by TCP.
`ws.rs:440-450` — a seq gap on any sid returns `Err`, tearing down the whole connection;
`ws.rs:371` `invalidate_all()` flips `synced=false` on every book **but retains the stale price
levels and the stale `updated` timestamp**, so the `synced` boolean is the *only* thing standing
between a gapped book and a trading decision. `ws.rs:377-382` backoff = 3/3/6/12/24/48/60 (cap 60).

Live evidence from `logs/run.log` (2026-07-26): connection established 17:27:55Z, then **12
consecutive `seq gap — resync via reconnect` events through 17:35:43Z**, backoff escalating to the
60s cap; stable for the following 2h40m. So the per-sid seq model is right in steady state and
wrong on (re)subscribe. From `data/ws_divergence.jsonl` (n=510 over 1h46m): **66 rows unsynced
overall; 14/448 = 3.1% after the startup burst**; ws-vs-REST |Δask| mean **2.6¢**, max **13¢**,
non-zero **81.6%** of the time.

Failure scenario: with `STREAK_WS=1` (the planned flip), a seq gap inside a 60s entry window makes
the book unusable for 3.5-61s. The design degrades to REST correctly
(`streak/strategy.rs:470-484` requires `synced && age < 1s`), so this costs the ws's 2.6¢ pricing
advantage, not money — **unless** a future change relaxes the `synced` gate, at which point the
retained stale levels become live prices. Today: MED (sensor outage in the exact window the sensor
exists for). The 3.1% unsynced rate is the number to hold the flip against.

Cheapest decisive check: **P8** — read-only frame capture across a multi-ticker subscribe.

---

### F10 — MED. House's 75s resting TTL is a safety property verified only to ~4 minutes.

`crates/house/src/signal.rs:13` `ORDER_TTL_SECS = 75`; `house/strategy.rs:417` sets
`expiration_ts = now + 75`. Streak's maker leg is the same shape at 60s
(`crates/streak/src/exec.rs:65-72`, which honestly documents the ~2-3min lazy sweep). The claim
"a dead process leaves NOTHING resting beyond ~75s" (`kalshi.rs:550-553`) is **false as written** —
verify-house-truth.md's closing section: *"Expiration-sweep laziness (≈2-3 min past
`expiration_ts`, prior run) was not re-measured today; the ~4 min worst-case orphan window stands."*

Failure scenario: process dies at T0+10s with a streak maker leg live. The bid can fill up to
~T0+240 — i.e. on a window we never decided to trade, at a price the signal no longer supports.
Accepted at $4 of size by explicit ruling; it becomes unacceptable the moment sizing moves to the
5% fraction (R148, after 8 clean fills). The number that must be re-measured before that: the
actual sweep lag under prod load.

---

### F11 — MED. `status="open"` is used as the `/markets` filter value everywhere, while our own lifecycle model names the states `active/closed/determined/finalized`.

`crates/volbook/src/strategy.rs:191`, `streak/strategy.rs:565`, `house/strategy.rs:239`,
`lock:39`, `weather:128` — all pass `"open"`. `crates/engine/src/kalshi.rs:173-177` documents the
lifecycle as `active → closed → determined → finalized`. If `"open"` is not a recognised value,
Kalshi may be ignoring the filter (returning everything, harmless-ish) or returning empty
(catastrophic, but we would have noticed). It currently works for crypto, so it resolves to
*something* — but nobody has confirmed **what**, and volbook's Monday debut depends on it
returning the full metal strike ladder. Volbook additionally has **no `open_unix` check at all**
(`volbook/strategy.rs:200-205`), so a metal rung that opens after the T-3h window silently never
trades and produces no skip record.

Cheapest decisive check: free — one public GET per series with `status=open` vs `status=active`
vs no status, compare counts. Fold into P5a.

---

### F12 — MED. Prod maker fee = 0 is INFERRED (demo 7/7). Direction differs by strategy.

verify-house-truth.md Q3 proves maker `fee_cost = 0.000000` on 7/7 demo fills across a
discriminating price range. Prod is explicitly unknown (that doc's "WHAT STAYS UNKNOWN").
Streak is safe either way: `exec.rs:24-26` derives EV(40) = +10.3¢ **including** the full taker fee
(52 − 40 − 1.68), so a zero maker fee is unmodelled upside, and the ledger reads `fee_cost`
(`streak/strategy.rs:1528-1538`). House is not: its whole thesis is "earn the 2¢ spread, pay fees
only on the taker flatten leg" (verify-house-truth §6). A prod maker fee of 1.6-1.7¢/contract at
mid **erases the entire gross edge** and the promote/kill verdict flips. House reads `fee_cost`
correctly (`house/strategy.rs:532-544`), so the ledger will be right — the *economics* are what is
unproven. First live maker fill settles it; it must be logged loudly and read before the promote
decision.

One code-level fragility in the same place: `house/strategy.rs:539-542` uses `try_fold`, so **if
any single fills row lacks `fee_cost`, the whole batch falls back to the taker formula** — a
mixed-presence batch silently converts a free maker fill into a 1.7¢ charge.

---

### F13 — MED (unasked datum, class D adjacent). A standing Δ$0.25 has been present in every one of 2730 divergence checks since the first live pass, and nobody has asked what it is.

`logs/run.log`: the very first live check, `2026-07-24T16:35:38Z`, reads
`real $100.25 vs expected $100.00 (Δ$0.25)`, and **all 2730 subsequent checks read exactly
Δ$0.25** (real $106.28 vs expected $106.03 today). So it is a constant offset present at live
genesis — most likely a residual from a pre-live self-test order or a deposit rounding — not
accounting drift. Two consequences: (a) it permanently consumes **12.5% of the $2.00 divergence
budget**, which matters given F1/F4 both live inside that budget; (b) its constancy is itself the
strongest evidence we have that nothing else is drifting — worth stating, because it converts
"2730 OK lines" from noise into a real sensor reading.

---

## INVENTORY — every exchange-behavior assumption, PROVEN vs INFERRED (44)

P = PROVEN (evidence cited) · I = INFERRED · **C** = CONTRADICTED by our own evidence.
"Fail" = what breaks if the assumption is wrong. Probe IDs defined below.

| # | Assumption | Where | P/I | Evidence / Fail-if-wrong | Probe |
|---|---|---|---|---|---|
| 1 | `/portfolio/balance` nets resting-order collateral | reconcile.rs:241-260 + risk.rs:250-254 | **I** | no evidence · **full-bot HALT either way (F1)** | P1 |
| 2 | A non-2xx on the resting POST ⇒ nothing was created | strategy.rs:446-453 | **I** | false for 409/5xx · **invisible 2nd position (F2)** | P2 |
| 3 | Duplicate coid → 409, blocks the retry | verify-streak-retry.md, demo | P | proven; **dedupe lifetime unproven** | P2 |
| 4 | Cancel of a gone order → 404 `not_found` | verify-house-truth "Bonus mechanics" | P | demo-proven | — |
| 5 | 404 on cancel ⇒ the order **filled** | streak/strategy.rs:1282, :1427 | **I** | converse never tested · false alert + held cap (F3) | P3 |
| 6 | Successful cancel returns `reduced_by` = qty still resting | verify-house-truth, demo (size 1) | P | proven at size 1, fully resting | — |
| 7 | `reduced_by` on a **partial** fill = unfilled remainder | streak/strategy.rs:1300-1314 | **I** | never observed · backstop silently never fires (F6) | P4 |
| 8 | A partial produces fills rows whose `fee_cost` is row-total | kalshi.rs:911-917 | **I** | proven only on single-fill rows | P4 |
| 9 | `/portfolio/fills` is real-time (≤~0.9s), never misses | verify-house-truth Q2, n=8 | P | demo, low-traffic; **prod at load unmeasured** | P5b |
| 10 | `GET /portfolio/orders/{id}` 404s forever on filled IOCs → useless for fill detection | verify-house-truth Q2 (R114) | P | demo-proven twice | — |
| 11 | `/portfolio/orders?status=resting` is eventually consistent | note 39 ops gotchas | P | observed; **exact lag unquantified** — drives F2 | P2 |
| 12 | fills rows carry `fee_cost` (dollars, row-total) + `is_taker` | verify-house-truth Q1 | P | verbatim payload pinned | — |
| 13 | Maker fill billed `0.000000` **on prod** | house/lib.rs thesis; exec.rs | **I** | demo 7/7 only · **house economics flip (F12)** | P7 |
| 14 | Taker fee = `ceil(0.07·P(1−P)·C, $0.0001)` | verify-house-truth Q3, 5/5 | P | exact | — |
| 15 | Our `taker_fee` ceils to the whole cent | risk.rs:127-131 | **C** | contradicts #14 · ≤$0.0099/order drift (F7) | — |
| 16 | Metal dailies use the same fee schedule as crypto | volbook/signal.rs:82-84 | **I** | all 12 verified rows are KXBTC15M | P7 |
| 17 | Ceiling fixed point = researcher's | volbook/signal.rs:103-107 | **C** | 68¢ vs 68.66¢ in the only binding bucket (F8) | — |
| 18 | Create-order 201 is FLAT (no `order` envelope) | verify-house-truth Q1 | P | pinned; parser tolerates both | — |
| 19 | 201 `fill_count`/`remaining_count` are synchronous IOC truth | engine/strategy.rs:663-673 | P | verify-house-truth Q1 | — |
| 20 | IOC at the ceiling price-improves to the resting ask | exec.rs:170-176 | P | live: 28¢ fill on a higher limit (note 39/R156) | — |
| 21 | An IOC that crosses nothing returns `fill_count 0`, not an error | exec.rs:174-176 | P | live-observed | — |
| 22 | `self_trade_prevention_type` is REQUIRED; `taker_at_cross` prevents self-match | kalshi.rs:469-472, :597-600 | P | verify-house-truth "Bonus" | — |
| 23 | `cancel_both` fails oneof validation | kalshi.rs:594-596 | P | demo-proven | — |
| 24 | GTD = `good_till_canceled` + future `expiration_ts`; TIF is required | kalshi.rs:588-593 | P | demo-proven 2026-07-25 | — |
| 25 | Omitted `expiration_ts` ⇒ rests forever | kalshi.rs:560-562 | **I** | doc-only; never tested (correctly) | — |
| 26 | Expiry is enforced LAZILY, ≈2-3 min (≤4 min orphan window) | exec.rs:69-72; house TTL 75s | **I** | one prior demo run, **not re-measured** (F10) | P3 |
| 27 | Markets for `[T0, T0+900]` OPEN at T0 | note 39 | P | prod-verified | — |
| 28 | `status=settled` filter LAGS the result (~36s) → use time-bounded `recent_closed` | kalshi.rs:358-362 | P | live 2026-07-24, 3/3 skips | — |
| 29 | `result` is non-empty at `determined`, before `finalized` | reconcile.rs:41-59 | P | encoded + live crypto; **metals untested** | P5a |
| 30 | Settlement **cash** is credited by the time `status=determined` | reconcile.rs:145-187 | **I** | n=1 crypto (2730 flat Δ) · **HALT on ~$44 metals day (F4)** | P5a/b |
| 31 | Metal daily close→determined latency is small vs 60s reconcile | reconcile.rs:117 (no series model) | **I** | zero evidence | P5a |
| 32 | `status="open"` is a valid `/markets` filter value | volbook:191 + 4 others | **I** | contradicts our own lifecycle names (F11) | P5a |
| 33 | Cursor pagination at limit=1000 reaches every metal strike | kalshi.rs:332-355 | **I** | never page-counted for metals | P5a |
| 34 | Metal rungs are all open before T-3h | volbook (no `open_unix` check) | **I** | silent no-trade, no skip record | P5a |
| 35 | Wing NO books are deep (200-500 @ touch) ⇒ 1+1 retry suffices | volbook/strategy.rs:43 | **I** | measured at ttc 27-77h on a weekend; **0 in-window snaps**; copper never captured | P5a |
| 36 | `market_positions` uses `position_fp` + `market_exposure_dollars` | kalshi.rs:1020-1046 | P | empirically observed on demo 2026-07-24 | — |
| 37 | Orderbook live schema = `orderbook_fp.{yes,no}_dollars` string-dollars | kalshi.rs:1155-1191 | P | verified 2026-07-25 | — |
| 38 | ws seq is per-**sid**, +1, across ALL frames incl. `ok` acks | ws.rs:528-544 | P | prod-verified (fix live) — but **12 gaps in 8 min at startup (F9)** | P8 |
| 39 | The server pings every ~10s (so no client keepalive is needed) | ws.rs:424-428 | **I** | doc-only · silent half-open socket | P8 |
| 40 | ws snapshot keys are `*_dollars_fp`; deltas carry `price`/`delta` | ws.rs:234-239, :311-335 | P | prod-verified | — |
| 41 | ws price value <1.0 ⇒ dollars, ≥1.0 ⇒ cents | ws.rs:212-219 | **I** | a dollar-encoded `1.00` reads as 1¢ | P8 |
| 42 | Our worst-case ~26-61 req/s is inside Kalshi's limit | nowhere — no limiter exists | **I** | plausibly 2-4× over · **window lost silently (F5)** | P6 |
| 43 | `Retry-After` is honoured on 429 | net.rs:44-64 + main.rs:334-341 | P (partial) | **streak/reconcile only; house & volbook loops discard it (F5)** | — |
| 44 | One account, one subaccount, commingled | grep `subaccount` = 0 hits | **I** | fills rows carry `subaccount_number: 0`; LIP/maker separation (R153) will break this | — |

---

## WHAT I VERIFIED CLEAN (and how)

- **A gapped or stale ws book can never price an entry.** Traced every reader of `WsBook`:
  `streak/strategy.rs:446-484` is the only one; it requires `STREAK_WS=1` **and**
  `q.synced && q.age < 1s` before overwriting `cand.yes_ask`/`no_ask`; REST asks are the untouched
  floor (`:580-585`). `invalidate_all` clears `synced` on every book on any gap. No other crate
  reads it. (Residual: the stale *levels* survive invalidation — the `synced` bool is the only
  guard. Do not relax it.)
- **Settlement outcomes are READ, never inferred.** `reconcile.rs:32-39` reads the literal `result`
  string; `""`/`void`/anything unexpected returns `None` → skip, never a phantom booking.
  `is_settleable` additionally refuses `determined` with an empty result.
- **The YES-book side/price translation is right** (`kalshi.rs:683-718`), unit-tested both
  directions including the docs' own worked example (buy NO @40 → ask 0.6000), and the fill-price
  inverse round-trips (`:1241-1248`). This is the one that would be catastrophic and it is solid.
- **Streak's settled-window handling correctly does not trust the `settled` filter**
  (`kalshi.rs:363-386`, `streak/strategy.rs:427-430`) — the class-B lesson was actually learned.
- **The backstop is re-sized at its own price**, not carried over from the maker leg
  (`exec.rs:226-240` test): 10 @ 40¢ ($4.00) → 8 @ 46¢ ($3.68), never over the $4 cap.
- **The 45s backstop ladder lands in-window**: 45 + 3×2s + 1 = 52 < 60 (`exec.rs:203-212` test).
- **Fee reading on the resting path is correct** (`engine/strategy.rs:515-533` → `on_fill_actual_fee`,
  `streak/strategy.rs:1528-1538` records both estimate and `fee_cost` with a `fee_basis` tag).
  It is the *taker* path that discards it (F7).
- **Divergence has not drifted**: 2730/2730 checks at exactly Δ$0.25 since live genesis (F13).

---

## PROPOSED PROBES — ranked by decisiveness per cost (DESIGN ONLY, none run)

All demo (`demo-api.kalshi.co`, key …fb, PEM `nestor/secrets/Demo.txt`) unless marked read-only.
Every resting order carries a future `expiration_ts`; cancel everything; assert the resting list is
empty at exit.

| # | Probe | Cost | Kills/confirms | Decisiveness |
|---|---|---|---|---|
| **P1** | **Resting-order collateral.** `GET /portfolio/balance` → place 1 GTD resting BUY 10 @ 40¢ (`expiration_ts = now+120`) on any liquid demo market **priced well above 40¢ so it cannot fill** → `GET balance` → `DELETE` the order → `GET balance`. Record all three. **If balance drops by exactly $4.00 while resting: Kalshi locks → F1 branch B (house is broken).** If unchanged: F1 branch A (streak halts the bot on its first maker leg). Extend by 1 call: repeat with a two-sided pair to price house's exposure. | 5 calls, 30s, $0 | **F1 (CRITICAL)** | absolute — a binary fact with a halt on both sides |
| **P2** | **coid dedupe lifetime + resting-409 classification.** POST resting order with coid `X` (exp now+180) → re-POST the *identical* body with coid `X`, record status + body → `DELETE` the first order → re-POST coid `X` a third time, record status. Answers: does a duplicate coid 409 while resting? does it still 409 after the order is dead (the restart case)? and is the 409 body's `code` exactly `order_already_exists`? | 4 calls, 20s, $0 | **F2 (CRITICAL)** | absolute for the restart double-fill path |
| **P3** | **404 disambiguation + real expiry lag.** POST a resting BUY 1 @ 1¢ (unfillable) with `expiration_ts = now+20`. Then poll `GET /portfolio/orders?status=resting&ticker=…` every 5s and record the timestamp it disappears (= the true lazy-sweep lag, F10/#26). Once gone, `DELETE` it and record the status/code. **A 404 here proves 404 ≠ "filled"** and kills F3's premise outright. | 1 POST + ~50 cheap GETs + 1 DELETE, ~4 min, $0.01 | **F3 (HIGH) + #26** | absolute; also re-measures the orphan window nobody re-measured |
| **P5a** | **Metal settlement timing — READ ONLY, zero money, prod-safe.** Across Monday 21:00Z: 1 Hz `GET /markets/{KXGOLDD rung}` recording `status` + `result`; log close→closed→determined→finalized latencies. Same pass answers #32/#33/#34: call `/markets?series_ticker=KXGOLDD` with `status=open`, `status=active`, and no status, compare counts and page counts, and record every rung's `open_time`. | ~1800 public GETs over 30 min, $0, no auth | **F4 (HIGH) + F11 + #29/#31/#32/#33/#34** | high; the one probe that can be run on prod today with zero risk |
| **P4** | **Partial fill + `reduced_by`.** On the live demo KXBTC15M window, post a resting BUY **5 @ 1¢ inside the best ask** (verify-house-truth: 5/5 such quotes were picked off in 4.7-45.9s). Poll `/portfolio/fills` at 250ms; the instant `0 < filled < 5`, `DELETE` the order. Record `reduced_by`, every fills row (`count_fp`, `fee_cost`, `is_taker`), and whether one row or several. | 1 POST + ~200 polls + 1 DELETE, ~1 min, ≤$0.05 | **F6 (HIGH) + #7/#8** | high; the only way to see a partial before live does |
| **P6** | **Rate-limit ceiling.** Ramp `GET /markets?series_ticker=KXBTC15M` from 5 → 40 req/s in 5 req/s steps of 10s; record the first 429, its `Retry-After`, and the sustained pass rate. Repeat for the signed `GET /portfolio/balance`. Gives the real public and signed budgets to size against the ~26-61 req/s burst. | ~2 min, $0 | **F5 (HIGH) + #42** | high; converts an unknown into a number we can build a limiter against |
| **P7** | **Metal fee schedule.** One 1-lot IOC taker BUY on a demo KXGOLDD (or KXSILVERD) wing rung; read the fills row's `fee_cost` and compare against `0.07·P(1−P)`, against `ceil(…,$0.0001)`, and against `ceil(…,$0.01)`. If a maker fill is achievable on the same book, repeat resting. | 2 orders, ~$1.80 demo | **F8 + F12 + #13/#16** | medium-high; also re-settles #14 on a second series |
| **P8** | **ws seq at subscribe — READ ONLY.** Connect, subscribe to ONE ticker, dump raw frames (`sid`, `seq`, `type`) for 120s; then subscribe a SECOND ticker on the same connection and dump 120s more. Determines whether the 12-gap startup burst is caused by multi-ticker subscribe interleaving (and therefore whether `seq_in_order` needs a per-subscription baseline reset). Also record the observed server-ping interval to settle #39, and whether any price arrives dollar-encoded as `1.00` (#41). | 4 min, $0 | **F9 + #38/#39/#41** | medium; sensor quality, not money — but it gates the `STREAK_WS=1` flip |

**Suggested batch order:** P5a (free, prod, time-boxed to Monday 21:00Z — schedule it now) →
P1 + P2 + P3 in one demo session (they share the same resting-order harness and together kill both
CRITICALs) → P4 → P6 → P7 → P8.

**Do before Monday regardless of probes** (code-vs-evidence contradictions needing no new data):
fix `risk.rs:127-131` to ceil at $0.0001 (F7); pass `placed.actual_fee_cents` into the taker fill
booking at `engine/strategy.rs:693-697` (F7); classify 409 `order_already_exists` on the resting
POST as `may_be_resting: true` (F2); make house's `try_fold` on `fee_cost` per-row rather than
all-or-nothing (F12).
