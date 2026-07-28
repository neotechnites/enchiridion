# SPEC: lip_v5 — presence-portfolio maker (from-first-principles rewrite)

Date 2026-07-28. Sources: `work/charter-lip-v5.md` (ground truth), `work/spec-lip-maker-v1.md` (v4's spec —
inherited only where re-derived), note 23 §II/§III/§IV, `tools/lip_maker_v4/` (evidence of what survived),
`crates/engine/src/reconcile.rs` (the breaker v5 must feed), `work/ops-first-principles.md`.

> **Implementor stanza (note 23 §II).** You are implementing an INTENT. This spec is a colleague's claim,
> not truth. Before writing code, ENUMERATE every constant, cadence, size, timeout and default you are
> about to write; derive each from §0's objective; mark UNDERIVED what you cannot. **For every guard you
> write, answer the MIRROR question in a comment beside it** (note 23 §IV): *"this guards one end/side/
> direction — name the other end; who guards it?"* An unnamed mirror is an unshipped incident. Where this
> spec and first principles diverge, STOP and surface. Answer note 23 §III's five (cash/breaker/schedule/
> collisions/alerts) in the binary's header before first launch — §11 drafts them; verify, don't copy.

## 0. THE OBJECTIVE AND ITS COST TERMS (everything below is a corollary)

0.1 **Maximize** `Σ_(market,side,second) resting_dollars × 0.5^ticks × pool_rate`, NET of fill costs (drift;
maker fees ≡ 0), inventory carry (`capital × time-to-liquidity`, PRICED), and operational risk — subject to
the bankroll envelope and program-revocation risk. **Axiom 1: earning = capital × time × proximity, in the
RESTING state.** Every constant below names the term it grows or the cost it cuts.

0.2 **Inherited, not re-litigated** (verified in v1 §0.2-0.5, prod-proven in v4): `Score = DF^(ref−price) ×
size`, DF = 0.50 on 100% of live programs (so `0.5^ticks` in 0.1 IS the filing's weight); per-side
normalize; snapshot excluded unless both sides hold ≥ `target_size_fp`; payout paid iff ≥ $1.00, rounded
down to the cent; `pool_usd = period_reward × 1e-4` **with the startup refusal assertion (v1 §0.3) kept
verbatim**; program periods are multi-day (modal 228h) so every window quantity is a FRACTION of that
program's own `[start_date, end_date]`; `ρ = pool_usd / window_hours` ($/h); a YES ask IS a NO bid.

0.3 **The PayPal axiom, in one line.** Per (market m, side s), with `q` = our resting contracts at same-side
best, `p` = price ($/contract), `S` = rival qualifying score, `φ` = fills/hour/resting-contract (MEASURED,
§2.4), `d` = adverse-selection drift per filled contract ($, measured), `L_eff` = hours to certain
liquidity (§1.2), `r*` = the portfolio's achieved marginal rate ($/h per collateral-$):

```
gross(q)   = ρ·S / (2·p·(q+S)²)                       $/h per collateral-$      [v1 §0.4, kept]
carry_cost = φ · L_eff · r*                           $/h per collateral-$      [NEW — the $16 lesson]
drift_cost = φ · d / p                                $/h per collateral-$      [v1 §2.2 hurdle, kept]
net(q)     = T̂ · gross(q)  −  carry_cost  −  drift_cost                                            (★)
```
`T̂` = measured toxicity multiplier ∈ [0,1] (§2.3). **(★) is the whole spec.** ALLOCATE admits a slot iff
`net(q) > λ_min/16`; there is no separate hurdle comparison (v1 §2.2's hurdle is now inside ★).

0.4 **Worked, with our own numbers — this is the test-plan's spine.** `r* = 0.00625`/h (gas-window achieved),
`d = $0.07`, `λ_min/16 = 0.00625`/h.
| venue | ρ $/h | p | S | φ | L_eff h | gross | carry | drift | net(0) | verdict |
|---|---|---|---|---|---|---|---|---|---|---|
| PYPL mention (close DEC 31) | 0.439 | 0.30 | 50 | 0.50 | 3744 | 0.0146 | **11.70** | 0.117 | **−11.80** | EXCLUDED, 808× |
| treasury daily | 6.25 | 0.50 | 50 | 0.08 | 8 | 0.125 | 0.0040 | 0.0112 | **+0.110** | KEEP, 17× floor |
| gas cheap side | 6.25 | 0.02 | 50 | 0.001 | 8 | 3.125 | 0.00005 | 0.001 | **+3.12** | KEEP |
The PayPal position is refused by three orders of magnitude by a term v4 did not have. Nothing else changed.
**`d` is capped at `p`** (v1 §2.2) — that cap is what makes the gas row reproduce: at `p=$0.02`,
`d = min($0.07, $0.02) = $0.02` so `d/p = 1.0` and drift = 0.001; at `p=0.50`, `d/p = 0.14`; at `p=0.30`,
`d/p = 0.233`. Recompute all three rows by hand before trusting any implementation of (★).

0.5 **Multiplier identity (charter §3's phrasing).** `H = max(0, 1 − carry_cost/(T̂·gross))` and
`net = T̂·gross·H − drift_cost`. **DIVERGENCE D1 (surfaced):** the charter writes horizon and toxicity as
multipliers; only toxicity is one. Carry is a COST that can exceed gross (PYPL: 800×) and a multiplier
cannot represent that without clipping to zero and losing the ranking. Additive is canonical; `H` is a
display quantity only. **DIVERGENCE D2:** the "verified-accrual multiplier" is derived as a CAP, not a rate
factor (§1.4) — model risk bounds exposure; it does not discount a rate, because a discounted rate still
allocates without bound to a venue that looks good enough.

## 1. THE VENUE PORTFOLIO MODEL

1.1 **Venue = series** (e.g. `KXAAAGASD`). Markets in a series share pool structure, horizon class and taker
population; verification (credits) arrives per program, and per-series is the coarsest key at which enough
settlements accumulate to verify anything. Toxicity is measured per **(market, side)** (charter §2) and
aggregated to the venue by dollar-hour weight. Ratchet caps live at the venue; kill decisions at (m,s).

1.2 **Liquidity horizon, measurable — all quantities in HOURS.** `SETTLE_LAG_H = 0.7` (R171: 41 min).
```
T_settle(m) = max( SETTLE_LAG_H , (close_ts − now)/3600 + SETTLE_LAG_H )
L_shed(m,s) = median hours open→flat via maker shed, trailing 20 completed sheds; unmeasured ⇒ ∞
L_eff(m,s)  = max( SETTLE_LAG_H , min( T_settle , L_shed ) )
PAST DUE (now > close_ts + SETTLE_LAG_H and no settlement observed):
    L_eff ← max( L_eff , 2 × hours_past_due )          # escalates monotonically, never shrinks
```
**The floor and the escalation are BLOCKER-grade, not polish.** Without the floor, `close_ts − now` turns
negative after close, `carry_cost` turns negative, and the model ADMITS a venue *on the strength of being
stuck* — the PayPal failure with the sign flipped. Nothing is ever liquid faster than the settlement lag, so
`SETTLE_LAG_H` is the floor. Past due, the remaining wait carries no information; the no-information bound
is that expected remaining wait grows at least linearly in elapsed overdue time, so `2 × hours_past_due`
escalates carry monotonically and a stuck market is progressively excluded, never progressively favored.
`L_shed` unmeasured ⇒ ∞ (so `L_eff = T_settle`) is the only default consistent with "no cap may assume
settlement bails it out."
**Hard horizon exclusion (both sides in hours — the type error matters):** let
`H_prog = (program_end_ts − now)/3600`. Exclude the venue if `T_settle > H_prog + 24` unless ratchet rung ≥ 2
(§1.4). Derivation: past program end, inventory carries with ZERO offsetting accrual — the exact PYPL
geometry; the 24 h grace covers same-day-after settlement. The `+24` is UNDERIVED (§9.4).

1.3 **Score used by ALLOCATE.** Replace v1 §2.2's hurdle line with (★). Water-filling, step, budget reserve,
caps and the per-program forfeit gate are **inherited unchanged from v1 §2.4-2.7 / §3** (they survived
adversarial fire and re-derive identically under (★)).
**The `r*` fixed point, specified (it is circular: `r*` prices carry, carry decides the allocation, the
allocation sets `r*`).** Precedent: v4's budget-reserve fixpoint.
```
r*_0 = max( trailing-7d achieved marginal rate , λ_min/16 )        # never seed below the floor
repeat k = 1..4:  A_k := ALLOCATE(r*_{k-1});  r_new := marginal rate of A_k at its stopping point
                  r*_k := 0.5·r*_{k-1} + 0.5·r_new                 # damped, prevents 2-cycles
                  stop when |r*_k − r*_{k-1}| / r*_k < 0.05
non-convergence after 4 iterations: use max(r*_0..r*_4), log `rstar_no_converge`, alert if 3 cycles in a row
```
Derivation of the tie-break: a HIGHER `r*` prices carry higher, admits fewer venues, and allocates less —
the conservative direction, and the one that fails toward the PayPal lesson rather than away from it. 4
iterations because damped iteration on a monotone scalar map halves the residual per step, so 4 covers a 16×
seed error; 5% because ALLOCATE's own step resolution is 2% and chasing below its own noise is theatre.
**Cold start:** on truly empty history `r*_0 = λ_min/16` — seeding low makes carry look cheap, which is the
PayPal direction; the §1.4 unverified cap is what bounds the damage in that one case.

1.4 **Verified-accrual ratchet.** Per venue, state `{rung, cap_usd, last_verify_ts, verify_history}`.
`floor_q(v)` = the smallest allocation whose projected payout over the program period clears
`ENTRY_FLOOR = $2.00`. **A probe smaller than `floor_q` measures nothing** — it cannot pay, so its
non-payment is not evidence about the venue. That fact forces the probe rule below; a naive
`min(floor_q, 0.02×ceiling)` is self-contradicting (it funds probes that are structurally unable to verify)
and must not be written.
```
rung 0: cap_usd = min( floor_q(v) , INV_CAP_USD/p per slot , per-market cap §8.2 )
        ADMIT the venue at rung 0 iff  Σ unverified exposure + cap_usd ≤ 0.20 × global_ceiling
                                  AND  count(unverified venues) < N_UNVERIFIED_MAX = 8
        if floor_q(v) > 0.02 × global_ceiling: the venue is OVERSIZED-PROBE — still admissible, but it
              consumes an oversized-probe slot (≤2 concurrent) and is logged `probe_oversized`
        if it cannot be admitted now: QUEUE it (ranked by net(0)); never shrink the probe below floor_q
rung k: cap_usd = min( 2^k × rung0_cap , per-market cap §8.2 , global ceiling )
VERIFY   (+1): popover_estimate or paid credit for a program in this venue whose ratio to the model's
               projection over the SAME window lies in [0.5, 2.0]
DISAGREE (−2): same reading outside [0.5, 2.0] — **only if the projection was ≥ ENTRY_FLOOR**
OUT OF REACH:  a reading on a program whose projection was < ENTRY_FLOOR ⇒ neither VERIFY nor DISAGREE;
               log `venue_out_of_reach`, hold the rung, and stop funding that venue this period
STAND DOWN (venue, not bot): DISAGREE on 2 consecutive settlement days  [charter §5]
```
Derivations: the **20% total / 8 concurrent / ≤2 oversized** bounds replace the per-venue 2% as the binding
constraint, because the per-venue number cannot be allowed to override the floor-clearing size without
destroying the verifier. At a $300 ceiling that is ≤$60 unverified at once — under four PayPal incidents'
worth, and each one measurable. **Up-1/down-2:** a false up-step costs capital at a venue that does not pay;
a false down-step costs only rate at a venue re-verifiable tomorrow. Expected drift per reading with verifier
accuracy `a` is `a·(+1) + (1−a)·(−2) = 3a − 2`, so **`a = 2/3` is the ladder's characteristic number: a
verifier worse than 2/3 accurate can never climb, and a coin-flip verifier (a=0.5) drifts to rung 0** (tests
T-R3, T-R7). **[0.5, 2.0]** is the system's own declared model tolerance (v1 §3.1, §12.3a) — self-consistent
by construction, UNDERIVED as a measured distribution (§9.4).
**MIRROR (ratchet up ↔ ratchet down ↔ revive):** a killed (m,s) or stood-down venue re-probes ONLY at a NEW
program period AND only if the 95% upper bound on its own `T̂` posterior clears the hurdle (§2.5). Memory is
retained across periods; nothing revives on a timer.

1.5 **Dose-response sizing (charter §3).** Each cycle, perturb allocation on ≥3 slots to `{0.5×, 1×, 2×}` of
the water-filling optimum, chosen deterministically by `hash(ticker,side,period)` so the panel is stable
within a period. **Perturbation budget: the total modeled rate given up must be < 2% of the modeled
portfolio rate** — derived, not chosen: ALLOCATE's own step is 2% of budget (v1 §2.5), so the allocation is
only accurate to 2% anyway and information bought inside that resolution is free. Slots on flat rate curves
therefore get large perturbations and steep ones get none, automatically.

## 2. PRESENCE-SECONDS PER DOLLAR-HOUR (the health metric — charter §2)

2.1 **Metering, exact.** A monotonic 1 Hz tick, on a FIXED phase, independent of the quoting loop (mirror:
sampling right after a requote biases `at_best` upward — the sampler must never be triggered by our own
action). Per (m,s) per tick, accumulate:
```
rest_dollar_s += Σ_live_orders( remaining × price )                     # $·s  resting capital
prox_dollar_s += Σ_live_orders( remaining × price × 0.5^ticks_behind )  # $·s  the objective's own term
inv_dollar_s  += |net_position| × entry_basis                           # $·s  capital that is NOT resting
at_best_s     += 1 if any live order at same-side best else 0
```
`ticks_behind` = (same-side best − our price) in cents, from the WS book at that tick.

2.2 **Ledger row (v5 ledger, §6.2), one per (m,s) per 60s:**
`{"t":"presence","ticker","side","from_ts","to_ts","rest_dollar_s","prox_dollar_s","inv_dollar_s",
"at_best_s","ticks_ct","fills_ct","fill_notional"}` — deltas, never cumulative, so replay is a sum and a
crash loses ≤ 60s. Volume: 32 markets × 2 sides × 60/h ≈ 92k rows/day ≈ 14 MB/day.

2.3 **The metric.** Over any window W:
```
PSDH(m,s) = Σ prox_dollar_s  /  [ Σ (rest_dollar_s + inv_dollar_s) / 3600 ]      units: seconds per hour
T̂(m,s)   = clip( PSDH / 3600 , 0, 1 )
```
`PSDH ∈ [0, 3600]`; 3600 = every committed dollar resting at best every second. **`T̂` needs no threshold
constant: it is exactly the fraction of modeled presence actually realized, so `T̂·gross` in (★) is the
realized rate and the water-level `λ_min/16` is already the kill threshold.** PYPL's geometry (capital
converts to inventory on contact) drives `inv_dollar_s` up and `prox_dollar_s` to zero ⇒ `T̂ → 0` within
hours, without any settlement data.
Shrinkage for thin data: `T̂ = (Σprox + 3600·k·T₀) / (Σcommitted_h·3600 + 3600·k)` with pseudo-weight
`k = 2` dollar-hours and prior `T₀` = the series' own dollar-hour-weighted median, else the portfolio median,
else 0.5 (UNDERIVED, §9.4 — it affects probe ORDER only, never exposure, because rung-0 caps bind first).

2.4 **`φ` and `d` from the same tape.** `φ(m,s) = fills_ct / Σ(rest_contract_hours)`. **Zero-fill venues use
the Rule of Three: `φ_ub = 3 / (Σ rest_contract_hours)` at 95%** — this replaces v1's guessed seeds
(`PHI_MID=0.08`, `PHI_CHEAP=0.001`) with a bound that is correct on day one and tightens with evidence. Seeds
remain only as the ceiling on `φ_ub` at zero exposure. `d(m,s)` = mean of `(mark_at_fill+Δ − fill_price)`
over the trailing 20 fills, measured at Δ = 60s (the horizon at which v3's 5-9¢ cross-cycle drift was
observed); unmeasured ⇒ `d = $0.07` capped at `p` (v1, inherited, still UNDERIVED §9.4).
**Decisiveness:** an estimate is decisive when `fills_ct ≥ 10` (relative s.d. 1/√10 = 32%, resolves a 2×
difference at ~2σ) OR `Σ committed_h ≥ 2` with zero fills (Rule-of-Three bound already below the hurdle).

2.5 **Automatic size-down and kill (charter §2 "sized down or killed automatically").**
Evaluate every 15 min per (m,s) with `ζ = net(q_current) / (λ_min/16)`:
```
ζ ≥ 1.5  and venue verified          → eligible for ratchet up (§1.4)
1.0 ≤ ζ < 1.5                        → hold
ζ < 1.0  for 3 consecutive evals AND the estimate is decisive (§2.4)   → KILL (m,s) for this period
PSDH == 0 with fills_ct ≥ 1 and Σcommitted_h ≥ 2                       → KILL immediately, no model
```
Size-DOWN is not a separate action: ALLOCATE re-runs each cycle and a falling `T̂` moves the dollars by
itself. **Note PSDH is scale-invariant** (numerator and denominator both ∝ q), so shrinking size cannot
"fix" a toxic venue — the only correct response is reallocation, which is what the water level does.
The 3-eval hysteresis (45 min) is the shortest interval that cannot be tripped by one fill burst inside one
15-min bucket; the second rule needs no model because zero presence is zero objective.
**MIRROR (kill ↔ revive):** §1.4's revive predicate. **MIRROR (per-slot kill ↔ book-wide kill):** §8.3.

## 3. RATE-BUDGET SCHEDULER (charter "derives fresh" #2)

3.1 **Budget.** Shared account, ~10 req/s observed limit (429s at 05:58Z on 2026-07-27 with nestor + LIP
pollers). v5's bucket `B = 4.0 req/s` steady. Derivation of the split: nestor's calls are trade-critical and
un-deferrable (signal → order); v5's are presence-maintaining and deferrable (the WS carries the book). Under
a shared constraint the deferrable consumer takes the residual, not half. `B` is a token bucket, capacity 8
tokens (2 s of burst — one requote round-trip).

3.2 **AIMD, because the constraint is shared and invisible.** On ANY 429 (ours or another process's, since we
cannot tell): `B ← B/2`, log `rate_yield`, and hold 60 s; then `B ← min(4.0, B×1.25)` every 60 s.
Multiplicative decrease guarantees we yield faster than we take, which is the charter's "degrade breadth
before degrading another bot's calls" expressed as a policy rather than a hope. The 1.25/60s recovery is
UNDERIVED (§9.4); the FORM is derived.

3.3 **Lanes and priority** (strict priority queue drawing from one bucket — a partition would waste the
reserve when idle): `exit_cancel > requote_cancel > place > verify(fills,positions,balance) > book_poll >
classify_sweep`. **The bucket refuses to fall below 1 token for any lane except `exit_cancel`** — a rate
budget must never be the reason an order cannot be cancelled.
**Cancel-lane bound (SF-1).** An unbounded preempting lane is a starvation weapon: a requote loop stuck in a
cancel/replace oscillation would consume the whole bucket at top priority and silently stop every other
function, which looks exactly like a dead bot. Bound: **cancels ≤ 25% of admitted requests over a rolling
60 s**. Over that share, `requote_cancel` degrades first (its slot falls back to leaving the resting order in
place until the next tick — a stale quote, which is a rate loss, not a risk); **`exit_cancel` is never
degraded and is never counted against the bound** (flatten, day-stop, T3 close sweep, poison cancel-all).
25% = one cancel per requote round-trip (place+cancel+verify+poll ≈ 4 requests) — above that the loop is
oscillating by definition. Log `cancel_share_exceeded` with the offending (m,s); 3 in 10 min ⇒ poison it.
MIRROR (over-reserving): the headroom is a priority floor, not a partition, so an idle exit lane costs zero.

3.4 **Degrade order, derived by marginal objective cost per request saved** (cheapest first):
1. classify sweep 5 Hz → 1 Hz (pinned-ness changes on a 15-min timescale; v4 `CLASSIFY_REFRESH_S=900`)
2. book polls on markets whose WS book is fresh and gate-passed (strictly redundant)
3. breadth: drop the LOWEST-`net`-rate markets from the quoting set (by construction the smallest objective
   contribution) — cancel-all on the dropped ones first
4. book-poll cadence on WS-less markets 1 Hz → 0.5 Hz (costs ≤0.5 s of coverage per requote)
5. positions/recon poll 600 s → 1800 s — **never dropped**; it is the truth-reader
6. NEVER degraded: cancels, T3 close sweep, day-stop flatten, the cash-feed write (local, no request)

3.5 **WS-first (inherit v4's `ws_feed.py` on its merits).** Kept verbatim: the 3-agreement gate before a WS
book may price anything, 60 s re-proof, `unit_mismatch` naming of a dollars/cents slip, per-market fallback
to REST on stale/gapped/corrupt, breadth 6 → 32 only while connected. REST is used ONLY for: startup
snapshot + gate, order place/cancel, verification (fills/positions/balance), and fallback polls.

## 4. QUOTING ENGINE

4.1 **WS-driven at-best maintenance.** Requote triggers are book events, not timers (v1 §4.3 (a)-(e) kept,
with (e) `SAFETY_RESYNC_S = 60` doubling as the WS re-proof). **Make-before-break kept** (v1 §4.1) with the
automatic cancel-first degrade at `T* = 46 s` on any insufficient-balance reject (v1 §4.2), and the §2.4
budget reserve that makes MBB affordable.

4.2 **Whole-second resting.** The exchange's snapshot cadence and phase are nonpublic; assume ~1/s. The
probability a coverage gap of `g` seconds costs a snapshot is `min(1, g)`. **MBB has `g = 0`, so whole-second
resting is satisfied by construction and is a POLICY only in the degraded path**, where: never voluntarily
cancel-and-replace inside the same integer second as the placement, and align requotes to the tick so the
gap is bounded by the round trip. `MIN_RESTING_LIFE_S = 30` (v1 §4.4, anti-gaming P1) kept; trigger (a)
overrides it.

4.3 **Per-venue shading (charter §4), derived — no constant.** Let `φ_k` = measured fills/h/contract at
distance `k` ticks behind best (both are observed because §1.5's panel and the degraded path put us at k=1
sometimes). Score at k=1 is exactly `DF = 0.5` of score at k=0, so our normalized share becomes
`0.5q/(0.5q+S)`. **Shade to k=1 iff**
```
(ρ/2)·[0.5q/(0.5q+S)] − φ₁·q·(d + p·L_eff·r*)   >   (ρ/2)·[q/(q+S)] − φ₀·q·(d + p·L_eff·r*)
```
i.e. iff the halved score is worth less than the avoided adverse selection AND carry. Seed for unmeasured
`φ₁`: `φ₁ = φ₀ × P(trade size ≥ depth at best)` from the public trade tape joined to our own book snapshots —
a measurement, not a guess. **Never consider k ≥ 2**: score ≤ 25% and those dollars beat it at the water
level in another venue. Log `shade_decision` per slot per cycle with both sides of the inequality.

4.4 **Inherited caps and guards — each with its MIRROR answered** (note 23 §IV; these ship as comments):
| guard (source) | mirror | who guards the mirror |
|---|---|---|
| net inventory cap `$10/slot` (v1 §8.1) | per-slot ↔ per-VENUE net | **NEW: `cap_series = max(INV_CAP_USD, 0.5 × day_stop_threshold)`** — no single venue may trip the global day stop alone, else one venue halts the whole book, contradicting charter §5 |
| window END guard + `expiration_ts` (v4) | window START | v4's window-start guard, kept: never quote a program before `start_date`, never a market before it is `active` |
| program window | market trading window | quote iff program live AND `status==active` AND `now < close_ts − 240 s` |
| make-before-break | break-before-make | automatic degrade at 46 s + `mbb_degraded` ledger row |
| ENTRY_FLOOR (entry) | exit | the three-way KEEP/TOP_UP/HOLD/ABANDON (v1 §3.5-3.7), kept verbatim |
| cash-feed spend | refund/credit | `pending_payout_dollars` widens only the positive side (§5.2) |
| write path | replay path | presence accumulators are deltas; replay parity test T-P2 |
| ratchet up | ratchet down / revive | §1.4 down-2 + new-period revive predicate |
| rate ceiling | rate floor | degrade step 3 sheds markets rather than holding all of them badly |
| `assume_filled` freeze on quoting | freeze on RECYCLING | v1 §9.4b kept verbatim — a quote-only freeze is a naked-short generator |
| day stop (loss) | day stop (win) | none needed — mirror considered: a large positive divergence is the settlement/credit path and is covered by §5.2's pending widening; document it beside the constant |
| AIMD decrease (yield) | AIMD increase (reclaim) | **NEW:** floor `B_min = 0.5 req/s` (below it we cannot hold even one market) and alert `rate_starved` if `B < 0.5×cap` for 10 min — silent permanent yielding is indistinguishable from a dead bot |
| unverified-exposure ceiling | exploration FLOOR | **NEW:** if unverified exposure < 5% of ceiling while the §1.4 queue is non-empty, admit the next queued venue. A cap on learning is a cap on earning (Ryan's capital corollary: boundedness never answers "why is this dollar here instead of where it earns") |
| sampler bias UP (sampling just after a requote) | bias DOWN (sampling inside a coverage gap) | fixed monotonic 1 Hz phase, never triggered by our own actions, asserted jitter < 100 ms — one guard kills both directions |
| kill for too MANY fills (we are the fish) | kill for ZERO fills ever (decorative book) | **v1 §10.3-P6 carried forward verbatim:** zero taker fills over 5 consecutive program-days ⇒ drop; plus the pre-entry filter on 5 days of public tape |
| v5 stops PUBLISHING the cash feed | nestor stops CONSUMING it | **NEW:** v5 reads nestor's `LIP_CASH_FEED_ENABLED` at startup; `mode:"shared"` with the reader disabled is a **STARTUP REFUSAL** — an unconsumed feed is a silent regression to the hand ledger |
| adopt too MUCH at cutover | adopt too LITTLE (orphaned live positions) | **NEW:** every exchange position not adopted is enumerated as `orphan_position`, alerted, and its market is refused for quoting (v4's inventory-slot guarantee, inverted) |
| day stop (losing money) | idle capital (losing nothing, earning nothing) | **NEW:** alert `idle_capital` when committed > 50% of ceiling while book-wide `net < λ_min/16` for 1 h |
| ratchet raises venue caps | Σ venue caps vs the global ceiling | ALLOCATE's budget binds: Σ caps MAY exceed the ceiling, Σ *allocated* never does (test T-R4b) |
Also inherited verbatim because they survived fire: poison rules (v1 §8.5), never-trust-the-indexes (§8.6),
coid stability with no run-id (§9.5), closing-room netting + closing exemptions, ledger-replay restart with
schema-mismatch abort, `NTFY_DISABLE` honored by construction, detect-and-page defaults, W2 trust gate for
any new sensor, staged human gates for spending paths.

4.5 **S = 0, qualification and revival — stated explicitly (N1), because (★) degenerates there.** At `S = 0`
`gross(q) = ρ·S/(2p(q+S)²) = 0`: with no rivals our marginal rate is exactly zero (we already own 100% of the
side), so **ALLOCATE correctly assigns an empty book ZERO** and would never enter one. That is right about
size and wrong about entry, because qualification is a DISCRETE PRECONDITION, not a rate: if either side
fails `target_size_fp`, the snapshot is EXCLUDED and *nobody* is paid, us included. Therefore qualification
is handled as a constraint outside the water-filling loop, exactly as v1 §6.1-6.2 derived:
```
for each candidate market, per side, before ALLOCATE:
  if not qualifies(side) and a legal price exists (not PINNED):
      post  max( target_size_fp − cum_size , min q clearing ENTRY_FLOOR )  at the cheapest legal price
      subject to: P7 revival caps (≤3 concurrent revival markets; never >90% of a qualifying side for >5
      consecutive days), LAND_GRAB_MAX_COLLATERAL_FRAC = 0.25 of budget, and the §1.4 rung-0 cap
  then run ALLOCATE on the resulting book, in which S > 0 and (★) is well-defined
```
**Do NOT size up into an empty book** (v1 D2): at `S≈0`, `share = q/(q+S) ≈ 1` for any `q`, so extra size buys
no share — only fill risk and carry. The minimum qualifying size is the maximum of the objective.

4.6 **Anti-gaming carried forward, and P5 re-priced under (★) (SF-3).** v1 §10.3 **P1** (honorable quotes:
real collateral, no cancel-on-approach, 30 s minimum resting life, make-before-break so we never withdraw
liquidity to dodge a taker), **P3** (two-sided at the PORTFOLIO level: ≥40% of resting collateral and ≥⅓ of
touched markets quoted both sides, excluding pinned/shedding), **P4** (fill-honoring ratio ≥0.95, investigate
<0.90), **P6** (revealed usefulness, pruner + pre-entry filter — see §4.4's mirror row), **P7** (revival
caps), **P8** (one account, ever), **P9** (the one-sentence test) carry forward **verbatim**. They are
restrictions and measurements; they cost <5% of modeled reward and they are the compliant posture under
either answer to the open anti-gaming question.
**P5 (a cheap-side share cap) stays DELETED, and v5 must own that this is now a LARGER exposure than v4's.**
(★) tilts further cheap: in §0.4's own numbers the gas cheap side beats the treasury side **28.4×** on `net`
versus **25.0×** on `gross` alone, because the drift term is `φ·d/p` with `d` capped at `p` — the `1/p` law
arriving a fourth time, through the cost side. Priced as v1 §10.1 priced it: the program dies 2026-09-01
(verbatim in the filing) ≈ **34 days**; remaining program EV **$3.4k–$8k with $8k the TOP of the range**, plan
against $3.4–5k; each +10 percentage points of revocation probability costs **$340–$800**. Reinstating a 60%
cheap-side cap costs roughly half of total reward = **$50–150/day = $1.7–5.1k**, i.e. **21–150 percentage
points** of revocation-risk reduction — more than exists to buy. A cap on legal, collateral-backed, hittable
quotes cannot plausibly purchase that. **Both numbers print in the run log every cycle so the tradeoff is
never made implicitly**, and `cheap_side_score_pct > 95% for 3 consecutive days` pages a human — an alert,
never a block. One genuine offset worth naming: (★)'s horizon term concentrates us into short-dated dailies,
which raises per-market share concentration and therefore makes **P7's caps bind MORE often** than in v4 —
the visible-making constraint tightens automatically as the cheap-side tilt increases. This paragraph is the
first thing to overrule if Ryan's risk appetite differs (v1 D5, unchanged in direction, larger in magnitude).

## 5. THE COMPUTED CASH FEED (charter "derives fresh" #1 — zero hand entries)

5.1 **Why a second file.** nestor's breaker SUMS every line of `data/external_cash.jsonl`
(`reconcile.rs:848`). A cumulative computed value appended there would double-count against the operator's
hand rows, and two writers on one file is a collision (note 23 §III #4). v5 therefore owns
**`data/lip_cash_feed.json`** — a SINGLE JSON object, rewritten atomically (temp + `rename`) — and nestor
gains one function that reads it and adds it to `(ext_cash, ext_pending)`. `external_cash.jsonl` survives
untouched for non-LIP flows (deposits, FOMC strangle).

5.2 **Schema (v5 writes; nestor reads).**
```json
{"schema":"lip_cash_feed/1","ts":1785216708.4,"seq":41207,"process":"lip_v5","pid":33912,
 "mode":"shared",                              // "shared" | "subaccount"
 "delta_dollars":-247.13,                      // ADD to nestor's expected_cash
 "pending_payout_dollars":31.40,               // widens the POSITIVE side only
 "components":{"resting_collateral":183.42,"inventory_basis":48.60,"settled_awaiting_payout":17.50,
               "realized_pnl":2.39,"fees_paid":0.00,"rewards_accrued_unpaid":6.94,
               "inventory_settle_max":18.20,"settled_payout_expected":6.26},
 "ceiling_usd":300.0,"max_inflight_usd":12.00,"heartbeat_s":30}
```
`delta_dollars = −(resting_collateral + inventory_basis + settled_awaiting_payout) + realized_pnl − fees_paid`
`pending_payout_dollars = rewards_accrued_unpaid + inventory_settle_max + settled_payout_expected`
(`inventory_settle_max = Σ n × $1.00` over UNSETTLED inventory — the largest credit that could land
unannounced; `settled_payout_expected` = the known payout of already-resolved-but-unpaid positions).

5.2a **`settled_awaiting_payout` — release on CASH CONFIRMATION, never on result (BLOCKER-1).** When a
market resolves, its basis moves from `inventory_basis` into `settled_awaiting_payout` and **stays inside
`delta_dollars` as consumed cash.** It is released only when the credit is confirmed *in cash*: v5's next
balance read (verify lane) shows an increase ≥ the expected credit, or a `/portfolio/settlements` row
carrying the paid amount. Derivation: R171 measured a **41-minute** settlement-index lag. Releasing on result
raises v5's published expected-cash before the real dollars land, and nestor's breaker reads exactly that as
MISSING MONEY — **v5 would halt nestor through the very interface built to stop v5 halting nestor.** The
sign is the same as the four hand-patched halts; only the author would have changed.
Timeout: still unreleased after 6 h ⇒ page `settlement_cash_unconfirmed`; **never auto-release on a timer**.
**MIRROR (released too late):** a lingering entry only makes v5 look poorer than it is — the safe direction,
which is why the timeout pages instead of releasing, and the 6 h bound (≈9× the 41-min observed lag) keeps a
genuinely stuck settlement visible rather than silently shrinking the book.

5.3 **Cadence, derived — write BEFORE the wire call.** The feed is written (and fsync'd) with the pending
order's collateral already included **before** any cash-consuming POST, and corrected after the response,
plus a 30 s heartbeat. Consequence: **v5's published expected-cash is never above the truth, only below** —
so the breaker's negative side (missing money = the dangerous direction) stays tight and v5 can never cause
a false halt. Cost: one fsync+rename (~1 ms) per POST against a ~100 ms HTTP call. This is the derived answer
to four hand-patched halts in 24h.

5.4 **Staleness guard (nestor side).** If `now − ts > 120 s` (4 × heartbeat: survives one miss plus jitter):
page `lip_cash_feed_stale`, KEEP using the last value (it is conservative by §5.3), do NOT halt. Halting on a
stale feed would convert v5 dying into nestor dying. **MIRROR (stale ↔ absent):** an absent file is
`(0,0)` — correct only if v5 is truly flat, so v5's SIGTERM path writes a final zeroed feed AFTER cancel-all
+ shed, and only then may the file be removed. A `-9` kill leaves the last conservative value plus the
staleness page.

5.5 **Subaccount-ready.** `mode:"subaccount"` publishes `delta_dollars: 0.0, pending_payout_dollars: 0.0`
while still publishing `components` and the heartbeat. The wall replaces the feed, but the staleness monitor
(hence the alarm chain) survives cutover — a silent disappearance of the alarm at the moment of an
account-structure change is exactly the class we keep paying for.

## 6. STATE, LEDGER, CUTOVER FROM v4

6.1 Paths: ledger `~/nestor/data/lip/v5_ledger.jsonl`, coid prefix `v5-`, seq `v5_coid_seq`, recon
`v5_recon.jsonl`, cash feed as §5.1. **v5 never writes any v4 path** — that is what makes rollback one
command. Startup refuses if a v4 heartbeat is fresh (< 120 s) in `~/nestor/data/lip/` — two makers on one
rung is self-trade plus double collateral.

6.2 Ledger vocabulary = v1 §9.1 (`place_req/place_resp/cancel_req/cancel_resp/fill_obs/snapshot`, normalized
fill vocabulary, schema-mismatch abort) PLUS `cash_feed` (each published seq), `rate_yield`, `ratchet`,
`venue_kill`, `venue_out_of_reach`, `shade_decision`, `orphan_position`. Restart procedure, 404
disambiguation at 36 s, the crash-gap fills window and `assume_filled` are inherited verbatim from v1
§9.4-9.4b.
**`presence` rows live in their OWN file (N2):** `v5_presence.jsonl`, rotated daily, never in the order
ledger. Two derivations force the split: (a) 14 MB/day of metering would be replayed on every restart by a
path that needs none of it, lengthening the one procedure that must be fast and correct; (b) the order
ledger is the money record and must stay append-only forever, whereas metering must be compactable.
**Compaction:** a daily step folds rows older than **7 days** into per-(m,s)-per-day aggregates in
`v5_presence_daily.jsonl` and deletes the folded segment file. 7 days is derived, not chosen: it is exactly
the trailing window `T̂`'s shrinkage and §8.7's collapse-median require; nothing reads finer-grained history
than that. Compaction never rewrites a file in place (write the aggregate, fsync, then unlink the segment) —
a metering record that can be silently rewritten is a metering record that cannot be trusted.

6.3 **Cutover — three options, one recommendation.**
- **A. Cold.** v4 SIGTERM (cancel-all, proven) → wait for its inventory to settle → v5 starts flat. Zero
  import risk; costs hours of presence and idle dollars on multi-day inventory.
- **B. Ledger import.** v5 replays v4's ledger through a shim. Fastest, but v4's ledger carries known
  divergences from the buggy hours (2026-07-28 morning report), and an import bug manufactures phantom
  inventory — the class that produces real naked shorts.
- **C. RECOMMENDED — hot handoff with a W2-gated adoption boundary.** v4 SIGTERM cancel-all (orders gone,
  the proven path). v5 does NOT replay v4's ledger into its own state. Instead:
  1. **v5 GENERATES the adoption file itself** — `lip_v5 --gen-adopt` reads v4's ledger **read-only** and
     writes `~/nestor/data/lip/v5_adopt.json` = `{ticker, side, net, basis}` per market. This is an owned,
     testable, re-runnable step, **not a hand entry** (the charter's "zero hand entries" applies to the
     cutover too — a hand-typed position table is the highest-stakes hand entry in the whole program).
  2. **W2 trust gate at startup:** cross-check against `GET /portfolio/positions`. The exchange is
     authoritative on `net`; v4's ledger is authoritative on `basis`. Any market where `net` disagrees is
     EXCLUDED from adoption and marked `assume_filled` (frozen for quoting AND recycling).
  3. **Basis sanity, because a bad basis silently mis-sizes every later cap:** accept `basis` only if
     `0.01 ≤ basis ≤ 0.99` AND `basis ≤ 2 × current mark`. Violation ⇒ exclude + freeze that market, log
     `adopt_basis_rejected`. (A ledger-era basis of $0.00 or $1.50 would otherwise make `inv_dollar_s`,
     `INV_CAP_USD` and the cash feed all wrong in the same direction at once.)
  4. Adopted positions may be shed but seed no new quote until the first clean recon pass; every exchange
     position NOT adopted is logged `orphan_position`, alerted, and its market refused for quoting.
  Downtime ≈ minutes. Cost over B: one generated + reviewed file; over A: nothing.
- **Rollback, with its honest boundary (SF-2).** `v5 SIGTERM` (cancel-all → zeroed cash feed) →
  `systemctl start lip-maker-v4`. **This is clean ONLY before the first fill on an adopted position** — after
  that, v4's ledger no longer describes reality and restarting v4 on it re-imports a stale world. Therefore
  v5's SIGTERM path ALWAYS writes `~/nestor/data/lip/v5_handback.json` — a v4-readable position statement
  `{ticker, side, net, basis, source:"v5", ts}` covering every position v5 holds — and past the boundary the
  rollback procedure is "start v4 with `--import-handback`", not "start v4". v5 logs
  `rollback_clean=true|false` on every cycle so the operator never has to guess which regime they are in.

## 7. HUMAN GATES (R186 — each is a separate call, one decision, with its own rollback)

| gate | decision | owner | command / read-out | rollback |
|---|---|---|---|---|
| **G0 nestor reader** | patch `reconcile.rs` to add `lip_cash_feed.json` to `(ext_cash, ext_pending)` — **ships behind `LIP_CASH_FEED_ENABLED`, default FALSE (IGNORE), and takes its OWN review gate before the flag is ever flipped** | **Ryan** (flag); separate review owns the patch | with the flag false, `divergence` is byte-identical to today across ≥1 reconcile pass; with it true, `expected_cash` moves by exactly the feed's `delta_dollars` | flag false (a one-line revert; the reader is inert code until then) |
| G1 arm inert | deploy binary, `--check` only, no capital | Fable | `--check` prints OK for unit assertion, ledger replay, data dir, cash-feed write, WS gate, **and that G0's flag state matches v5's `mode`** (§4.4 mirror — `shared` + reader-disabled is a startup refusal) | delete unit |
| G2 shadow | quote nothing for ≥1 full program period; meter PSDH, score venues, publish a zeroed cash feed | Fable | `venue_rank` lines vs v4's realized accrual; PSDH populated for ≥10 (m,s) | stop |
| G3 probe capital | rung-0 caps live: floor-clearing probes, ≤20% of ceiling unverified in total, ≤8 concurrent, ≤2 oversized | **Ryan** | first `allocate` line: `Σ unverified ≤ 0.20×ceiling`, `count(unverified) ≤ 8`, `count(probe_oversized) ≤ 2`, and **no venue funded below its `floor_q`** | SIGTERM |
| G4 ratchet enable | allow caps to climb on verified accrual | **Ryan** | `ratchet` rows show only `+1` on in-band verifications | flag false |
| G5 ceiling rung | each rung funded by the PREVIOUS window's observed print, never the model (R168) | **Ryan** | one constant, one commit | previous rung |
| G6 taker-exit | enable the spending exit path | **Ryan** | v1 §5.2 inequality logged before first exit | flag false |
| G7 subaccount | cash feed → `mode:"subaccount"`; key-capability probe first (GTC + `expiration_ts` + coid cancels, one $1 order) | **Ryan** | probe passes before any capital moves | mode shared |
| G8 v4 decommission | stop and disable v4, **and zero the v4-era rows in `external_cash.jsonl`** (N3) | **Ryan** | after 3 clean v5 settlement days AND v4 verified FLAT (zero positions, zero resting — the rows offset cash v4 had CONSUMED, so zeroing them while it still holds inventory creates a false positive divergence): append one offsetting entry equal to `−Σ(v4-era delta_dollars)` with a note naming the rows it cancels, then verify `divergence ≈ $0.00` on the next reconcile pass | restart v4; the offsetting entry is itself reversed by another append |
No gate bundles a capital change with a code change. No shared-tree builds — worktree only; native aarch64
built on the VPS (`file` the artifact against `uname -m`).

## 8. TEST PLAN — money rules as pure functions, no network in any test

8.1 **`net_rate(ρ,S,p,q,φ,d,L_eff,r*,T̂)`** — T-N1..N3 reproduce §0.4's three rows to 1e-3 **with `d` capped
at `p`**: PYPL −11.80 (and `H` clips to 0), treasury +0.110, gas-cheap +3.12. T-N4: `L_eff` doubling halves
the horizon multiplier's headroom exactly (`carry` linear in `L`). T-N5: at `q=0` the function is finite (no
division by zero — v1's B4 defect must not return). **T-N6 (B2):** `now = close_ts + 3 h` with no settlement
⇒ `L_eff = 6 h` (2× past-due), carry STRICTLY GREATER than at `close_ts`, and `L_eff ≥ SETTLE_LAG_H` at every
input including `now ≫ close_ts`; assert `carry_cost > 0` always — **a negative carry must be unreachable.**
**T-N7 (SF-8):** the `r*` fixpoint converges in ≤4 damped iterations on a 16× seed error; a constructed
oscillating book hits the iteration cap and the allocation uses `max(r*_0..r*_4)`, logging `rstar_no_converge`
— assert the non-converged run allocates ≤ the converged run (conservative direction).
**T-N8 (N1):** `S = 0` ⇒ ALLOCATE returns qty 0 for that slot, AND the qualification path supplies
`target_size_fp − cum_size` at the cheapest legal price, bounded by P7 and the 0.25 land-grab fraction.
8.2 **`psdh(rows)`** — T-P1: 60 min at $100 resting at best, no inventory ⇒ 3600 s/h, `T̂=1`. T-P2 replay
parity: shuffled/split `presence` rows sum identically. T-P3: $100 resting 1 min then $100 inventory 59 min
⇒ PSDH = 60·60/(100·60/3600·... ) computed exactly in the test, `T̂ ≈ 0.0167`; assert KILL fires under §2.5's
zero-model rule variant. T-P4 scale invariance: 10× all sizes ⇒ identical PSDH. T-P5: one tick behind best
weights 0.5 exactly.
8.3 **`ratchet(state, reading, model)`** — T-R1 in-band ⇒ +1 and cap doubles; T-R2 out-of-band ⇒ −2, floor at
rung 0; T-R3 **coin-flip verifier over 1,000 alternating readings ⇒ terminal rung 0** (the asymmetry proof);
T-R4 caps never exceed `min(per-market cap, ceiling)`; **T-R4b** Σ venue caps MAY exceed the global ceiling
while Σ *allocated* never does; T-R5 two consecutive out-of-band settlement days ⇒ venue STAND_DOWN and every
other venue keeps quoting. **T-R6 (B3):** a venue whose `floor_q` exceeds 2% of the ceiling is admitted at
`floor_q` (never shrunk below it) while unverified totals allow, and consumes an oversized-probe slot; a
reading on a program whose projection was **below** ENTRY_FLOOR yields **neither VERIFY nor DISAGREE** — the
rung is unchanged and `venue_out_of_reach` is logged. Assert a venue can never be stood down by a probe that
could not have paid. **T-R7 (SF-7):** verifier accuracy 2/3 ⇒ zero expected drift (`3a−2 = 0`); 0.60 ⇒ drifts
down; 0.70 ⇒ drifts up. This number is the ladder's sensor-quality requirement and must appear in the test.
8.4 **`cash_feed(state)`** — T-C1 exact dollars for a hand-built state. **T-C2 property, the load-bearing one:
over a random wire sequence (place/fill/cancel/RESOLVE/cash-credit), published expected-cash ≤ true cash at
EVERY step.** The sequence MUST include a resolve with the cash credit arriving **41 minutes later** (R171);
the naive "release on result" implementation fails this test at minute 0, which is exactly BLOCKER-1. T-C3
single-object atomic write; a partially written file never parses as valid (temp+rename). T-C4
`mode:"subaccount"` ⇒ zeros with components intact. T-C5 zeroed final feed on SIGTERM after cancel-all.
**T-C6:** `settled_awaiting_payout` releases on a balance read showing the credit, does NOT release on the
resolve event, and at +6 h unconfirmed pages `settlement_cash_unconfirmed` **without releasing**.
8.5 **`rate_bucket`** — T-B1 steady 4 req/s sustained; T-B2 429 ⇒ 2 req/s for 60 s then geometric recovery to
4.0, with `B` floored at 0.5 and `rate_starved` alerting after 10 min below half cap; T-B3 an `exit_cancel`
is admitted at zero tokens while a book poll is refused; T-B4 the degrade ladder fires in §3.4's order under
a shrinking bucket and step 3 drops the LOWEST-`net` market first. **T-B5 (SF-1):** a requote loop driving
cancels past 25% of admitted requests over 60 s ⇒ `requote_cancel` degrades and the resting order is left in
place, while `exit_cancel` is never degraded and never counted against the bound; 3 breaches in 10 min ⇒ that
market is poisoned.
8.6 **`shade_decision`** — T-S1 with `φ₁=φ₀` ⇒ never shade (halving score for nothing); T-S2 `φ₁=0` and a
large `L_eff` ⇒ shade; T-S3 k≥2 is never returned.
8.7 **Calibration loop / stand-down predicates** — T-D1 per-venue: `|log2(reading/model)| > 1` on 2
consecutive settlement days ⇒ that venue only (assert the others still allocate). T-D2 book-wide: aggregate
ratio out of band 2 days ⇒ HALT (v1 §12.3a). T-D3 no reconcilable rows 2 days ⇒ HALT (v1 §12.3b). T-D5 each
stand-down is reversible only by an explicit operator record, never by a timer.
**T-D4 — presence collapse, with the three corrections BLOCKER-4 requires.** Book-wide
`PSDH_book = Σ prox_dollar_s / (Σ committed_dollar_s / 3600)` over the trailing 2 h, HALT if
`< 0.25 × median(trailing 7 days of hourly PSDH_book)`, subject to:
```
(a) DENOMINATOR EXCLUDES every second in which NO program was live (or none was fundable). Otherwise a
    quiet overnight — the normal state — collapses the metric by arithmetic and halts a healthy book.
(b) STARVATION IS NOT TOXICITY. If `rate_yield` was active for >20% of the 2 h window, or the WS was
    disconnected >20% of it, the predicate routes to `rate_starved` / `ws_degraded` and does NOT halt:
    presence lost to our own throttling says nothing about who is eating us.
(c) MINIMUM HISTORY: the trigger is INACTIVE until ≥7 days × ≥6 fundable hours/day of history exist. A
    median over 3 samples is not a median; a fabricated one halts the book on its second day.
```
Derivation of 25%: a 4× book-wide degradation is the same magnitude (2 ratchet rungs) that stands a single
venue down. 2 h at ≥$300 committed is ≥600 $·h — decisive by §2.4. Test all four branches: healthy, genuine
collapse (HALT), overnight-quiet (no halt, by (a)), and 429-starved (no halt, by (b)); plus day-2 cold start
(no halt, by (c)).
8.8 **Cutover (`--gen-adopt`, adoption gate)** — T-A1 basis outside `[0.01,0.99]` or `> 2×` mark ⇒ market
excluded + frozen + `adopt_basis_rejected`. T-A2 an exchange position absent from the adopt file ⇒
`orphan_position` alert and that market refused for quoting. T-A3 `net` disagreement ⇒ `assume_filled`
(quoting AND recycling frozen). T-A4 `rollback_clean` flips to false on the first fill against an adopted
position, and SIGTERM writes `v5_handback.json` covering every held position in both regimes.
8.9 **Inherited suites kept as-is** (they encode invariants that survived adversarial fire): v1 §14.1-14.6 —
ALLOCATE T1-T7, forfeit/rescue T8-T13b, recycle T14-T18, at-best/coverage T19-T22, `score_side` T23-T28b,
ledger replay T29-T35. Re-baseline T28's size ladder only against a real reconciled payout.

## 9. UNDERIVED (flag upward, never silently ship)

9.1 Shared rate limit ≈ 10 req/s and v5's 4 req/s share — the limit is documented, not measured by us; the
evidence is 429s at an unknown aggregate load. AIMD makes a wrong number self-correcting, which is why the
form is derived and the number is not.
9.2 AIMD recovery (×1.25 per 60 s) — the multiplicative-decrease half is derived; the recovery rate is
convention.
9.3 Unverified bounds — 20% of ceiling total, 8 concurrent venues, ≤2 oversized probes (§1.4) — scaled from
the $16 PayPal loss as a magnitude, not from a distribution of unverified-venue outcomes. Recalibrate after
10 verified venues. The 2% figure is now a *classification* threshold (oversized or not), not a cap.
9.4 Also underived: the `+24h` horizon grace (§1.2); the past-due carry escalation factor **2×** (§1.2 — the
no-information *direction* is derived, the coefficient is not); cold-start `T₀ = 0.5` (§2.3 — affects probe
order only); the `[0.5, 2.0]` verification band (self-consistent with the system's own tolerance,
unmeasured); `d = $0.07` and the 60 s drift horizon (inherited v1 §15.4); feed staleness 120 s (4×
heartbeat) and the 6 h settlement-cash timeout (≈9× the 41-min observed lag); the 45-min kill hysteresis
(§2.5); dose-response panel of 3 slots; the cancel-lane 25% share and the 20%-of-window starvation cut in
§8.7(b); presence compaction at 7 days is derived from `T̂`'s own window, the daily rotation granularity is
not.
9.5 Inherited and still open from v1: `λ_min = 0.10`, `ENTRY_FLOOR = $2.00` (recalibrate to
`$1.00/q05(actual/projected)` after 5 reconciled periods), P3's 40%/⅓ two-sidedness, P5's deletion and its
residual anti-gaming exposure, `MIN_RESTING_LIFE_S = 30`, day stop at 35%.

## 10. OPEN QUESTIONS (each with a default that is safe under either answer)

Q1 **Machine-readable per-rung estimate surface?** Default: the operator-entered `popover_estimate` row is
the only verification input, so the ratchet advances ≤ once/day/venue — slower, never wrong. An endpoint
would raise cadence and nothing else.
Q2 **Does the main balance read include subaccounts?** Default `mode:"shared"` until probed at G7: if
included the feed stays exactly correct, if excluded it goes to zeros and the wall takes over.
Q3 **Exchange snapshot cadence/phase.** Default 1 Hz metering + MBB (`g=0`). The answer changes only PSDH's
scale factor, which cancels in every ratio the spec uses (`T̂`, ζ, the 25% collapse trigger).
Q4 **Rate limit per-account or per-key?** Default: per-account (the tighter reading); AIMD converges under
either.
Q5 **Does a YES ask covering a YES position consume collateral?** Default: charge it in our budget math (v4's
behavior). "Free" is a bonus surfaced by the resting-collateral observation already logged in
`reconcile.rs`; "charged" costs nothing because we reserved it anyway.
Q6 **$1.00 exactly: paid or not?** Inherited v1 Q2 — the $2.00/$1.10 floors sit clear on both readings; no
code path depends on the answer.

## 11. NOTE 23 §III — THE FIVE, DRAFTED (verify before first launch, do not copy)

**Cash:** v5 consumes collateral and creates inventory in the shared account until G7; every movement is
published to `lip_cash_feed.json` BEFORE it happens (§5.3); resolved-but-unpaid positions stay counted as
consumed cash until the credit is confirmed IN CASH (§5.2a); nestor's breaker adds it to expected cash; zero
hand entries in steady state; `external_cash.jsonl` remains for deposits/manual trades only, and its v4-era
rows are zeroed at G8.
**Breaker:** reads `(external_cash.jsonl sum) + (lip_cash_feed.json, behind G0's flag, default IGNORE)`;
negative side stays tight because v5 over-reports consumption; positive side widened by
`rewards_accrued_unpaid + inventory_settle_max + settled_payout_expected`; staleness > 120 s pages without
halting (§5.4); `mode:"shared"` with G0's flag false is a v5 STARTUP REFUSAL, not a warning.
**Schedule:** program `paid_out` flips ~2h post-close (poll 30 min) → `credit_pending` → ratchet input;
settlements land per market close + ~41 min; `expiration_ts` = close − 4 min backstops every order; the
credits ritual reminder fires daily; two days without credits halts deployment (v1 §12.3b).
**Collisions:** coid prefix `v5-` disjoint from `v4-` and from nestor; v5 refuses to start on a fresh v4
heartbeat; separate ledger/recon/seq paths; one writer per file; rate lanes §3.3; STP `taker_at_cross` on
every order; v5 never quotes a ticker nestor holds an open order on (read from nestor's own state file at
cycle start — if unavailable, that is a startup refusal, not a warning).
**Alerts:** ntfy `senate-nestor-2732e947` — halt, poison, day stop, `assume_filled` freeze, venue stand-down,
presence collapse, `lip_cash_feed_stale`, `settlement_cash_unconfirmed` (6 h), `orphan_position`,
`adopt_basis_rejected`, `rate_starved` (10 min), `cancel_share_exceeded`, `idle_capital` (1 h),
`rstar_no_converge` (3 cycles), coverage < 90% for 10 min, credits ritual due. `NTFY_DISABLE` honored by construction. **The human is on the topic before G3** (V-gate
G5 in ops-first-principles: the alarm chain needs a human at the end of it).
