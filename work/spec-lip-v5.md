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

1.2 **Liquidity horizon, measurable.** `L_eff(m,s) = min( T_settle(m), L_shed(m,s) )` hours.
`T_settle = (close_ts − now)/3600 + SETTLE_LAG_H` where `SETTLE_LAG_H = 0.7` (R171 measured 41 min).
`L_shed` = median hours from position-open to flat via maker shed on that (m,s), over the trailing 20
completed sheds; **unmeasured ⇒ `L_shed = ∞`** (so `L_eff = T_settle`) — the conservative default, and the
only one consistent with "no cap may assume settlement bails it out."
**Hard horizon exclusion:** a venue is excluded outright if `T_settle > program_end + 24h` unless its ratchet
rung ≥ 2 (§1.4). Derivation: past program end, inventory carries with ZERO offsetting accrual — the exact
PYPL geometry; the 24h grace covers same-day-after settlement. `+24h` is UNDERIVED (§9.4).

1.3 **Score used by ALLOCATE.** Replace v1 §2.2's hurdle line with (★). Water-filling, step, budget reserve,
caps and the per-program forfeit gate are **inherited unchanged from v1 §2.4-2.7 / §3** (they survived
adversarial fire and re-derive identically under (★)). `r*` is the previous cycle's achieved marginal rate
(fixed-point; monotone in allocation, converges). **Cold start seeds `r*` = trailing-7-day achieved rate, and
only on a truly empty history `λ_min/16`** — seeding low makes carry look cheap, which is the PayPal
direction; the unverified cap (§1.4) is what bounds the damage in that one case.

1.4 **Verified-accrual ratchet.** Per venue, state `{rung, cap_usd, last_verify_ts, verify_history}`.
```
rung 0 (unverified): cap_usd = min( ENTRY_FLOOR-clearing allocation ,
                                    0.02 × global_ceiling , INV_CAP_USD/p per slot )
rung k:              cap_usd = min( 2^k × rung0_cap , per-market cap §8.2 , global ceiling )
VERIFY(+1 rung): a popover_estimate or paid credit for a program in this venue whose ratio to the model's
                 projection over the SAME window lies in [0.5, 2.0]
DISAGREE(−2 rungs): same reading outside [0.5, 2.0]
STAND DOWN (venue, not bot): DISAGREE on 2 consecutive settlement days  [charter §5]
```
Derivations: **probe size** = the smallest allocation that still clears `ENTRY_FLOOR = $2.00` projected over
the program period (below it the venue cannot pay at all, so a smaller probe measures nothing), capped at
**2% of the global ceiling** — at $300 that is $6, so no unverified venue can repeat the $16 PayPal exposure,
and with the §3 rate budget bounding concurrent venues, **total unverified exposure ≤ 20% of the ceiling**
(hard-enforced, not emergent). **Up-1/down-2** because a false up-step costs capital at a venue that does not
pay while a false down-step costs only rate at a venue re-verifiable tomorrow; the asymmetry also makes the
ladder's drift negative under a 50%-accurate verifier, so a coin-flip sensor cannot walk the cap up (test
T-R3). **[0.5, 2.0]** is the system's own declared model tolerance (v1 §3.1, §12.3a) — self-consistent by
construction, UNDERIVED as a measured distribution (§9.4).
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
reserve when idle): `cancel/exit > place > verify(fills,positions,balance) > book_poll > classify_sweep`.
**The bucket refuses to fall below 1 token for any lane except cancel/exit** — a rate budget must never be
the reason an order cannot be cancelled. MIRROR (over-reserving): the headroom is a priority floor, not a
partition, so an idle cancel lane costs nothing.

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
Also inherited verbatim because they survived fire: poison rules (v1 §8.5), never-trust-the-indexes (§8.6),
coid stability with no run-id (§9.5), closing-room netting + closing exemptions, ledger-replay restart with
schema-mismatch abort, `NTFY_DISABLE` honored by construction, detect-and-page defaults, W2 trust gate for
any new sensor, staged human gates for spending paths.

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
 "components":{"resting_collateral":183.42,"inventory_basis":66.10,"realized_pnl":2.39,
               "fees_paid":0.00,"rewards_accrued_unpaid":6.94,"inventory_settle_max":24.46},
 "ceiling_usd":300.0,"max_inflight_usd":12.00,"heartbeat_s":30}
```
`delta_dollars = −(resting_collateral + inventory_basis) + realized_pnl − fees_paid`.
`pending_payout_dollars = rewards_accrued_unpaid + inventory_settle_max` (`= Σ n × $1.00`, the largest credit
that could land unannounced).

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
fill vocabulary, schema-mismatch abort) PLUS `presence` (§2.2), `cash_feed` (each published seq),
`rate_yield`, `ratchet`, `venue_kill`, `shade_decision`. Restart procedure, 404 disambiguation at 36 s, the
crash-gap fills window and `assume_filled` are inherited verbatim from v1 §9.4-9.4b.

6.3 **Cutover — three options, one recommendation.**
- **A. Cold.** v4 SIGTERM (cancel-all, proven) → wait for its inventory to settle → v5 starts flat. Zero
  import risk; costs hours of presence and idle dollars on multi-day inventory.
- **B. Ledger import.** v5 replays v4's ledger through a shim. Fastest, but v4's ledger carries known
  divergences from the buggy hours (2026-07-28 morning report), and an import bug manufactures phantom
  inventory — the class that produces real naked shorts.
- **C. RECOMMENDED — hot handoff with a W2-gated adoption boundary.** v4 SIGTERM cancel-all (orders gone,
  the proven path). v5 does NOT read v4's ledger. Instead it reads a one-time
  `~/nestor/data/lip/v5_adopt.json` listing `{ticker, side, net, basis}`, and at startup **cross-checks it
  against `GET /portfolio/positions`**: the exchange is authoritative on `net`, v4's ledger on `basis`. Any
  market where `net` disagrees is EXCLUDED from adoption and marked `assume_filled` (frozen for quoting AND
  recycling) until a human reconciles. Adopted positions may be shed but do not seed any new quote until the
  first clean recon pass. Downtime ≈ minutes. Cost over B: one reviewed file; over A: nothing.
- **Rollback:** v5 SIGTERM (cancel-all + zeroed cash feed) → `systemctl start lip-maker-v4`. v4's state is
  untouched by construction (6.1).

## 7. HUMAN GATES (R186 — each is a separate call, one decision, with its own rollback)

| gate | decision | owner | command / read-out | rollback |
|---|---|---|---|---|
| G1 arm inert | deploy binary, `--check` only, no capital | Fable | `--check` prints OK for unit assertion, ledger replay, data dir, cash-feed write, WS gate | delete unit |
| G2 shadow | quote nothing for ≥1 full program period; meter PSDH, score venues, publish a zeroed cash feed | Fable | `venue_rank` lines vs v4's realized accrual; PSDH populated for ≥10 (m,s) | stop |
| G3 probe capital | rung-0 caps live (2%/venue, 20% total) | **Ryan** | first `allocate` line: `spent ≤ 0.20×ceiling`, no venue > 0.02×ceiling | SIGTERM |
| G4 ratchet enable | allow caps to climb on verified accrual | **Ryan** | `ratchet` rows show only `+1` on in-band verifications | flag false |
| G5 ceiling rung | each rung funded by the PREVIOUS window's observed print, never the model (R168) | **Ryan** | one constant, one commit | previous rung |
| G6 taker-exit | enable the spending exit path | **Ryan** | v1 §5.2 inequality logged before first exit | flag false |
| G7 subaccount | cash feed → `mode:"subaccount"`; key-capability probe first (GTC + `expiration_ts` + coid cancels, one $1 order) | **Ryan** | probe passes before any capital moves | mode shared |
| G8 v4 decommission | stop and disable v4 | **Ryan** | after 3 clean v5 settlement days | restart v4 |
No gate bundles a capital change with a code change. No shared-tree builds — worktree only; native aarch64
built on the VPS (`file` the artifact against `uname -m`).

## 8. TEST PLAN — money rules as pure functions, no network in any test

8.1 **`net_rate(ρ,S,p,q,φ,d,L_eff,r*,T̂)`** — T-N1..N3 reproduce §0.4's three rows to 1e-3: PYPL −11.80 (and
`H` clips to 0), treasury +0.110, gas-cheap +3.12. T-N4: `L_eff` doubling halves the horizon multiplier's
headroom exactly (`carry` linear in `L`). T-N5: at `q=0` the function is finite (no division by zero — v1's
B4 defect must not return).
8.2 **`psdh(rows)`** — T-P1: 60 min at $100 resting at best, no inventory ⇒ 3600 s/h, `T̂=1`. T-P2 replay
parity: shuffled/split `presence` rows sum identically. T-P3: $100 resting 1 min then $100 inventory 59 min
⇒ PSDH = 60·60/(100·60/3600·... ) computed exactly in the test, `T̂ ≈ 0.0167`; assert KILL fires under §2.5's
zero-model rule variant. T-P4 scale invariance: 10× all sizes ⇒ identical PSDH. T-P5: one tick behind best
weights 0.5 exactly.
8.3 **`ratchet(state, reading, model)`** — T-R1 in-band ⇒ +1 and cap doubles; T-R2 out-of-band ⇒ −2, floor at
rung 0; T-R3 **coin-flip verifier over 1,000 alternating readings ⇒ terminal rung 0** (the asymmetry proof);
T-R4 caps never exceed `min(per-market cap, ceiling)`; T-R5 two consecutive out-of-band settlement days ⇒
venue STAND_DOWN and every other venue keeps quoting.
8.4 **`cash_feed(state)`** — T-C1 exact dollars for a hand-built state. T-C2 **property: over a random wire
sequence (place/fill/cancel/settle), published `delta_dollars` implies expected-cash ≤ true cash at EVERY
step** (§5.3's write-before-place invariant). T-C3 single-object atomic write; a partially written file never
parses as valid (temp+rename). T-C4 `mode:"subaccount"` ⇒ zeros with components intact. T-C5 zeroed final
feed on SIGTERM after cancel-all.
8.5 **`rate_bucket`** — T-B1 steady 4 req/s sustained; T-B2 429 ⇒ 2 req/s for 60 s then geometric recovery to
4.0; T-B3 a cancel is admitted at zero tokens while a book poll is refused; T-B4 the degrade ladder fires in
§3.4's order under a shrinking bucket and step 3 drops the LOWEST-`net` market first.
8.6 **`shade_decision`** — T-S1 with `φ₁=φ₀` ⇒ never shade (halving score for nothing); T-S2 `φ₁=0` and a
large `L_eff` ⇒ shade; T-S3 k≥2 is never returned.
8.7 **Calibration loop / stand-down predicates** — T-D1 per-venue: `|log2(reading/model)| > 1` on 2
consecutive settlement days ⇒ that venue only (assert the others still allocate). T-D2 book-wide: aggregate
ratio out of band 2 days ⇒ HALT (v1 §12.3a). T-D3 no reconcilable rows 2 days ⇒ HALT (v1 §12.3b). T-D4 **new
— presence collapse:** book-wide PSDH below 25% of its trailing-7-day median for 2 consecutive hours ⇒ HALT.
Derivation: that is the signature of being the fish everywhere at once (an exchange-wide taker surge), it is
detectable in minutes instead of days, and 4× is the same magnitude (2 ratchet rungs) that stands a single
venue down; 2 h at ≥$300 committed is ≥600 $·h, decisive by §2.4. T-D5 each stand-down is reversible only by
an explicit operator record, never by a timer.
8.8 **Inherited suites kept as-is** (they encode invariants that survived adversarial fire): v1 §14.1-14.6 —
ALLOCATE T1-T7, forfeit/rescue T8-T13b, recycle T14-T18, at-best/coverage T19-T22, `score_side` T23-T28b,
ledger replay T29-T35. Re-baseline T28's size ladder only against a real reconciled payout.

## 9. UNDERIVED (flag upward, never silently ship)

9.1 Shared rate limit ≈ 10 req/s and v5's 4 req/s share — the limit is documented, not measured by us; the
evidence is 429s at an unknown aggregate load. AIMD makes a wrong number self-correcting, which is why the
form is derived and the number is not.
9.2 AIMD recovery (×1.25 per 60 s) — the multiplicative-decrease half is derived; the recovery rate is
convention.
9.3 Unverified caps 2% per venue / 20% total — scaled from the $16 PayPal loss as a magnitude, not from a
distribution of unverified-venue outcomes. Recalibrate after 10 verified venues.
9.4 Also underived: the `+24h` horizon grace (§1.2); cold-start `T₀ = 0.5` (§2.3 — affects probe order only);
the `[0.5, 2.0]` verification band (self-consistent with the system's own tolerance, unmeasured); `d = $0.07`
and the 60 s drift horizon (inherited v1 §15.4); feed staleness 120 s (4× heartbeat); the 45-min kill
hysteresis (§2.5); dose-response panel of 3 slots.
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
published to `lip_cash_feed.json` BEFORE it happens (§5.3); nestor's breaker adds it to expected cash; zero
hand entries in steady state; `external_cash.jsonl` remains for deposits/manual trades only.
**Breaker:** reads `(external_cash.jsonl sum) + (lip_cash_feed.json)`; negative side stays tight because v5
over-reports consumption; positive side widened by `rewards_accrued_unpaid + inventory_settle_max`;
staleness > 120 s pages without halting (§5.4).
**Schedule:** program `paid_out` flips ~2h post-close (poll 30 min) → `credit_pending` → ratchet input;
settlements land per market close + ~41 min; `expiration_ts` = close − 4 min backstops every order; the
credits ritual reminder fires daily; two days without credits halts deployment (v1 §12.3b).
**Collisions:** coid prefix `v5-` disjoint from `v4-` and from nestor; v5 refuses to start on a fresh v4
heartbeat; separate ledger/recon/seq paths; one writer per file; rate lanes §3.3; STP `taker_at_cross` on
every order; v5 never quotes a ticker nestor holds an open order on (read from nestor's own state file at
cycle start — if unavailable, that is a startup refusal, not a warning).
**Alerts:** ntfy `senate-nestor-2732e947` — halt, poison, day stop, `assume_filled` freeze, venue stand-down,
presence collapse, `lip_cash_feed_stale`, coverage < 90% for 10 min, credits ritual due, `rate_yield`
sustained > 10 min. `NTFY_DISABLE` honored by construction. **The human is on the topic before G3** (V-gate
G5 in ops-first-principles: the alarm chain needs a human at the end of it).
