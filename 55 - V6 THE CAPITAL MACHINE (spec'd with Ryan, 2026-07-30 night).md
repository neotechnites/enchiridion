# 55 - V6: THE CAPITAL MACHINE — spec'd with Ryan, 2026-07-30 night

> Written to be BUILT TODAY and TURNED ON TOMORROW (2026-07-31). Everything here was
> derived in conversation with Ryan on 2026-07-30; premises and reasoning included so the
> builder needs no other context. Companion: [[54 - THE ALLOCATOR LAW]] (v5's law — v6 is
> the same machine with three dials changed), [[52]], [[47]], [[48]].

## 0. THE FRAME (Ryan's, agreed)
**v5 is written to be safe, earn a little, and gather data.** Its $10/rung seat is a
deliberate straitjacket — it will be too small for its own best finds (sole-maker venues
need $20–60 just to qualify a side; v5 walks past them by design).
**v6 is where capital increases earnings.** Same law, three dials changed. Target: $200/day
on $1–2k. LIP ends Sept 1.

## 1. THE THREE DIALS (two alone land ~$110/day; three land $200)
**Dial 1 — order sizing becomes MARGINAL.** v5: every funded market gets one knee-sized
seat. v6: cliff-aware entry (v5's exact cost-to-target formula) + marginal deepening above
it — keep adding dollars wherever the NEXT dollar earns most (deepen vs open-next), until
all funded markets' marginal returns equalize and capital is gone. Most clusters stop at
their knee (~$15–25) because "the other $40 earns more elsewhere" (Ryan). This is the old
water-fill SHAPE readmitted with correct inputs and a cliff — v5's law is its special case.
**Dial 2 — per-cluster cap from the ruin formula** (see [[54]] procedure): A = C/N,
N ≥ z²·p(1−p)/(d−p)², N capital-INDEPENDENT (~25–36 at measured p≈8–10%, d=0.2, z=2).
At $2k: A ≈ $55–80. The cap is a ruin guard, NOT a target — it binds only for freak
markets (sole-maker, giant pool, flat marginal curve). The knee is where money stops.
**Dial 3 — the price floor lowers as good rungs exhaust.** Deepening hits share concavity
(2–3× credit for 6.6× dollars); the rest of $200 is BREADTH, and breadth at $2k exhausts
the supply above today's floor. Rule: lower the floor until the marginal admitted rung's
(expected credit − measured calibration bleed) equals the deepening margin. Both sides
from the price-bucket calibration (8,240 settled markets), not opinion. Evidence the
frontier is near: sub-5¢ cohort was net +$3.88 even on v4's undisciplined book.
Breadth needs NO new machinery — the law already funds cheapest-first until capital
exhausts; more capital automatically = more rungs; the floor just supplies them.

## 2. ORDER SIZING RULING (Ryan delegated; derived from the machine; supersedes his ex-1)
**Order = W, the full resting size the share-math demands.** Never shrink to stretch
across turnovers — a shrunk order under-earns EVERY hour and misses the target with
certainty. **Turnovers enter only the affordability screen: W × max(1, φ·h) ≤ A, else
skip with the number logged** ("can't afford it"). Requote budget = allocation minus
consumed; refill at full W until spent. **Oversize past W toward full A only on
MEASURED-low φ** (the "market is awesome" case is conditioned on knowing φ, not ignorance).
Consequence accepted: need-$5-at-2.5-turnovers now FAILS a $10 seat ($12.50>$10) — correct:
a market you can't sustain at full size through expected fills can't reliably hit target.

## 3. WHY SLICING BEATS DUMPING (size×time analysis)
Under taker-event-eats-your-quote, total dollar-hours = allocation/φ — INVARIANT to
slicing. Slicing wins on two tiebreaks: share is concave in size (small-sustained earns
more credit per dollar-hour in contested books) and requoting converts one death-draw into
many (variance matters because of the forfeit cliff). Dump-full-size wins only at S≈0
(share flat at 100%) — which the law already does via measured-low-φ oversize.

## 4. RUIN MATH SETTLED (the p conversation)
p (per-cluster daily wipe) = P(full allocation fills in a day) × P(settles against |
filled). The second term is READ OFF THE BOARD (the price), degraded by the measured
calibration gap (1–2¢ posted ≈ 8× overpriced, −95–100% EV/$ filled; ≥~15¢ posted≈realized).
**The price floor's whole job is capping the calibration gap** — above it, filled inventory
is EV≈0 VARIANCE, not bleed ("worst case ~$50 drawdown on EV-0"), so p collapses to the
tail of fair draws and N≈30 is survivable. The floor trades rung availability for safety —
a trade that reprices at $2k (Dial 3). Under Ryan's conservative always-filled worst case,
φ drops out of ruin entirely; φ only paces capital→inventory conversion.

## 5. WHAT PICKS RUNGS (the whole model, reduced)
Of the five inputs: pool, time left, accrued are KNOWN (feeds). Presence is operational.
**All residual skill = modeling competition S(t) and φ.** Sharpenings:
- Snapshot S is known (one book read); the real problem is S over the NEXT 24h — we commit
  against today's book and rivals arrive after us. S-drift is the bigger lever (dust rungs
  died of share dilution, not fills).
- Newcomers are mis-ranked by BOTH: no φ tape AND deceptively-empty opening books (ranked
  at their most flattering moment). Info corrects in hours; the committed capital does NOT
  move — locked until settle or Ryan sells (no displacement in v5, deliberate; moving
  UNFILLED collateral off a ruined rung is a legitimate v6+ question, unresolved).
- Rungs that miss their dollar burn the DAY (~$1.50 opportunity each), not principal —
  unfilled collateral returns; sub-$1 accrual forfeits. Expect ~⅓ misses early, shrinking
  with tape.
- Longshots: higher φ per unit activity (adjacent to the graveyard; capitulation flows
  into cheap bids) AND near-total loss-given-fill, but tiny collateral each — price
  predicts φ within a venue, ACTIVITY predicts it across venues → φ fallback wants both
  dimensions. Fill adverse-selection tripwire (built): capital-weighted avg order price vs
  avg position entry price vs bucket-φ-predicted gap, on recon cadence.

## 6. THE DATA (all flowing as of tonight)
- **Competition recorder LIVE** (~/kalshi_data/scripts/competition_recorder.py, @reboot
  cron): WS orderbook_delta for EVERY in-window rewarded market (3,605 at start), every
  change ms-stamped, full snapshot per book each 15-min reconnect → exact S(t) at any
  second; time-of-day competition analysis trivial. ~10–30MB/day gz,
  ~/kalshi_data/competition/deltas-YYYYMMDD.jsonl.gz.
- **φ tape**: ledger fill_obs (fills) + presence jsonl (exposure denominator), accumulating
  whenever v5 runs.
- **φ for markets we never touched**: recorder books × public trades tape (a print against
  a resting level IS someone's fill) → measured fill hazard by market and price level
  across the whole universe. Kills the newcomer-φ problem.
- Calibration set: ~/calib2.json (8,240 settled markets) — the floor/bleed measurements.

## 7. STATE OF THE BUILD (as of writing)
v5-law allocator: two independent builds (Opus `allocator-law`, Fable `allocator-law-f`)
adjudicated by Fable manager; Fable branch WON (short-window target max($1, 1.50·h/24);
turnover-inclusive affordability; decisive-zero φ) with grafts from Opus (TARGET_MOVED
requote trigger, empty-side legal fix, measured-φ-gated oversize) + the §2 sizing ruling.
Manager re-runs full mutation battery before APPROVED. Safety law already LIVE: never
sells, halt = cancel own orders then idle, startup ignores inherited orders, nothing
cap-exempt. v6 = the approved v5-law build + the three dials — the implementation delta
is deliberately small.

## 8. TOMORROW (2026-07-31, Ryan's call on each)
1. Deploy the approved v5-law allocator (prove $10/day low-swing shape).
2. Deposit → run [[54]]'s capital-scaling procedure → set A and the floor from capital.
3. Flip Dial 1 (marginal deepening) — the only new CODE v6 needs beyond v5-law.
4. First S(t)/φ models from the recorder's first night of data.

## §7 UPDATE — APPROVED FINAL (2026-07-30 ~21:50 MT)
Manager stamped **9bf23c5** on allocator-law-f: Fable build won (short-window target
max($1, 1.50·h/24); decisive-zero φ) + grafts G1/G2/G3 + Ryan's sizing ruling (order = W,
turnover affordability, example-1 knowingly inverted) + the B14 gate (plan-driven cancels
respect 30s min resting life → same-rung placements ≥30s apart → 3-in-60 breaker
unreachable by plan oscillation, safety cancels exempt; the live 18:33 halt geometry now a
pinned test). All 8 manager mutations killed. Merged to lip-v5-build (−1,604 net lines),
715 green verified independently, pushed. NOT deployed — awaiting Ryan's go. Deploy = rsync
+ operator halt-clear + start.

## LIVE (2026-07-30 ~22:15 MT, Ryan's go)
9bf23c5 deployed, halt cleared with root cause, started. First law book on the wire in
~3 min: 11 seat-sized orders ($2.60–$10 each), plan 317 candidates → 11 funded / 204
unaffordable (logged with numbers) / 102 cluster_taken, $96 of $217 budget deployed.
Short-window clamp verified live (7.3h window → target $1.00). Measured-low-φ oversize
fired (MCMORROW $9.94). Churn gate pacing (plan_exit_deferred). Positions charged against
seats (no doubling down). WATCH ITEM for morning: plan-vs-rail cluster-key mismatch on
KXMLABELSHARE + KXTRUMPSAY (plan funds, rail refuses on inherited positions, ~52 bounded
retries) — to builder.

## RECONCILED WITH RYAN (2026-07-30 late) — supersedes §1's "three dials" framing
1. **v6 is a LOGIC change**: new allocator core — rank → enter at cliff cost → marginally
   deepen (next dollar to highest marginal rate) → stay/move EMERGENT. Not dials. Proof:
   v5 complete at $300; v6 has nothing to buy below ~$600.
2. **FORK, don't edit** (Ryan): v5 freezes as the working $300 earner + fallback binary;
   v6 builds on the fork. Frozen branch = zero maintenance = the fork is free.
3. **No DONE rule in v6.** Banked credit is sunk; enforced skip pulls capital from proven
   rates to unproven ones. The cliff makes PRE-floor dollars special (they unlock stranded
   accrual); above the floor, markets compete on plain rate. Rotation (same-cluster next
   market) and staying are both emergent from the marginal queue.
4. **Anti-churn without timers**: (a) a switch pays its TRUE cost — stranded sub-cliff
   accrual at risk + transit presence loss — small differences can't pay the toll, big real
   ones pay instantly; (b) rank on SMOOTHED S (window derived from the recorder's measured
   flicker rate, first night). 30s min-resting-life + B14 remain the structural floor.
5. **Proof gate = ONE settle-day** (not 3): ~$7+ earned, per-fill realized-EV tape ≈ fair
   (each fill: price paid vs outcome; calibration table as prior), no unexplained halts.
   p needs no ensemble — its components are the board price + calibration gap.
6. **Measured dispersion (first day, 79 programs)**: fastest rung $0.276/h (→$1.50 in 5.4h
   at ~$7 seat; ~2.6h at $30; ~1.8h at $66, √-scaled, ceiling = side's half-pool rate);
   median $0.0065/h (232h — can NEVER clear a window); spread 42×. The game is holding
   seats in the top decile; top-2 rungs were most of all earnings.
7. **Breadth guarantee**: the queue never idles capital (entry in a moderate market always
   beats idle); deepening self-stops at the knee; the real breadth limiter is the
   affordability screen → solved by the floor dial + bigger seats. Rail stays $66;
   TRIPWIRE: <40 distinct funded markets at $2k on day one → halve to $33.

## THE CLUSTER CAP, DERIVED (crystal-clear record, Ryan-requested)
**Why clusters**: markets resolving from one fact settle together (measured: treasury
tenors 9/9 same-direction over 13 settle-days). Diversification across them is fake; risk
bounds per SETTLE SOURCE. Cluster = one settle source.
**Why a cap**: fills that settle against us are the loss mechanism; a cluster's markets
fill and die together, so the catastrophe unit is the cluster. Cap = max dollars one
settle source may take in its worst day.
**Derivation**: (1) policy d=0.2 (day stop: lose ≤20%/day); (2) cluster worst case = its
allocation A, 100% LGD, always-filled assumed (φ out of ruin by construction);
(3) p = P(wipe/cluster/day) = board price of our held side, degraded by the measured
calibration gap (n=8,240: 1–2¢ ≈ 8× overpriced; ≥~15¢ posted≈realized). THE FLOOR'S RUIN
ROLE: above it fills are fair draws → p = tail of fair coins, not burn;
(4) clusters independent BY CONSTRUCTION → same-day wipes ~ Binomial(N,p); z-sigma day
wipes ≈ pN + z√(Np(1−p)); (5) A×wipes ≤ d·C with A=C/N → **N ≥ z²·p(1−p)/(d−p)²**.
N is CAPITAL-INDEPENDENT (C cancels); A=C/N scales linearly; p→d ⇒ N→∞ ("don't play").
**Numbers**: p≈8–10% prior → N≥25–36 → run 30 → A=$10@$300, ≈$66@$2k.
**Couplings (decided 2026-07-30)**: floor↓ ⇒ funded-mix p↑ ⇒ N↑ ⇒ A↓ — compute the cap
from the ACTUAL funded mix's p. Knee (≈$25–30, per-MARKET, emergent from share concavity)
≠ cap (per-CLUSTER, law); a $66 rail naturally holds ~2 knee markets (adjacency). N is a
diversification FLOOR, not a target. $66 kept over $33: $33 refuses the second knee in
exactly the double-fast clusters where the best dollars live; $66 adds no unpriced risk.

## THE RISK FRAME, RYAN'S DOOR (2026-07-30 late — CANONICAL framing; the N-formula is its math)
**Hold to settlement stands for v6; selling maybe v7** ("every time we try to do it we
decide not to").
**Ryan's statement (near-verbatim):** we had a day earning $70 in rewards while losing
~$140 in positions — bought markets that move together, consolidated on two, both lost.
Large drawdowns are forbidden NOT because losing is bad but because RECOVERY is slow:
positive EV over 10 days is worthless if half the stack dies on day one. Four levers:
(1) play smaller — lowers profit, useless; (2) longer horizon — same as more capital,
which we don't have; (3) UNCORRELATED breadth with sample size big enough for neutral EV —
THE lever; (4) selling — out of scope, no intelligent way yet. Position PRICE sets the
required sample: ~1¢ positions need ~thousands uncorrelated (and are burn, not variance —
the calibration gap); ~99¢ positions, one deep bet a day passes. Shallow (120 small) vs
deep (10 big); we KNOW which markets we want; the sweet spot between average cost and
cluster cap is what earns most. [= the floor–cap coupling: every (avg price, cap) pair
implies required N via the binomial; maximize expected credit s.t. z-day ≤ d — computable
from the ranking table, a lookup not a debate.]

**NO MONEY-LOST STOPPER (Ryan, agreed).** Variance losses never halt the earner — the
sizing priced them, and halting adds a $0 day on top of the loss. The stopper becomes a
BUG ALARM: halt only on losses INCONSISTENT with the priced model (faster than the
always-filled worst case; loss-per-fill outside calibration). Model-consistent losses:
keep earning. Model-impossible losses: the machine is broken.

**d DERIVED (was Ryan's 20% guess).** Recovery cost of drawdown x = x/(e(1−x)) days at
daily earning rate e ≈ 5–10%/day — pure compounding tolerates d>40%. The binding
constraint is MODEL ERROR: p rests on ~4 days of tape + calibration; a 2× p-error ≈
doubles the real z-day. Fatal line (recovery eats the program) ≈ 45–50%.
**d = fatal ÷ error-allowance ≈ 45%/2 ≈ 22%.** The 20% was right for the newly-derived
reason: insurance against our inputs being 2× wrong. As tape shrinks p's error bars,
d drifts up — earned, measured looseness.

**d SETTLED = 20% (Ryan, final).** Derivation trail, so it isn't lost: (1) pure
compounding barely binds at this machine's earning rates; (2) the binding constraint is
MODEL ERROR — p rests on thin tape + calibration, allow it to be 2× wrong; (3) fatal
recovery line ≈ 45–50% ÷ 2 ⇒ ~22%; (4) RYAN'S CORRECTION: earnings do NOT scale linearly
with capital (√-saturation per rung, knees, finite fast markets ⇒ concave in C), so the
linear-recovery arithmetic in (1) overstates recovery cost of a drawdown and the whole
derivation is conservative; (5) rounded to **20%** — "not restricting us all that much."
Revisit only when p's error bars shrink with tape (loosen) or the funded mix cheapens
(tighten via the floor–cap coupling).

## PHI SHRINKAGE (Ryan, 2026-07-30 night — in build, deploys to v5 on approval)
Incident: quiet afternoon tape → "measured 0" → oversize-to-$10 fired → evening flow ate the
seats (42 fills, ~$76 inventory in 8h; fees only $1.23 — clean maker fills, not crossing).
Fix (Ryan): "take our global average and use the rung's history to adjust it until the
history is very long" = empirical-Bayes shrinkage: φ̂ = (fills + k·prior)/(exposure_h + k),
prior = bucket→global, k derived from cross-market φ dispersion (gamma-Poisson), NOT chosen.
Kills both prior failure modes: thin-quiet ≠ zero (no premature oversize), unknown ≠ high
(no R3 un-funding deadlock). Oversize gate becomes posterior-low AND history-dominates.

## INCIDENT: THE MISSING BLEED TERM (2026-07-30 late night) — v5 STOPPED by Ryan
Resting book slid to avg 8.2¢ (two 300-lot 3¢ walls, a 1¢ rung) vs held 12.3¢ vs ~15¢
design. ROOT CAUSE: my spec translation. The law ranks by capital-to-target only; the old
★'s expected-fill-loss term (φ × loss-given-fill) was dropped when the metric changed —
builders built the spec faithfully, review reviewed against the spec. The 15¢ average was
an EMERGENT property of the old machine; its cause was never encoded. FIX in build
(wt-bleed): g(bucket) derived from calib2.json (8,240 settled) = expected loss fraction
per $ filled; effective_need = W·max(1,T) + W·T·g ranks the toxicity; viability screen
target > W·T·g ("bleed_exceeds_credit", logged); envelope bleed charged at envelope size
(self-limits at low φ). No average-price rule — the average re-emerges as consequence.
Also: phi-shrinkage review caught a second incident-door (zero-prior bucket ⇒ k=0 ⇒ any
exposure "dominates" ⇒ envelope on quiet tape) — sent back, fix in flight. v5 stays OFF
until both land + Ryan's go.

## BOTH LANES APPROVED + LIVE — the 24h proof run started
φ-shrinkage 4c88a2e + fill-bleed 89fc682 (both APPROVED FINAL after adversarial review:
independent g-table reproduction, PAVA-totals adoption, one-callable ordering with AST
guard, six+ mutations re-run per lane). Merged, 748 green, deployed, v5 STARTED. First
cycles show the bleed screen refusing with numbers (bleed_exceeds_credit) — live proof the
term bites. g-table headline: gap persists to ~50¢ (10–28¢: 35% bleed/$ filled, 7σ) — the
"fair above 15¢" claim was WRONG; funded-book natural average ~19.7¢. Ryan's plan: 24h run
→ tomorrow 8pm deposit $1k → v6. Proof gate: presence ≥90%, 20+ rungs, per-fill realized
loss ≤ ~1.5× table, estimates paid as stated, credits ≥ realized bleed.

## FINAL AMENDMENTS BEFORE THE V6 BUILD (settled with Ryan, 2026-07-31 night)
1. **TWO-SIDED, as law**: additive revenue IFF the second side's expected fill cost < its
   half-pool credit — per-market arithmetic, machine-checked (already live in v5: shared
   envelope, split-guarded oversize, fundable-counterpart detection). At the wings the
   condition ~never holds (calibration is symmetric: whichever side is CHEAP is the
   overpriced one — the "+EV lottery-sale" claim is DEAD, killed by the file). It holds in
   mid-priced markets and, decisively, in the quiet class where both sides rest unfilled.
2. **THE CENTERPIECE — ladder-wide presence in quiet venues.** The $70-day decomposition:
   v4 grossed $70 via fat rungs (~$8) across ENTIRE ladders (dozens of per-strike pools,
   both sides, $448 deployed) — at −$195 of fills. Net −$124: volume at negative margin.
   v6 builds the same revenue shape where the margin is ~zero: quiet families (treasuries,
   hourly-price ladders — φ≈0 structurally), every affordable strike, both sides where
   clean, seats sized to qualify walls. One-market-per-cluster is RELAXED for this class:
   the cluster cap bounds DOLLARS (per note 55 reconciliation); per-strike $1 floors
   require fat-enough-per-strike or wall-qualified sides — the builders must derive this
   arithmetic (per-strike credit = share × pool/2 must clear $1/window per strike funded).
3. **CAPITAL = $600 recommended** (Ryan nervous, correctly — money follows receipts).
   Seats ~$20 = knees + cheapest walls (1-2¢ ladders); the decisive treasury-wall test
   identical at $600 vs $1k; ceiling ~$60-100/day if thesis holds. Scale to $1k+ only
   AFTER the wall verdict + a proven profitable day.
4. **Deploy rule**: v6 goes live the moment (a) reviewer's battery passes AND (b) the
   deposit lands — no clock-waiting. v5 freezes as fallback binary at the fork commit.
5. **First act at deploy**: the ~$40 treasury wall (one quiet ladder market, qualified),
   estimates feed watched across 2 reward batches — the thesis' load-bearing link measured
   before the rest deploys. If share flows → the queue deploys the book. If not → STOP,
   nothing else deploys, thesis rework.

## THE DEPLOY PLAN — RYAN'S 120/480 (2026-07-31 night, supersedes the $40 probe)
Ryan: stake $120 in treasury + gas (the measured top earners), "not worry about risk,"
test earnings; the rest stabilizes. VERIFIED AGAINST THE MATH: worst case of the $120
(everything fills, everything settles wrong) = exactly d×C = 20% of $600 — the
concentrated probe is precisely as safe as the diversified book by the day-stop's own
arithmetic (the ruin formula inverted). Conditions that keep it honest: WINGS AND WALLS
ONLY (treasury qualification walls across tenors at 1-2¢ sides ~$10-20 each + gas ladder
wings across strikes; NEVER mid-priced fat legs — that's what killed v4, not
concentration); never-sell unchanged; treasuries+gas = two settle sources, both-die-day =
the full $120, priced and accepted. The $480 runs the ordinary law book (denominator +
steady credits). 3× the signal of the $40 probe, verdict within one settle cycle (both
families run daily windows). v6's boot mode executes exactly this split.

## ERRATA to §4 / RUIN-p (2026-08-01 early, validated by convergent evidence)
Both v6 builders, working blind, independently found §4's p-definition self-contradictory:
the literal always-filled/board-price reading gives p ≈ 0.87–0.97 ≫ d=0.20 ⇒ N→∞ ⇒ v6
funds NOTHING. Both independently shipped the same resolution, and the adjudicator
reproduced the arithmetic: **the measured 8–10% cluster-days prior owns the LEVEL of p;
the funded mix enters as a RATIO of calibration-degraded settle-against probabilities**
(preserving floor↓⇒p↑⇒N↑⇒A↓ one-directionally and reproducing this note's own N=25–36
band and A=C/30 values). The implied fill factor is logged on every derivation so the
cluster-days tape can falsify the anchoring. §4's always-filled framing survives only as
a diagnostic instrument (RUIN_ALWAYS_FILLED pages "don't play").

## V6 ADJUDICATION (2026-08-01 early): Opus lane Wins as base + 2 blocking grafts
Winner v6-build (850 tests, exact cancel-all spine, disarmed-mode = bit-for-bit green v5
fallback, floor-dial correct: band → min tick when armed, bleed/net screens do the
refusing — the Fable lane's kept 6¢ band refused the 1¢ treasury wall outright, deploy
plan inexecutable there). Grafts from Fable: N = max(30, formula) (diversification floor,
"run 30"); deepening carries the oversize evidence clauses (proven: 0.5 contract-hour
rung deepened to the $19.95 rail — the quiet-afternoon incident through a new door).
Minor: probe seat-exemption fix; PROBE_FAMILIES = KXAAAGASD + boot pages on zero-slot
probe family. Final battery after grafts → APPROVED FINAL → merge to v6, deploy on deposit.

## V6 APPROVED FINAL — 1af221a (2026-08-01 early), merged to branch v6, DEPLOY-READY
Final battery clean (7 mutations re-run by the adjudicator, 872 green armed AND disarmed).
THE STRONGEST SIGNAL THIS PROCESS PRODUCES: after grafts, the two blind-built lanes yield
the IDENTICAL funded book on the shared board (wall $10.01 / mid $2.85 / rich $20.00 /
rescue $1.92 / thin $1.20). Note's printed dial pairs reproduce exactly. Disarmed mode =
green v5 fallback. Deploy = deposit lands → set C → boot (README deploy checklist: verify
PROBE_FAMILIES against the live board — armed-probe-zero-slots pages). The 120/480 probe
fires on boot; estimates feed adjudicates the wall thesis within ~2 reward batches.
v5 soaks meanwhile on lip-v5-build; v6-f (Fable lane) frozen unmerged, its two winning
ideas live in G1/G2.

## TRIPLE-REVIEW ROUND (Ryan-mandated, 2026-08-01) — Lane A (theory) record
Verdicts: THEORY approve · LOGIC/PREMISES reject · IMPLEMENTATION reject. Per the defend
protocol, all three rejections were CROSS-EXAMINED and CONFIRMED by my own execution:
1. **Banked accrual not sunk** (source-confirmed: paid(q)=accrued+credit): dead past-cliff
   incumbents rank at inflated entry rates, block clusters, buy negative-EV contracts —
   rotation structurally broken. Fix: entry uses the increment.
2. **Side-pooled g table hides favorite-laying toxicity** (data-confirmed from calib2, my
   own split): NO-side g at 6-50¢ collateral = 0.23–0.58 vs YES-side ≈ 0-to-negative;
   pooled 0.35 undercharges the toxic side — same defect class as the 07-30 incident.
   Fix: side-split table, g ≥ 0 clamp, bucket key = (side, unit).
3. **Probe d×C cap per-pass only** (source-confirmed: spent_by_lane={} each pass; position
   basis never charges the lane) → intraday restaking can exceed $120 in the capitulation
   regime. Fix: seed lane spend from probe-eligible position basis.
Plus: probe verdict strengthened (earned vs PREDICTED, ratio ≥ 0.5) — "earned>0" passed a
10×-discounted wall. Fix round with the builder; Lanes B (implementation) and C (red team)
still out; all nine verdicts required before deploy.

## Lane B (implementation) record — 2026-08-01
Verdicts: THEORY-AS-IMPLEMENTED approve (every formula hand-verified; block-jump pinned
to brute force; 21-mutation battery, 20 killed) · LOGIC/PREMISES reject · IMPLEMENTATION
reject. Central defect CONFIRMED at source: the probe's cluster-rail exemption exists in
the PLAN only — place() enforces the unexempted rail, so the $120 probe deploys ~$30-40
on the wire with six walls refusing forever (violates the codebase's own one-expression-
of-the-rail premise; fails CLOSED — behavior bug, not money-safety). Fixes F5-F8
dispatched: exemption expressed once via probe.eligible world-facts at both plan and
place + end-to-end regression; M17 survivor pinned (family evidence licenses, never
manufactures); probe walls excluded from funded-mix p (double-charge argument — builder
to derive or counter); the last two unlogged skips counted. Lane C (red team) still out.

## Lane C (red team) record — 2026-07-31
Verdicts: THEORY approve-qualified (p_fill_implied assumed-not-measured — known, flagged) ·
LOGIC/PREMISES reject (probe families are SINGLE clusters — unfixed, the $120 deploys ~$30;
converges with Lane B's F5) · IMPLEMENTATION reject (it reviewed the worktree MID-FIX-ROUND
— my process error; re-review runs on a frozen commit). ITS NEW FIND WAS FIRING LIVE:
retirement diff runs against possibly-partial scans (last_scan_complete had ZERO production
consumers) — overnight: 36 partial scans, healthy top-earner rungs recalled
(retired_venue_recalled: MCMORROW-NOONE, TRUMPTIME-H2, EURUSD, TRUMPACT-T3) — presence
bleeding on our best seats all night. FIXED + DEPLOYED to live v5 within the hour
(completeness gate + structural guard, 753 green); ported to v6 as F9 with an
absence-as-evidence audit of all new modules. Attack scripts preserved in scratchpad.

## Round 3 — the F1-F9 consolidation (frozen 2026-07-31, commit e2061ff on v6-build)
Builder delivered all nine fixes on ONE frozen commit: **e2061ff**. 924 tests green ARMED
and DISARMED (4 armed-only skips — the v5-fallback claim holds); mutation battery 48/48
killed, every fix has a clause-level mutant with a named killer.
- F1: banked credit sunk via `net = paid(q) − paid(0)`; sunk constant in q → deepen
  derivative untouched (asserted numerically at q=20/80/300); paid(0)=0 below the cliff →
  the sub-$1 rescue is untouched. Dead cluster now rotates (entry_rate −0.11 vs +20.73
  pre-fix on the reproduction fixture).
- F2: `g_for_price(p, leg)` — leg REQUIRED, no pooled fallback. Shipped constants at 20c:
  NO 0.5679, YES 0.1122 (pooled was 0.3508). YES-negative g clamped 0 ("risk charges do
  not pay rebates" — stated policy). PROBE_WING_MAX_C re-derived on split tables (same 4c,
  now computed — a synthetic-table test kills the hardcode).
- F3+F5 merged into ONE lane accounting read by plan AND place(): fresh pass stakes $80.08
  across 8 tenors (cluster rail exceeded, seat binds at $10.01); after $120 of fills the
  lane stakes $0. Positions charge the lane at placement — F3/F5 share the ledger.
- F4 verdict = earned vs predicted ≥ 0.5 (REPORT otherwise; no denominator ⇒ report).
  F8: requote skips counted with distinct reasons. F9 ported with both tests. F6 pinned
  both directions.
- **Builder deviation 1 (flagged, to adjudicate):** ordinary rail derived from the
  ORDINARY lane's capital — $480/30 = **$16.00** at C=600, not $20 from full C. Argument:
  full-C rail lets the lane hold 24 clusters while claiming 30 → ruin bound stated but
  not met; cites this note's own "the $480 runs the ordinary law book".
- **Builder deviation 2 (flagged):** bug-alarm threshold is a CORRECTION not a comment
  fix — the statistic only moves on settlements, so family = settlements, m = N×days =
  960, α = 5.2e-5, **z = 3.8807**, invariant to loop rate.
- Process find worth keeping: a defensive DUPLICATE of a guard is a clause no mutation
  can kill (the unreachable pop-side gate survived the battery until deduplicated).
  Rule: enforce each guard at exactly one reachable point.

## Round 3 tri-lane re-review — LAUNCHED 2026-07-31
Three fresh Fable lanes (A theory-first, B premises-vs-data, C red team) launched against
the pinned frozen worktree at e2061ff. Each must return verdicts on all three axes (nine
approves total); defend protocol in force — no claimed defect accepted without executed
reproduction. Both builder deviations put to all three lanes for adjudication. Deploy
condition unchanged: nine approves AND Ryan's $600 deposit — no clock-waiting; first act
is the 120/480 probe.

## Round 3 Lane B (premises-vs-data) record — 2026-07-31
Verdicts: THEORY **APPROVE** · LOGIC/PREMISES **APPROVE** · IMPLEMENTATION **APPROVE**.
All by execution on e2061ff: 924/924 armed, 924 (4 skips) disarmed; independent
reimplementation of the g-derivation matched shipped tables ROW-FOR-ROW; side-split
proven real boundary-free (z=4.64 on 5-25c; NO more toxic in 37/47 cent buckets,
p=4.9e-5); every dials number reproduced to the digit (p_from_mix(0.197)=0.0900,
N=30, rail $16@480/$20@600, 819-day prior = exactly the ±1pp precision); all five
scoring premises match code; probe symbol check real and pages loud (scan-complete
gated). BOTH deviations ADJUDICATED AGREE: $16 rail is the note's own "$480 runs the
ordinary law book" ($20 would claim N=30, fund 24); z=3.8807 matched by independent
bisection, m=960 conservative vs actual per-family settle cadence (windows 15.5-87h).
Findings: MINOR per-family g-direction flips (metals/gas daily — cannot undercharge a
probe leg, bands there charge 0.65-1.0 both sides); MINOR calib mids are ≥60min-not-
exactly-60 before close; NIT stale pooled-g in dials.py:44 comment; NIT note's "232h"
is 230.8h. None blocking. Disclosed non-recomputables (correctly handled in build):
p=0.09 is note 54's prior + VPS-logged 19.7c ref; 42x tape is VPS-only and feeds no
constant. Lanes A and C still out.

## Round 3 Lane A (theory-first) record — 2026-07-31
Verdicts: THEORY **APPROVE** · LOGIC/PREMISES **APPROVE** · IMPLEMENTATION **REJECT**.
The reject: **F-A1 (MAJOR, orchestrator-confirmed at source)** — `probe.note_predictions`
has ZERO production callers (grep: definition + tests only; engine calls retune/clusters/
observe, never it) → on the wire `predicted` is always empty, ratio None, and the probe
verdict — the deploy plan's load-bearing instrument — can only ever say "report", never
PASS. Same defect class as the retirement bug: a hook with no production consumer, masked
by tests that hand-feed it. Fix has TWO halves: wire the planned credit (mq_entered's
credit_usd) in plan_marginal, AND pro-rate predicted to elapsed window — full-horizon
credit vs a 2-batch verdict fails even a perfect wall (false STOP).
Non-blocking: F-A2 MINOR — marginal.py's `rate<=0: break` termination clause is
unreachable/unkillable (entry+deepen both pre-filter positive rates; mutation to `if
False` survives the full 924) — the duplicate-guard trap again; F-A3 MINOR — alarm
family should count settled TICKERS not clusters (ladders settle many/day; at 60/day
effective family-wise alpha = 0.10 vs stated 0.05; worst case a false halt-and-flatten,
no money risk) — fix: m from funded tickers; F-A4 NIT — dials header worked example
stale again post-F2 (quotes pooled-g numbers).
Verified clean by its own execution: F1 sunk/rotation/rescue/deepen-derivative; full
dials chain; both g tables re-derived end-to-end; clamp monotone through PAVA (no
reorder, conservative every channel); probe cap/retune/place-substitution (if/elif, no
duplicate clause); phi min-only; toll. BOTH deviations ADJUDICATED AGREE with executed
counterexample for the rail ($20 in a $480 lane = 24 fundable clusters, z-day loss
20.68% — bound violated; $16 gives 19.45% — holds).
Round 3 score: A approve/approve/reject · B approve/approve/approve · C out.
Plan: consolidate A+C findings into ONE builder round on a new frozen commit.

## Round 3 Lane C (red team) record — 2026-07-31
Verdicts: THEORY **APPROVE** · LOGIC/PREMISES **REJECT** · IMPLEMENTATION **REJECT**.
Four MAJORs, ALL orchestrator-reproduced by execution before acceptance:
- **C-1 (deploy-blocking, fails closed): the B18 portfolio-variance rail refuses v6's own
  book.** PORTFOLIO_VAR_MAX=0.25 is a v5 constant derived for a ~12c mix (config's own
  derivation says so); the probe's 1-4c walls hit V=0.44 at $40 of one-cluster collateral
  → place() refuses the second wall; ~$43 of the $120 deploys; the plan never models B18
  → permanent plan-vs-place re-offer (the codebase's own forbidden state). The ordinary
  v6 cheap-floor book breaks it too (30×$16 at 2c → V≈1.05). The builder's F5 place-tests
  passed only because they construct PlaceContext WITHOUT portfolio_var_max — the real
  engine always arms it. Same magnitude as the original F5 ($120→~$43), one rail down.
- **C-2: the deepening arm buys negative-marginal contracts.** _block_to_rate bisects on
  AVERAGE rate from q0, not marginal; with heap empty + budget left it deepens past the
  interior max of net(). Reproduced: q=588 funded at marginal −0.079 vs greedy optimum
  q=279 — $1.42 net destroyed, $2.89 extra bleed, $27 extra capital at risk for negative
  return. Violates the module's own header invariant and the knee. The pinned block-vs-
  brute-force identity never leaves the budget-bound regime — fixture gap.
- **C-3 (surviving mutant, F2 site): the QUEUE's consumption of the side-split is
  unpinned** — mutant g_for_price(p,"yes") at marginal.py:217 survives all 924 (my rerun
  confirms). bleed.py's split is pinned; the ranking's use of it can silently regress.
- **C-4 (surviving mutant, F3 site): lane seeding unpinned** — `if False and` on the
  position-basis seeding survives all 924 (my rerun confirms). Money held by F5's place
  cap, but the plan re-offers $40 instead of $5 and the F3 fix has no named killer.
MINOR: alarm 2 pages a genuine 2×-bleed bug at median ~583 settlements ≈ 19 days — it is
a program-scale backstop; the per-fill realized-vs-table proof-gate stays the day-scale
manned instrument. WHAT HELD under attack (all executed): the entire safety law (s1-s4),
rails a1-a7, probe restart/replay accounting p1-p3, queue no-double-charge + bleed<paid
over 400 fuzz slates, all absence-as-evidence sites, 16/18 of its new mutants killed
incl. all safety guards. BOTH deviations ADJUDICATED AGREE by execution (rail: $16 holds
19.45%≤20%, $20 breaks at 20.68%; alarm z derived-not-picked, 0 false pages in 300
simulated programs, authorized probe worst case does NOT page).
**Round 3 final score: 7 of 9 axes approve-equivalent, 2 reject. Five MAJORs to fix:
F-A1, C-1, C-2, C-3, C-4.** Consolidated Round 4 dispatched to the builder on top of
e2061ff; tri-lane re-review to relaunch on the new frozen commit.
**For Ryan (C-1 design point, spec-derived resolution):** the probe is exempted from B18
because note 55's deploy plan explicitly prices its concentration (d×C, "not worry about
risk"); PORTFOLIO_VAR_MAX for the ordinary book gets re-derived from the v6 dials frame
(N=30, d=20%, floor mix) instead of the inherited v5 12c-mix constant — builder derives,
reviewers re-adjudicate, and this line is the flag if Ryan wants it decided differently.

## Round 4 record — 2026-07-31 (frozen 02b6ed8, then R4-6 sent back)
Builder delivered all five MAJORs + five minors on 02b6ed8: 967 green armed AND disarmed;
battery 50/50 on a verified-green baseline. R4-1: verdict wired under the production call
pattern AND re-based to a RATE vs elapsed hours (full-horizon bar would have STOPped a
perfect wall at 0.083). R4-2: B18 in the plan (var_room_usd), probe exempt via guards'
`exclude` callback, tolerance re-derived V_max=(1−p)/(N·p) — reproduces ~0.25 at the v5
12c mix (proof it's the same statement, not a loosening); all six walls place, $120 of
$120, seventh stops on probe_lane_cap. FLAGGED for lane re-adjudication. R4-3: block
tests each contract's own discrete rate; repro lands exactly on the greedy optimum.
R4-4/R4-5: named killers (my re-runs confirm — M02 killed; the true seeding site killed
when I re-targeted the mutant at it). m2 moves the alarm to settled-ticker family:
m=1,920, z=4.0461.
**Builder's confession, propagate everywhere: the in-place mutation harness fails GREEN**
— a timeout between write and restore left a stub in the tree, one test failed in every
later run, every mutant looked killed. Two earlier battery rounds invalidated; harness
now mutates a COPY and asserts a green baseline first. Four genuine survivors surfaced
on the clean re-run, all pinned.
**My spot-check found R4-6:** the PLAN-side probe exclusion in cluster_var_book
(engine.py:288) is an unpinned clause — `if False and` there survives all 967 (my
execution). Conservative direction today (probe fills would count as concentration →
plan under-offers → fails closed), but it is a surviving mutant in a fix site AND the
exclusion is expressed TWICE (guards' exclude callback + this inline clause) with the
identity untested — the divergence path back to plan-offers-what-place-refuses. Sent
back for a named killer + single-expression (or identity test). Lanes relaunch on the
refrozen commit.

## R4-6 refrozen + Round 4 tri-lane re-review LAUNCHED — 2026-07-31
Builder refroze at **36de3dd**: 976 green armed AND disarmed; battery 54/54 copy-based on
green baseline. Fix took the single-expression shape: guards.cluster_book = the ONE
aggregation (called by portfolio_variance AND engine.cluster_var_book), Maker.var_exclude
= the ONE exclusion predicate (read by plan and place_context). Pinned by EFFECT (probe
positions change what the plan offers, in dollars; plan-side == rail-side cluster-for-
cluster and v to 12 places; structural test forbids cluster_var_book growing its own
loop). My mutant re-run on 36de3dd: FAILED (failures=2) — killed. Bonus from the
collapse: a PRE-EXISTING untested clause (the unpriced-leg 0<basis<1 guard, predating
R4-2) became visible and is now pinned — the one-expression doctrine catches old debt,
not just drift. Three fresh Fable lanes launched on 36de3dd (A theory, B premises, C red
team), briefed on all Round 4 re-derivations to adjudicate (V_max=(1−p)/(N·p), verdict
rate-basis, discrete marginal rate, z=4.0461@m=1920) and on the harness fail-green
confession (battery claims to be independently re-run). Nine approves required.

## Round 4 re-review, Lanes A + B records — 2026-07-31 (frozen 36de3dd)
**Lane A: THEORY approve · PREMISES reject · IMPL reject. Lane B: THEORY approve ·
PREMISES reject · IMPL reject.** Lane C still out. Both flagged re-derivations
ADJUDICATED AGREE by both lanes with independent derivations (V_max=(1−p)/(N·p)
reproduces the v5 0.25 at its own calibration point — the old constant WAS this formula
at 12c/N=30; alarm family/z matched to 1e-6).
Three MAJORs, all orchestrator-confirmed at source:
- **RV-1 (Lanes A+B CONVERGENT, blind — the strongest signal): the probe verdict can
  false-PASS.** earned = LIFETIME sum(last_accrued) but predicted = rate × hours since
  first_seen; the baseline at first read is stored and never subtracted. Any mid-window
  restart (Probe rebuilt in Maker.__init__; restarts are normal here) re-seeds the
  baseline with pre-restart accrual → dead wall at 2.5-5% of promise verdicts PASS
  (A: ratio 6.275; B: ratio 5.05 — independent fixtures). The instrument that tells
  Ryan to scale, failing in the scale direction. Fix: score the delta since first read.
- **RV-2 (Lane A): R4-3's negative-marginal defect RESURRECTED by the m1 refactor.**
  runner_up = -heap[0][0] unclamped + m1's unconditional deepen push → heap legally
  holds negative rates → _block_to_rate floors the top rung at a NEGATIVE rate, past
  its interior optimum. Repro: q=460 vs greedy 122, $9.03 net destroyed; 400-slate fuzz:
  36/400 mismatches incl. bleed>paid violations (worst $479.68 vs $47.74). The one-line
  clamp max(0.0, ·) leaves all 976 green — fixture gap proven. Confirmed at source:
  the m1 comment relies on the pop-level rate<=0 break, which never guards the FLOOR.
- **RV-3 (Lane B): B18 plan-room prices the increment at the ORDER's price; place()
  prices the BLENDED cluster.** Conservative only for cheap-into-expensive; a requote
  after drift or a richer ladder strike over-offers → place refuses → permanent re-offer
  (pure function, same plan next cycle). Executed engine-realistic: plan funds $9.08,
  place refuses at v_next 0.1442 > 0.1381. Fails closed. Confirmed at source — the
  code's own comment concedes px is between the two and defends one direction only.
  Fix: room on the post-add blended price or plan calls portfolio_variance itself.
Non-blocking: A-MINOR Jensen gap in V_max on price-dispersed mixes (conservative);
A-MINOR/B-NIT alarm family vs intraday ladders (gas daily 15.5h windows → realized
alpha drifts toward 7-8% on a gas-heavy book; hourly ladders worse if funded);
B-MINOR dials 45c prose stale THIRD instance (pinned test carries correct values,
prose doesn't); B-NIT denominator asymmetry max(ceiling,deployed) vs ceiling-strict
(unreachable under B15). Verified clean by both lanes: verdict rate-basis correct
(healthy wall 0.975 PASS where full-horizon bar false-STOPped at 0.081), spine intact
(F1 rotation, rescue, dials chain, side-split consumption, phi min-only, toll), R4-6
pinned both halves (B's own mutants: 7 and 3 failures).

## Round 4 re-review, Lane C record — 2026-07-31 (frozen 36de3dd)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**. Six MAJORs; three
CONVERGE with A/B (verdict false-PASS found by ALL THREE lanes blind; negative floor by
A+C — C's repro is worse: q=3253 vs greedy 166, net −$21.40, bleed EXCEEDS paid, the
pinned invariant falsified; plan-room price basis by B+C). Three new, all
orchestrator-confirmed:
- **C4-1: B18 stuck-state refuses the PROBE.** The tolerance MOVES with dials.mix_price
  daily while positions ride to settlement; a book built legally at a 2c mix (V=0.209)
  meets a 19.7c tolerance (0.1359) next day → place() refuses EVERYTHING absolutely —
  including probe walls whose v_next == v_now, falsifying guards' own "the check cannot
  refuse it" comment. Deploy's first act places $0 in this state. Source-confirmed: the
  exclusion guarantees v_next == v_now, NOT v_next ≤ max — deleting the "unreachable"
  probe clause per mutation doctrine removed the property without testing it first.
- **C4-3: resting probe walls are invisible to the plan's cluster ledger** (positions
  only, engine.py:1161-1168) → a mid-priced same-family leg is plan-funded and
  place-refused (cluster_admits counts resting) → re-offer. Source-confirmed.
- **C4-6: surviving mutant in the R4-1 fix site** — note_predictions max→last passes
  all 976 (my re-run confirms). The max-not-last semantic is documented load-bearing.
What held: entire safety law rerun, rails, probe cap incl. restart restaking ($92-108 ≤
$120), F1, alarm 0/300 false pages, builder battery 54/54 re-run independently, both
deviations again. All three lanes AGREE on V_max derivation and z — every REJECT is in
the integration seams (plan/place parity, verdict accounting), not the theory.
**Round 4 score: 3 THEORY approves, 0/6 on premises+impl. Six consolidated defects
(RV-1..RV-6) dispatched as Round 5.** Process lesson pinned for the vault: TWO of six
were RESURRECTIONS caused by Round-4 fixes themselves (m1's unconditional push exposed
the unclamped floor; the unreachable-clause deletion removed a property with no named
test). New rule: before deleting a guard as unreachable, write the test that pins the
PROPERTY it expressed; a refactor that widens a domain must re-run the invariants of
everything reading that domain.

## Round 5 — frozen 27544f6, 2026-07-31
All six RV defects fixed on one commit: 1007 green armed AND disarmed; battery 65/65
copy-based. My spot-checks: suite green; all three Lane C finding-fixtures now FAIL
(= defects gone — negfloor lands on greedy, verdict REPORTs through restart both
directions, probe wall places in the stuck-tolerance state); NC15 mutant killed (2
failures). Notables: (1) TWO self-found units bugs while pinning RV-3 — variance book
charged turnover-inflated capital instead of resting collateral, and room min'd against
capital-denominated terms unconverted; both silent under-offers at T>1, both pinned by
a T=2.4-vs-T=0 same-size fixture. (2) n3's real fix: token-presence tests let stale
prose survive — the test now carries a BLACKLIST of superseded numbers (0.0617, $21.43,
N=28...) so rot fails wherever it happens. (3) The resurrection rule is adopted: the
probe-unrefusability property has a named test; property test BEFORE deleting any
guard as unreachable. (4) **Builder COUNTER-DERIVED RV-5's fix shape, with evidence:**
seeding plan ledgers from resting_basis breaks the ordinary book — at steady state
resting collateral IS the plan's own allocation (requoter cancels what's not in it), so
counting it → next pass plans $0, all markets cluster_rail_full → whole-book oscillation
every cycle (measured: $16.00 → $0.00). Real inconsistency was one-directional: probe
was exempt from the rail but the rail wasn't exempt from the probe. Fix: cluster_admits
excludes probe rows when judging a NON-probe order — symmetric with B18's exclusion,
requoting untouched, both the fixture and the oscillation evidence in the suite. FOR
THE LANES TO ADJUDICATE: does the symmetric exclusion open a concentration hole
(ordinary rail + probe walls stacking in one cluster), or is that exactly the priced
d×C + rail split the spec already states? Round 5 tri-lane re-review launching on
27544f6.

## Round 5 re-review, Lane A record — 2026-07-31 (frozen 27544f6)
Verdicts: THEORY **APPROVE** · PREMISES **APPROVE** · IMPL **REJECT** (one MAJOR).
- **A5-1 (MAJOR, orchestrator-confirmed at source): RV-1's delta breaks at the WINDOW
  ROLLOVER.** Feed accrual resets to 0 at the program-window boundary; the reset itself
  counts as a batch (abs(acc−prev) trips), max(0, acc−baseline) floors earned at $0
  against the stale baseline, and the verdict fires PERMANENT REPORT on a healthy wall
  (true ratio 0.86 → earned $0.0000). Conservative (cannot false-PASS) but the false-
  STOP class again, reachable whenever boot lands within hours of a boundary — and both
  probe families run daily windows. Zero tests feed decreasing accrual. Fix: on
  decrease, bank (prev−baseline) into a realized accumulator, re-base baseline=0,
  don't count the reset as a batch; rollover fixture.
- A5-2 MINOR: RV-5's exclusion missing at the plan's ledger SEEDING (positions counted
  unexcluded) — plan tighter than place (conservative, no re-offer possible), but the
  one-expression doctrine broken at the exact seam RV-5 fixed; same exclusion + named
  test. A5-3 MINOR: placement-ORDER transient — legal final book, V-raising order
  placed before its sibling repair refuses for ONE cycle then heals (3/500 slates);
  rate-ordered placement would close it. NITs: ratio exactly 0.500 passes (per spec);
  an entirely-dead probe family never batches → silence not REPORT (predates R5,
  operator-watched).
- Clean: RV-2 via independent greedy oracle 400 slates 0 violations; RV-3 units; RV-4
  property under stuck/full states; spine re-verified; all four R5 fix-site mutants
  killed on its own re-run.
- **RV-5 counter-derivation ADJUDICATED: builder RIGHT, both halves.** Oscillation
  reproduced mechanically ($16→$0→$16 six-cycle sim — the plan charging itself for its
  own output); the symmetric exclusion opens NO hole: worst stack $135.90 on one settle
  fact = 22.6% of C, each dollar charged by exactly ONE instrument (probe by d×C which
  already assumes 100% loss, ordinary by the rail inside the z-day bound) — the
  pre-RV-5 behavior was a DOUBLE-CHARGE, same class F7 removed from the mix.

## Round 5 re-review, Lane B record — 2026-07-31 (frozen 27544f6)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**.
- **B5-2 (MAJOR, CONVERGES with A5-1 — blind, second convergence on this instrument):
  the verdict false-REPORTs at any accrual DECREASE.** Not just window rollover: a
  settlement PAYMENT (engine.credit_paid subtracts paid credit from accrued) produces
  the same negative delta → wall earns exactly its promise, gets paid, verdicts REPORT
  $0.0000 vs $2.0167 (control without boundary: PASS 1.00x). Both probe families settle
  daily; the verdict is specced to land within one settle cycle — the boundary is the
  NORMAL case, not the edge. Fix: bank prev−baseline into a realized register on
  decrease, rebase, don't count the reset as a batch, cap elapsed at the window.
- **B5-1 (MAJOR, new): the VARIANCE ledger re-creates the oscillation RV-5 fixed in the
  cluster ledger.** cluster_var_book seeds positions PLUS RESTING (source-confirmed);
  the plan then charges its own candidate offers on top → at steady state the plan
  double-charges its own allocation → period-2 whole-book size oscillation, measured
  $478.71 → $121.16 → $478.71 at the designed book, carried to the wire every ≥30s.
  Fails toward under-deploy/churn. Fix = the builder's own doctrine: var book seeds
  from positions only (probe-excluded), matching cluster_spent.
- B5-3 MINOR = A5-2 (convergent): cluster_spent seeding lacks the probe exclusion —
  plan tighter than place, ordinary lane locked out of probe families while probe
  positions ride. B5-4 MINOR: the n3 blacklist scans dials.py only — the superseded
  $21.43/N=28 numbers are alive in test_dials.py:5 and test_quiet.py:176 prose. B5-5
  DEPLOY-CHECKLIST FLAG: earned sums per-PROGRAM accrual once per watched TICKER — if
  a probe family's program spans strikes, earned inflates by strike count (false-PASS
  direction); verify program→ticker cardinality on the live feed before trusting PASS.
- Units audit at T=2.4 (its special focus): CLEAN end-to-end, every ledger matches
  hand-derivation to 1e-9; plan strictly tighter at T>1 by design. RV-1/2/3/4/6 all
  verified pinned by its own mutant re-runs. **RV-5 counter-derivation ADJUDICATED
  AGREE (second lane): premise true in code (requoter cancels off-allocation), max
  one-settle-source stack $136 = 22.7% of C is the spec's own priced d×C + rail split;
  the pre-fix counting was an accident, not a bound.**

## Round 5 re-review, Lane C record + orchestrator adjudication — 2026-07-31 (27544f6)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**.
- C5-1 MAJOR: plan/place variance parity is PATH-DEPENDENT — plan charges its var book
  in heap-pop order (in-pass repairs unlock cross-cluster room), the wire places in
  slot order → 5/3000 engine-realistic fuzz trials hit plan-funds/place-refuses without
  a stuck book. Usually self-heals in one cycle (= Lane A's A5-3, convergent
  observation), but the doctrine bans the state and a stable refusal fixed point is not
  excluded. Fix shapes: place in plan order / judge plan against raw pre-pass book /
  batch-atomic admission.
- C5-2 MAJOR (battery rule): max(0,·) clamp in earned_usd is an unpinned clause in this
  round's own fix — survives all 1007. Conservative-only direction proven, but the
  resurrection rule forbids exactly this. Named killer required.
- C5-3 MINOR = A5-2 = B5-3 (TRI-convergent): cluster_spent seeds probe fills unexcluded
  → the mid leg RV-5 deployed is refused again ONE FILL EVENT later.
- C5-4 MINOR: reset-counts-as-batch → perfect wall REPORTs at 0.0 (same family as
  A5-1/B5-2; B's payment path makes the negative delta DOCUMENTED code behavior, so
  the tri-convergent MAJOR stands).
- **C5-5 MAJOR (PREMISES) — LANE SPLIT, adjudicated by orchestrator:** C executed the
  stack: one settle fact can carry $135.75 (22.6% of C), both-family fact-day $152
  (25.3%), vs note 55's sentence "worst case of the $120 = exactly d×C." A+B priced
  the day-UNION (accepted since Round 3: ~$213 both-die-day); C is RIGHT that the
  SINGLE-FACT sentence is falsified — pre-RV-5 the rail accidentally enforced lane
  separation per cluster; the fix removed it. **RESOLUTION (mine, conservative, keeps
  Ryan's sentence true, FLAGGED and reversible): bar ordinary entries from probe
  clusters while the probe is ARMED — expressed in BOTH plan and place (no re-offer
  state). Costs 2 of 30 clusters for the probe's ~one-settle-cycle life; restores
  single-fact worst to exactly $120 = d×C. RYAN: if you'd rather re-price the sentence
  (accept 22.6% single-fact) and keep the 2 clusters, say so — one-line reversal.**
  Also: exclusion has no retirement path (verdict-complete probe positions stay
  rail-excluded until settlement, bounded ~daily) — the armed-bar subsumes this.
What held: RV-2 vs independent oracle 0/400; RV-4 admit rule pointwise sound (proof
sketch: ceiling ⟺ v_next≤max OR ≤v_now); stuck-walk lands $70.20 all inside rails, V
monotone down; no attacker basis reaches the wire; 15/16 new mutants killed; disarmed
fallback exact.

## Round 6 — frozen 50303e0, tri-lane re-review launched — 2026-07-31
Builder delivered all five on one commit: 1029 green armed AND disarmed; battery 80/80;
Lane C's fuzz 0/3000. My spot-checks: suite green; R6-2 confirmed at source (var ledger
positions-only, delegated to guards.cluster_book); R6-1 verified by execution (healthy
wall through rollover: banks $0.20 realized, reset NOT a batch, verdicts PASS at true
ratio 0.86). TWO COUNTER-SHAPES shipped, both flagged to the lanes:
- **R6-4 counter-shape:** instead of ordering/pre-pass/atomic, each cluster held to the
  designed book's own share v_max/N — order-independent BY CONSTRUCTION (v_max IS N
  equal clusters at v_max/N each), survives interleaved fills. Getting there surfaced:
  the plan had NEVER modeled B16 (158 of the 142-trial fuzz refusals were market_cap),
  and the FUZZ HARNESS itself diverged from production (planned against rail only) —
  corrected to min(rail, market_leg_cap). Harness-vs-production parity is now a lane
  verification item.
- **R6-5 counter-shape (replaces my adjudicated armed-bar):** the note's sentence AS AN
  INVARIANT — no single settle source carries more than d×C counting BOTH lanes, one
  predicate read by plan and place. Probe empty → costless; fills → ordinary tightens;
  measured: mid leg admitted on empty, refused single_fact_cap at $120 full; single-
  fact total EXACTLY $120 = 20% = d×C. Builder's evidence against my bar: KXAAAGASD is
  both a live probe family and the canonical fixture family — the bar broke 55 tests
  and forfeits a top-earning quiet-class gas LADDER (not 2 clusters) even when the
  probe holds nothing. Also new: ARMED vs DRAINING — disarm does not make probe
  collateral ordinary; rows stay probe-governed to settlement while active bounds
  switch off. OPEN QUESTION dispatched to all three lanes: SEQUENCING — the cap refuses
  new orders only, positions ride and never sell; who gets refused when the other lane
  got there first, and can fills of already-resting orders on both lanes push a fact
  past $120 (checked at place, exceeded at fill)? Round 6 tri-lane launched on 50303e0.

## Round 6 re-review, Lane B record — 2026-07-31 (frozen 50303e0)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT (FATAL)** · IMPL **REJECT**.
- **B6-1 (FATAL, orchestrator-confirmed at source): THE PROBE CANNOT SEE THE REAL
  TREASURY BOARD.** is_probe_family matches the CLUSTER key; CLUSTER_MAP rewrites all
  five real treasury wire series (KXUST2AD/5AD/7AD/10AD/30AD) to "RATES"; no
  PROBE_FAMILIES prefix matches "RATES". On deploy night: the $120 probe's treasury
  half NEVER EXISTS (real tenors fund as ONE $10 ordinary order — RATES is a single
  cluster), the verdict silently scores on gas alone, and the armed-zero-slots page
  does NOT fire (needs zero matches; gas matches). Survived SIX ROUNDS because every
  probe fixture uses "KXUST-" symbols that dodge the map — the fixture-vs-wire
  divergence NO plan/place parity fuzz can see. This was the pending deploy-checklist
  item ("probe-family live-board symbol check") — it is not a checklist item, it is a
  code defect.
- **B6-2 (MAJOR, Round-6-introduced, confirmed at source): DRAINING is a plan/place
  split** — marginal.py never consults `armed` (zero occurrences); ProbeLane.admits
  refuses everything probe_disarmed. Post-verdict (the NORMAL path, within one settle
  cycle): plan funds gas walls with zero refusal reasons, place refuses all, permanent
  re-offer on the top-earner family forever after the verdict.
- B6-3 MINOR: in-pass probe charges consume the ordinary in-pass cluster ledger
  (R6-3 fixed the seed, not in-pass) — same lockout class, plan tighter. B6-4 MINOR:
  "single settle source" = cluster_of = one SERIES; gas daily full ($120) + gas weekly
  ordinary leg ($15.90) on a shared Friday closing fact = $135.90 = 22.6% — the exact
  number the sentence forbids, re-entered through cluster identity. B6-5 MINOR: the
  $120 never returns to the ordinary lane after full probe settlement (rail stays $16
  not $20 forever; conservative, compounds B6-2).
- Verified clean: R6-1 all seven negative-delta paths (incl. double-rollover, payment-
  then-rollover, elapsed capped on program_end_ts — the right stamp); its own 1,200-
  slate engine-realistic fuzz 0 refusals/13,696 plan-funded orders (its first run's 459
  hits were its OWN harness artifact — honest correction); T=2.4 B16 parity; draining
  ledger behavior; blacklist now package-wide, stale prose gone.
- **R6-4 counter-shape ADJUDICATED AGREE** (share bound = v_max one level down; total
  V ≤ v_max in any arrival order; B16-in-plan belt-and-braces correct direction).
  **R6-5 invariant ADJUDICATED the better resolution** (bar was genuinely mispriced —
  a ladder, not 2 clusters) — but its LIFECYCLE (B6-2) and its IDENTITY FUNCTION
  (B6-4) are not done.

## Round 6 re-review, Lane A record — 2026-07-31 (frozen 50303e0)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**.
- **A6-1 (MAJOR): the single-fact invariant is ONE-SIDED.** Enforced only on ordinary
  orders (place routes probe orders to ProbeLane.admits = lane cap only; plan gates the
  fact check on lane != probe). Mirror sequencing executed: ordinary $16 lands FIRST →
  all six probe walls admitted → fact $136.00 = 22.7% — C5-5's number back through the
  other lane's door. Also fully-live path: probe 100/20 split, sibling family settles,
  restake into gas admitted, $136 end-to-end THROUGH THE ENGINE. Fix is small:
  ProbeLane.admits mins against single_fact_room_usd (max cost $16 of walls deferred).
- **A6-2 (MAJOR, = B6-2 CONVERGENT, sharpened): `disarm` has ZERO production callers**
  (grep: probe.py:260 + tests only — the verdict landing does NOT call it; the F-A1/
  retirement orphaned-hook class AGAIN) — and when invoked, plan never reads `armed`
  (funds all 4 walls) while place refuses all 4 probe_disarmed: permanent forbidden
  state. Mutant deleting the disarmed refusal survives all 1029 — the draining claim
  is unpinned AND broken AND unreachable.
- **A6-3 (MAJOR, battery rule): two surviving mutants in Round-6 fix sites** — M4
  in-pass resting charge (R6-5's plan half) `if False` survives (re-offer class);
  M10 B16-in-plan reverted survives AND is proven arithmetically INERT (rail ≤ 2.7%C
  < B16 ≥ 10%C at every reachable geometry — min always picks rail; the unkillable-
  clause class the doctrine deletes).
- MINORS: m1 share-bound residual coupling (positions-fat cluster zeroes siblings'
  room — conservative; "no cross-cluster coupling" claim overstated); m2 share-bound
  breadth cost on dispersed cheap books (2c cluster clamped $5.77 vs $16 rail; zero
  cost at-mix — equal-VARIANCE displacing equal-DOLLAR semantics, on the record next
  to the Jensen note); m3 stuck book can no longer self-repair through the plan (room
  $0; place would admit the repair; settlement-clock recovery only — conservative,
  priced); m4 check_silence LATCHES (wall dead 6h then healthy earns $8 — verdict
  stays "silent" forever, a genuine PASS can never land). NITs: double-init in
  Probe.__init__; plan-time vs place-time projected_day_reward (unreachable today).
- Verified clean: R6-1 algebra exact on five fixtures; R6-2 END-TO-END through engine
  seeding (oscillation dead: 460.34/458.79/458.79); R6-3; B16 parity ⊆ place; its own
  3000-trial rerun of C5-1's harness: 0/3000 (the path-dependence defect is DEAD).
- **R6-4 ADJUDICATED: ACCEPT with the claim trimmed** (not fully order-independent —
  couples via others, shrink-only; breadth cost real on cheap books; repair channel
  forfeited — all conservative and priced). **R6-5 ADJUDICATED: ACCEPT the shape,
  REJECT the shipped enforcement** (invariant is the honest reading of Ryan's
  sentence; one-sided enforcement falsifies the letter).
Lane C still out. Round 7 will consolidate: B6-1 FATAL (probe blind to treasury),
A6-1/B6-4 (two doors on the fact bound), A6-2/B6-2 (draining orphan+split), A6-3
survivors, B6-3/B6-5, minors.

## Round 6 re-review, Lane C record — 2026-07-31 (frozen 50303e0)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**.
- **C6-1 (= A6-1 CONVERGENT, + engine-real): the fact bound is enforced only against
  the SECOND lane, and only if ordinary.** Deploy-night state executed: boot with $16
  of INHERITED v5 gas positions (the actual live book) → probe stakes $105.07 on top →
  fact $121.07 > d×C, CERTAIN on deploy night, not sequencing luck. Fills never breach
  on their own (a fill converts, never adds) — the breach is entirely at placement
  through the probe branch.
- **C6-2 (= A6-2/B6-2 TRI-CONVERGENT, + starvation): draining unreachable (disarm has
  zero production callers), unpinned (M13 place-guard survivor), and when entered the
  phantom probe allocations consume the ONE global budget — ordinary book STARVED of
  $105.01 vs control, 156/3000 fuzz, permanent.**
- **C6-3 (MAJOR, false-PASS direction): M3 survivor — `banked = prev` (baseline
  dropped in reset banking) passes all 1029**; dead wall at 1.7% of promise with a
  pre-watch baseline verdicts PASS 0.93 through R6-1's own reset path. Unpinned.
- **C6-4 (= A6-3/M10 CONVERGENT): B16-in-plan PROVABLY INERT** (market_leg_cap ≥
  0.1C, rail ≤ 0.027C — min always picks rail) and the round record's "142→0/3000"
  claim belongs to the HARNESS correction, not the code (old divergent harness still
  shows 127/3000 on this commit). Fix-site survivor + false claim in the record.
- C6-5 MINOR: "order-independent by construction" overstated — 1-4/3000 boundary-thin
  refusals under ADVERSARIAL order via intra-cluster blend-price (0/3000 under the
  engine's actual deterministic order — fixed where the wire walks). C6-6 MINOR:
  flapping-feed inflation (every decrease banks as realized — 6× over 50 flips;
  pathological feed, false-PASS direction). A-m4 (Lane A): check_silence LATCHES —
  dead-6h-then-healthy wall can never PASS. NITs: double-init; re-entry dead-gap.
- Battery note: builder's 80-mutant battery NOT in the tree — unverifiable; Lane C's
  own 15 substituted (12 killed, 3 survivors, all in R6 fix sites). RULE for Round 7:
  the battery ships IN THE TREE.
- Held: safety s1-s4, rails, refusal-only everywhere (no sell/cancel of riding
  positions on any new guard), R6-1 clean on all monotone+restart paths, R6-2 dead,
  finding-fixture inversion sweep across ALL prior rounds correct.
**Round 6 score: 3 THEORY approves; premises+impl 0/6. Round 7 consolidates: B6-1
FATAL (treasury-blind probe), two-lane fact bound + identity, draining wiring, three
fix-site survivors, B16 inert-clause deletion, minors.**

## Round 7 — frozen e7d74c2, tri-lane re-review launched — 2026-07-31
All six on one commit: 1066 green armed AND disarmed; battery 96/96 AND NOW IN THE TREE
(lip_v5/battery/run.py); parity fuzz 0/3000. My spot-checks: suite green; real treasury
tenors probe-eligible (KXUST10AD/2AD → True); disarm has TWO production callers
(verdict + silence); the fact bound enforces BOTH directions (ordinary-first defers
probe wall 5 at room $4 → fact $116 ≤ $120; probe-first refuses ordinary at room $0);
fills convert-never-add; disarmed plan funds ordinary in the former probe cluster
(starvation gone). R7-1 fixed at the IDENTITY level: series_of (the wire's name)
answers family membership, cluster_of (the risk name) answers a different question —
"symbols are premises" is now a tests rule with the real five tenors pinned. R7-2(b)
COUNTER-DERIVED: facts group by UNDERLYING, not (underlying, settle-day) — day-aware
needs a settle timestamp for every held row (close cache can be stale/missing) and a
bound that silently splits on an unreadable timestamp FAILS OPEN; underlying-grouping
fails CLOSED (defers dollars on genuinely-independent windows — priced, logged). R7-6:
inert B16 min-term DELETED with the algebra + a synthetic-geometry cross-fire test;
record corrected (the 142→0 was the harness). Builder found two more while fixing:
B6-3 was still live (in-pass probe charges eating the ordinary rail — now ONE predicate
rail_exempt), and lane_of didn't consult armed (disarmed probe kept multi-market
licence + rail exemption = $40 in one cluster — both now gated, isolated tests).
PROCESS: several cross-round survivors trace to scripted edits that silently no-op'd
on drifted anchors — `assert old in s` now on every scripted edit. Tri-lane launched
on e7d74c2; the fact-identity counter-derivation is the round's main adjudication.

## Round 7 re-review, Lane A record — 2026-07-31 (frozen e7d74c2)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**. All confirmed by
orchestrator at source (+ A7-1's key symbol confirmed from v5's own LIVE BOOK —
KXAAAGASW-26AUG03 positions are riding right now).
- **A7-1 (MAJOR): FACT_MAP misses the REAL gas siblings.** Lane A queried the live
  Kalshi API: KXAAAGASW (weekly) and KXAAAGASM (monthly) are real open series; FACT:GAS
  members are (KXAAAGASD, KXNATGAS, KXGAS, KXNGAS) — the map's own comment states the
  intent, the member list omits the real symbols, and the shipped fixture "proves"
  cross-series binding with the INVENTED KXNATGAS — the symbols-are-premises rule
  violated inside the fix that cites it. Executed: probe full $120 + weekly $15.90
  admitted = $135.90 = 22.65% through the real sibling.
- **A7-2 (MAJOR): the ordinary-side gate is SERIES membership, not FACT membership**
  (bars_ordinary = armed AND is_probe_family) — with the map patched, the weekly leg
  is STILL admitted; two independent doors. Fix: gate on fact_of. (+ KX7Y in FACT_MAP
  but not PROBE_FAMILIES — alias asymmetry.)
- **A7-3 (MAJOR): draining is not restart-durable.** Probe is unconditionally rebuilt
  ARMED from config flags at Maker init; disarm has zero persistence consumers.
  Verdict REPORT → restart → armed=True, new wall places; after settlement a restart
  stakes a FRESH $120 into a falsified thesis. Money bounded (lane seeds from replayed
  positions, ≤ d×C instantaneous) but note 55's "If not → STOP, nothing else deploys"
  is held only in RAM. Fix: persist disarm (ledger row consumed at replay, or verdict
  re-derived from world facts at boot).
- A7-4 MINOR: draining-window plan-funds/place-refuses on wing-priced family legs
  (~$32, self-heals at release — same seam as B6-2, fails closed). NITs: stale
  cluster_of prose at config.py:1624/README:400.
- Verified clean: R7-2 deploy-night + mirror engine-real both directions; R7-3 all
  three transitions disarm in production, walls ride, silence unlatches, release →
  rail $20; battery 96/96 with 0 anchor-skips (its own re-run); five tenors = one
  FACT:RATES adjudicated CORRECT under the one-curve frame.
- **Fact-identity counter-derivation ADJUDICATED: shape ACCEPTED by construction**
  (refusal-only bound → over-grouping is provably one-sided-conservative; cost ≤ ~$32
  deferred ≤ one settle cycle) — **but the shipped input is rejected**: it swapped a
  runtime input for a static symbol map that is missing the very siblings the doctrine
  names, and the gate re-introduces the wire-vs-risk confusion R7-1 diagnosed.
  FACT_MAP joins PROBE_FAMILIES in the live-board symbol test.

## Round 7 re-review, Lane B record — 2026-07-31 (frozen e7d74c2)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**.
- **B7-1 (MAJOR, = A7-1+A7-2 BLIND-CONVERGENT, proved from the tree's OWN DATA):**
  calib2.json contains exactly KXAAAGASD (gas daily), KXAAAGASW (gas weekly, 83 rows),
  and the five KXUST*AD tenors — FACT:GAS instead carries three INVENTED aliases
  (KXNATGAS/KXGAS/KXNGAS) and omits the real weekly. $136 = 22.67% executed through
  place_allowed. ROOT CAUSE NAMED: KXAAAGASD is the AAA RETAIL GASOLINE index, not
  natural gas — the map comment's wrong underlying is how the aliases got invented.
  Second door confirmed independently: the gate keys on PROBE_FAMILIES membership,
  not fact identity — must be fact_of(ticker) ∈ probe facts, expressed once.
- **B7-2 (MAJOR, new): the absent-family page fires per PREFIX and 8/10 prefixes are
  dead.** On a fully healthy board (all real tenors + gas live) the urgent
  probe_no_families page fired on EVERY plan pass ("8 of 10 matched ZERO"), raw ntfy,
  no latch. A page that always fires on a healthy night trains the operator to ignore
  it — burying the genuine absent-family signal it exists for. The passing test
  invents live markets for all eight dead aliases.
- **B7-3 (MAJOR, = A7-4 upgraded): the draining-window plan/place split is CERTAIN,
  not an edge.** All live treasuries price 1-2c ≤ the 4c wing edge, so post-verdict
  the plan funds ordinary-shaped cheap rungs in the families ($9.63 gas + $7.82
  KXUST10AD executed) and place refuses probe_disarmed — after EVERY verdict, until
  the last probe row settles. R7-3's tests dodge it (15c mid / S=0 walls).
- MINORS: README deploy checklist still says cluster_of (the artifact the operator
  follows); probe.py claims a cardinality checklist item that doesn't exist.
- Clean: battery 0/96 (its own re-run), R7-2 both doors on real tenors, R7-3d release
  exactly-once (rail 16→20, probe→None), verdict trichotomy end-to-end through
  m.cycle (PASS 1.00 / REPORT 0.0007 / silence unlatch), spot checks all hold.
- Fact-identity adjudication: underlying-grouping RIGHT and error-direction verified —
  but fail-closed is CONDITIONAL on membership completeness, and membership was
  checkable against the tree's own data. The doctrine holds; the input didn't.

## Round 7 re-review, Lane C record — 2026-07-31 (frozen e7d74c2)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**.
- **C7-1 (= A7-1/B7-1, THIRD blind confirmation + the sharpest facts):** live API —
  KXAAAGASW/KXAAAGASM exist (AAA gasoline, same underlying as the probe family);
  mapped alias KXNATGAS is a **404**; KXNATGASW/KXNGAS are DIFFERENT underlyings
  (Pyth/EIA natural gas) wrongly grouped INWARD. $136 executed. N13 survivor:
  fact_of's prefix semantics unpinned against a real suffixed sibling.
- **C7-2 (= A7-4/B7-3): draining routing split** — plan routes on armed, place routes
  on world-facts → wing-priced family candidates plan-funded ordinary, place-refused
  probe_disarmed; 6/6 cycles, 39/3000 fuzz. Builder's fixture dodges (unaffordable
  wall).
- **C7-3 (MAJOR, premises — the deepest one): the fact bound is gated on ARMED but
  the RISK rides to settlement.** Verdict disarms (normal path) → during the hours-
  long draining window the ordinary book re-enters the fact under the $16 rail →
  wire PLACES the $135.90 stack end-to-end. The gate belongs on DRAINED (rows
  riding), not armed.
- MINORS: silence-disarm destroys the probe → the silence-unlatch is unreachable in
  the exact no-fill case it describes; draining is memory-only across restart (= A7-3;
  **FLAG FOR RYAN**: note 55 says "probe fires on boot" — resolution proposed: fires
  on FIRST boot; a landed verdict is a world fact that must survive restarts within
  the program — builder to derive, Ryan can overrule). NITs: release needs a slotted
  pass; stale prose; None-basis TypeError (unreachable); C6-5 residual 1/3000.
- Held: R7-2a both directions + fills-convert + 0/400 interleave; deploy-night no-
  deadlock (probe re-routes to treasury when gas is capped, 30 cycles clean); RATES
  one-fact adjudicated correct ($150 five-fact stack refused); all five tenors live-
  verified probe-eligible; battery 96/96 twice with zero anchor-skips; every prior
  fixture inverts; safety/rails/absence/oracle/halt-during-draining all clean;
  release exactly-once with nothing caching the stale rail.
- Fact-identity adjudication: shape AGREE; "fail-closed" is TRUE ONLY INSIDE MAP
  COVERAGE and coverage is the fail-open door (guessed aliases, one a 404, real
  siblings missing, future listings degrade silently to per-series). The fix is the
  round's own medicine: live-board-verified members, real-sibling fixtures, prefix-
  semantics killer.
**Round 7 score: 3 THEORY approves; premises+impl 0/6 — but the defect surface is now
entirely the SYMBOL/LIFECYCLE layer; economics, enforcement machinery, safety, and
battery discipline all held on all three lanes. Round 8 consolidates.**

## Round 8 — frozen 1585994, tri-lane re-review launched — 2026-07-31
All items on one commit: 1091 green armed AND disarmed; battery 103/103 in-tree (my
own re-run confirms 0 survivors); parity fuzz 0/3000. My spot-checks: real siblings
KXAAAGASW/M → FACT:GAS probe-eligible; foreign KXNATGASW correctly its own fact.
R8-1's ground truth: calib2.json's `ser` field settles every symbol question (435
settled KXAAAGASD, 83 KXAAAGASW, five tenors 52-61 each; the aliases NEVER settled a
market) — test_symbols.py refuses unresolvable members and requires PROBE_FAMILIES ==
FACT_MAP members structurally. R8-3(b) DERIVED: a landed verdict is a world fact
within the program — persisted as a `probe_state` ledger row, replays like
settlements; "fires on boot" = FIRST boot. R8-5 DERIVED: note 55's gate is a function
OF REWARD BATCHES — zero batches = the gate not yet answering, NOT a third verdict;
silence pages and changes nothing; the unlatch problem dissolves; stated cost: a dead
family keeps d×C staked in ~0-hazard wings until a human acts ("nothing else deploys"
is Ryan's sentence to invoke). 5th and 6th unkillable clauses removed. **BUILDER
CONFESSION, WORSENING: three more scripted edits silently no-op'd this round — one
left the R8-3 bound INERT until a test caught it; a majority of cross-round survivors
trace to unverified string edits, a tooling failure unfixed since Round 5.** All
three lanes explicitly tasked with an EDIT-LIVENESS AUDIT (walk the diff hunk-by-hunk,
verify each claim present AND live) plus the new-listing hole (KXAAAGASM is live but
likely absent from calib2's historical ser field — which way does the symbol test
fail?), the lifecycle state machine × restart grid, and the routing grid fuzz.

## Round 8 re-review, Lane A record — 2026-07-31 (frozen 1585994)
Verdicts: THEORY **APPROVE** · PREMISES **APPROVE** · IMPL **REJECT** — first
two-axis approve from any lane since Round 5; ONE MAJOR stands.
- **A8-1 (MAJOR, orchestrator-confirmed at source): the silence page STORMS at the
  cycle rate** — R.ntfy unlatched (ntfy_once exists one file over), message embeds
  elapsed hours so it could never latch; 120 pages in 120 reads = ~1,400-5,000/day on
  the topic that must carry probe_verdict. The EXACT class this round fixed for the
  family page, and R8-5's entire residual risk (d×C until a human acts) is carried by
  this page being effective — by the tree's own doctrine a cycle-rate page equals no
  page. Fix: condition-keyed latch + derived re-page cadence; pin CADENCE not just
  existence.
- MINORS: prefix identity fails OPEN for a future colliding series (hypothetical
  KXUSTEEL shares "KXUST" prefix → probe-eligible+rail-exempt; bounded by lane cap/
  seat/wing; pin foreign-collision EXCLUSION not just sibling inclusion); corrupt
  probe_state row missing `armed` re-arms (safer default: disarmed+page; truncated-
  row fallback is correct). NIT: operator-only end for a never-answering probe with
  filled walls → probe=None makes riding collateral count ordinary (conservative).
- Verified LIVE end-to-end (its own execution): R8-3 riding-rows bound (refused at
  $120 riding, admitted at partial $10, admitted drained; plan agrees all three);
  REAL Maker restarts on shared data dir (verdict row idempotent, restart disarmed,
  re-release without staking, fresh dir = armed — "first boot" = fresh ledger, retune
  on deposit does NOT re-arm — correct); R8-4 12-cell routing grid agrees every cell;
  R8-1 calib2 gate LIVE (dead alias → 3 failures); battery 103/103 with 0 anchor-
  skips (the drift class ABSENT this round).
- **Calib2-as-authority ADJUDICATED RIGHT** (strongest offline statement of the wire;
  the one residual: a NEW same-underlying sibling under a DIFFERENT series name
  silently degrades the fact bound to per-series — no offline test can close it —
  goes on the deploy checklist explicitly as accepted bounded risk). **Silence
  re-derivation ADJUDICATED CORRECT** (zero batches = no reading; dead-link vs
  dead-thesis need opposite responses; the prior auto-disarm provably deadlocked;
  cost inside note 55's acceptance) — the design is sound, its one mitigation
  shipped defective (A8-1).

## Round 8 re-review, Lane B record — 2026-07-31 (frozen 1585994)
Verdicts: THEORY **APPROVE** · PREMISES **APPROVE** · IMPL **REJECT** — same shape as
Lane A, and the SAME single MAJOR (convergent).
- **B8-1 (MAJOR = A8-1): probe_silent pages at the CYCLE RATE** — executed through the
  real engine: 10 urgent pages in 10 cycles; at CYCLE_HZ=1.0 that is ~3,600/hour to
  the SHARED topic carrying the watchdog and bug alarm, indefinitely — burying the
  genuine alarms and training the operator to filter the one page whose job is to
  summon the human who owns "STOP, thesis rework." Message body varies per pass so
  naive ntfy_once won't latch — needs a stable-key latch/throttle. The n1 class fixed
  sixty lines above in this same round.
- **EDIT-LIVENESS AUDIT: CLEAN.** Full e7d74c2→1585994 diff (1,298 lines) walked hunk
  by hunk; every R8 edit present AND live by execution (incl. the R8-3a bound the
  confession said was momentarily inert — live at both readers; plan plans $0.00 into
  a riding fact post-disarm through the real engine). Battery anchors pre-scanned:
  zero skips possible.
- **KXAAAGASM new-listing question RESOLVED: no contradiction** — the lists carry
  PREFIXES; the test requires each prefix to resolve to ≥1 settled series (D/W
  satisfy); the monthly is eligible+fact-mapped through the prefix today; a genuinely
  new PREFIX still requires evidence. Correct direction both ways.
- MINORS: README operator contract stale at TWO of this round's own fix sites (item 9
  still says silence stamps a verdict/"treat as FAIL"; item 10 says disarm lifts the
  fact bound — drain does); R7 prose NITs claimed swept but alive (README:399-400 +
  config.py:1631 still say cluster_of); phantom test citation (test_clusters.TestThe
  FactMapIsBuiltFromREALSymbols → actually test_symbols); NIT: probe_state rows not
  fsync'd — a TORN disarm row replays ARMED (fail-open on power-loss only, bounded by
  lane seeding ≤ d×C).
- Premises verified to the digit (calib2 ser counts 435/83/61/56/53/52/55; symbol test
  enforces both directions by executed mutation; replay contract clean; note 55's own
  lines support R8-5; the never-answering probe distorts nothing but the page channel).
**Round 8 score with two lanes in: 4 approves, 2 impl rejects on ONE convergent MAJOR
with the fix pattern already in the tree. Lane C out.**

## Round 8 re-review, Lane C record — 2026-07-31 (frozen 1585994)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**.
- **C8-1 (MAJOR, deploy-blocking): the R8 prefixes OVER-MATCH the live rewarded
  board.** Live incentive_programs API (40k in-program markets, full pagination):
  KXAAAGAS also matches KXAAAGASMAX/MIN state-yearly-extreme families (60 rewarded
  markets — different observation, different window); KXUST also matches
  KXUSTESTSMATH (US Test scores). Executed: $117.13 of the $120 staked in FOREIGN
  underlyings; one Texas-yearly cluster placed $120 vs the $16 rail; the treasury
  verdict produced by a test-scores market. These are thin yearly longshot books —
  wall-shaped — exactly what the probe preferentially ranks: the LIKELY deploy book,
  not a corner. test_symbols asserts each prefix matches ≥1 evidenced series, never
  ONLY the family; calib2 (historical) can't see the new foreign listings. Six rounds
  of guessed-enumeration → one round of over-matching prefixes: the lesson is EXACT
  series enumeration, evidence-validated, with foreign-collision EXCLUSION pinned on
  the real stranger symbols.
- **C8-2 (MAJOR): R8-3(b) persisted the VERDICT but not the ACCUMULATOR.** batches/
  first_seen/baseline/realized rebuilt empty every restart. Executed with controls:
  watchdog-every-3h × dead family → 0 silence pages in 24h (control: 36); healthy
  wall restarted every 45min → 12 real batches, gate counts {}, verdict never lands.
  Both halves of R8-5 fail under restart cadence < batch cadence: gate can't answer
  AND can't page — $120 riding, human never told. Same orphan class as F-A1/
  retirement/disarm, one field over.
- **C8-3: three R8 fix-site survivors** — N3 riding-rows SEMANTICS (lane_usd>0 vs
  bool(rows) undistinguished), N12 the REPEATS property of the silence page (R8-5's
  whole safety argument, unpinned — compounds C8-2 and converges with A8-1/B8-1),
  N21 a SEVENTH inert duplicate clause (rail_exempt's armed test — routes() ⊆ armed).
- MINORS: C8-4 draining place-side rail hole (ordinary wing rows in probe families
  invisible to cluster_admits — up to $105-120 admissible at place vs $16 rail; FAILS
  CLOSED in practice, plan strictly tighter — engine-driven control equal); C8-5
  probe_state idempotence process-local (40 identical rows on 40 restarts). NITs:
  restore-before-halt-refusal ordering; per-process probe_released re-log; 1/3000
  C6-5 residual.
- HELD (all executed): routing grid 27/27; fuzz 0/3000 (its first 56 hits were its
  OWN harness modeling a rowless draining state production can't reach — honest
  correction per the standing rule); riding-rows bound at every partial drain k=0..6;
  lifecycle × restart incl. no-restake and no stale-row brick; safety s1-s4 with
  probe_state × halt interaction clean; finding-fixture inversions across all rounds.
**Round 8 score: 3 THEORY, 2 PREMISES, 0 IMPL. The state machine is sound by attack;
the blindness is the two INPUTS (which symbols, how many batches). Round 9 dispatched.**

## Round 9 — frozen f6a9f37, FINAL tri-lane review launched — 2026-07-31
All items on one commit: 1108 green armed AND disarmed; battery 115/115 in-tree;
parity fuzz 0/3000. My spot-checks: strangers excluded (KXAAAGASMAXTX, KXUSTESTSMATH →
not eligible, own facts), siblings included (KXAAAGASM → FACT:GAS), fail-closed new
listing (KXAAAGASQ → own fact); LIVE_VERIFIED = {KXAAAGASM: (2026-07-31, live API
200, no settles yet)}. R9-1 = EXACT enumeration, eight names, equality membership
(prefix provably cannot work: KXAAAGASMAX startswith KXAAAGASM); the invented KXUST-
ticker flushed from ~40 fixtures. **Evidence exception DERIVED on the asymmetry:
omitting a real sibling from the fact map = UNBOUNDED (the R8-1 hole); including an
unsettled one = BOUNDED (wing edge, cliff, bleed screen, d×C) — so the exception goes
to inclusion, once, flagged with date+method, both test-enforced.** R9-2 accumulator
replays like the verdict (probe_accrual rows re-consumed at boot; Lane C's two
restart controls are fixtures). R9-3 silence latch on condition + re-page on the
bound; N12 pinned BOTH sides (600 cycles → 1 page; 3 across 19h). n5 DOCUMENTED not
changed — builder's counter-argument stands for adjudication: riding walls ARE probe
collateral (bought against the probe's budget); the fact bound at d×C is their
instrument; aligning the filter would re-introduce R6-3. NEW STRUCTURAL ENFORCEMENT:
has-a-caller AST tests for lifecycle hooks + test_symbols for all symbol premises —
the two defect classes of the whole build ("I asserted a premise instead of verifying
one") now enforced by machinery, not memory. Builder's own honest record: NINE ROUNDS
— the machine survived attack once written; the inputs (guessed symbols, caller-less
hooks) were the defect source. Final tri-lane launched on f6a9f37 — if all nine
verdicts are APPROVE, v6 is deploy-ready and waits on Ryan's $600.

## Round 9 FINAL review, Lane B record — 2026-07-31 (frozen f6a9f37)
Verdicts: THEORY **APPROVE** · PREMISES **APPROVE** · IMPL **APPROVE** — the first
full-lane approval of the build. No FATAL/MAJOR. Findings all prose: MINOR — deploy
checklist item 2 (README:399-401) still describes prefix/cluster_of semantics R9-1
abolished (zero R9 hunks in that file — the "swept for real this time" claim is false
at that half; config.py half WAS swept); NITs — item 9 understates page cadence
(once/6h shipped, "once per window" written), item 11 prefix vocabulary, n5 rationale
lives in commit message not at site. Correction demanded in next commit; "no
re-review implied by anything I measured."
Approve evidence (its own execution): suite 1108/1108 both modes; battery 0/115 +
static proof all anchors exist; full diff hunk-by-hunk — every CODE claim live; 7 own
symbol mutations all killed; live wire re-verified independently (KXAAAGASM 200,
KXNATGAS 404, KXAAAGASMAXTX 200); restart chains to the cent vs controls (dead family
3h×8 pages 4=4; rollover-during-gap banks $0.75, earned == hand-truth at 1e-9;
healthy-restarted PASSes 1.0; dead REPORTs 0.0089 — no false-PASS door); torn row →
prior row; armless row → DRAINING+page. RECORD CORRECTION (Lane B's, verified
equivalent by execution): R9-2's mechanism is the accumulator persisted INSIDE the
fsync'd probe_state row, not probe_accrual replay.
**Score: 3 of 9. Lanes A and C out.**

## Round 9 FINAL review, Lane C record — 2026-07-31 (frozen f6a9f37)
Verdicts: THEORY **APPROVE** · PREMISES **REJECT** · IMPL **REJECT**.
- **C9-1 (MAJOR, orchestrator-confirmed at source): the verdict gate has a DEAD ZONE
  between one batch and two.** Silence mutes forever on sum(batches)>0 (any batch
  EVER, incl. settled windows); the verdict needs max(per-ticker)≥2; daily windows
  RE-KEY tickers (KXAAAGASD-26AUG01→02→03) so per-ticker can never reach 2 — a stable
  orbit: no verdict, no page, indefinitely, armed, restaking d×C — and R9-2's replay
  makes the stall RESTART-DURABLE (the persisted batch is what mutes the page after
  every reboot; engine-real 48h × 3h-watchdog: 0 pages, no verdict). This is the
  WEAK-SHARE failure — share flowed once, weakly, died — the center mass of what the
  probe exists to detect, and the instrument is blind there. Falsifies R8-5's
  residual-risk argument over its most probable case. Fix shape: silence keys on time
  since LAST batch; verdict counts batch EVENTS (observe passes where any watched
  ticker increased) so scattered/per-window evidence accumulates; pin the semantics.
- **C9-2 (MAJOR, battery rule): silent_paged_at persistence unpinned BOTH directions**
  (state_row-writes-None and restore-discards each pass all 1108; the round-trip
  fixture enumerates eight fields, omits this one). Shipped behavior correct; mutated
  copy: 25 pages vs 4 under 45-min restarts. **C9-3 (MAJOR, battery rule, false-PASS
  direction): _maybe_verdict max→sum passes all 1108** — one batch on each of two
  tickers verdicts PASS from half the specced evidence on the mutant.
- NITs: 1/3000 C6-5 residual (previously adjudicated); KXGAS in two non-probe v5-law
  fixtures (nothing masked); LIVE_VERIFIED enforcement is test-only BY DESIGN (the
  fabricated-evidence residual is offline-unclosable, correctly on the checklist);
  hand-poisoned row unreachable by engine writes.
- HELD (all executed): R9-2 replay under all EIGHT poison sequences (duplication,
  reordering, stale windows/tickers, crash orderings — conservative every time);
  R9-1 exact on every frontier one char at a time; R9-3 cadence shipped-correct;
  safety s1-s4; routing 27/27; partial drains k=0..6; release exactly-once;
  no-restake; deploy-night stack ≤ d×C 0/400; fixture inversions across all nine
  rounds; battery 115/115 zero anchor-skips. New mutants 16: 13 killed, 3 survived
  (the two C9-2 + C9-3).
- **Its closing words: "nine rounds of attack have left nothing standing in the money
  paths... What remains defective is exactly one seam, three ways: the batch-counting
  semantics of the verdict gate... a small, well-localized fix — one condition, one
  counter, three fixtures — but I cannot call a machine deploy-ready when its
  go/no-go instrument can silently say nothing forever while $120 rides."**
**Score: 3 (Lane B full) + 1 (C theory) = 4 of 9 so far. Lane A out. Round 10 = the
gate seam + Lane B's prose corrections.**

## Round 9 FINAL review, Lane A record + ROUND 9 TALLY — 2026-07-31 (frozen f6a9f37)
Lane A verdicts: THEORY **APPROVE** · PREMISES **APPROVE** · IMPL **APPROVE**.
- **Evidence exception ADJUDICATED SOUND with the load-bearing premise executed:**
  there is NO per-series calibration anywhere in the machine (g is side+price-band
  population-wide), so an unsettled series is not a new risk class; at wing prices the
  monthly draws the table's MOST conservative rows (1c NO g=1.0000). All four bounds
  fired under execution; test_symbols forces the entry out of the exception when
  settles arrive (fail-loud). Note for the record: monthly walls extend DRAINING up
  to a month — conservative.
- **n5 ADJUDICATED BUILDER-RIGHT in every constructible state**: plan strictly
  tighter everywhere; worst-case cross-pass conversion $110.05 never crossed d×C;
  the counter-factual (aligning the filter) mechanically reproduces R6-3.
- R9-2 restart durability 4/4 through the real Maker+ledger (pages 4×/24h under 3h
  restarts; PASS lands under 45-min restarts with interleaved ledger noise; batch
  counter exact across restart; double-replay idempotent). Spine: dials to the digit,
  g row-for-row, F1/rescue/sunk, greedy oracle 0/400 (its own 39 hits were its own
  harness — honest correction), fact bound both directions, R9-3 both sides, N3/N21.
- Findings, none blocking: MINOR the SAME stale README line as Lane B (third round
  surviving a "swept" claim); MINOR conservative: ordinary wing rows placed while
  draining keep drained() False → for the centerpiece book release is effectively
  unreachable and the d×C carve-out persists (capital OCCUPIED inside the carve-out,
  not idle; fails toward under-deploy; operator lever = PROBE_ARMED=False; extends
  B6-5 — for the builder to document or derive); NIT item-11 vocabulary.
**ROUND 9 TALLY: 7 of 9 — Lanes A and B FULL APPROVALS (six), Lane C theory approve
(seven) with premises+impl rejects on ONE localized seam: the verdict gate's batch-
counting semantics (dead zone C9-1 + two unpinned-seam survivors C9-2/C9-3). Round 10
= one condition, one counter, three fixtures, plus the prose sweep both approving
lanes demanded. Then the final tri-lane pass.**

## RYAN'S ORDERS, 2026-07-31 midday — THE DEPLOY PLAN CHANGES
1. **v5 STOPPED** on his order (clean shutdown 07-31 ~12:03 PM MT, handback written,
   43 positions riding to settlement — it never sells). The $1-earned day confirmed
   the dispersion diagnosis: $10 seats accrue under the $1 cliff and forfeit.
2. **The 120/480 probe is superseded by RYAN'S OWN DESIGN — RANK-TRUNCATION MODE:**
   run the machine with the $2,000 dials (rail $66.67, N=30, full law geometry) but
   fund only the actual ~$120-132 deposit. The law ranks the whole board exactly as
   the $2k machine would and funds down the marginal-rate order until the cash runs
   out — the top ~2 clusters get $66 seats, everything below the budget line doesn't
   exist. NO hardcoded family list, NO probe lane (PROBE_ARMED=False) — the emergent
   ranking IS the probe; "top earners are first in rank order, and with a truncated
   budget, first = only." Ryan EXPLICITLY ACCEPTS the concentrated swing: the 20%-day
   bound is knowingly waived — the unstaked $1,868 is the risk reduction. What it
   measures: the actual daily earnings of the $2k machine's best seats, for ~$132
   exposure. Two capital constants, explicitly split: C_DIALS=2000 (geometry) vs
   C_CASH=deposit (budget + cash guards); every capital consumer audited and assigned
   with a derivation; waivers documented where the day-stop would have applied;
   standard mode provably unchanged. Builder folding into the Round-10 commit; then
   ONE focused tri-lane pass on the new seam + the verdict-gate fixes; then deploy-
   ready on Ryan's funding (~$120-132).
   Context from the exchange with Ryan: "$66/cluster" is not a fixed limit — the rail
   is C/N ($66.67 only at $2k; $16 at $600; ~$4 at $120), which is why concentration
   at small capital requires either an exemption (the old probe) or notional dials
   (this mode). He never ordered the hardcoded family list — that was our expression
   of the exemption boundary; this mode removes the need for it.

## LIVE INCIDENT + STAND-DOWN — 2026-07-31 ~12:45 PM MT
Rank-truncation v6 (8cee2ad + VPS-armed config, $132) ran ~15 min live and CHURNED —
Ryan watched orders placing/rescinding continuously (his pre-launch fear, verbatim).
STOPPED on his order; **v6 STAYS DOWN until Ryan says otherwise.** Anatomy: (1) at a
fully-spent budget the rank boundary is razor-thin — world jitter reshuffles the funded
set every pass (SME q 13↔14, spent 55↔70 oscillating); (2) cancel-first renders every
move as disappear/reappear; (3) churn drained the ratelimit token bucket → 51
reserve_floor refusals → place-after-cancel refused → dark slots → re-place next pass
→ more churn. What the 15 min also showed, all law-working-as-built: the EV-commodity
entries were CLIFF RESCUES of v5's morning accrual (pennies to bank ~$1 each); WMG
(72c accrued) untouchable — position_divergence (v5's −4 riding) and the safety law
won't quote a diverging rung, so that 72c forfeits; Clayton's 40c rung refused
entry_net_negative WITH RECEIPTS (T=3.2 turnovers/day, bleed 2.03 vs credit 1.08, net
−0.95 — the 40c was gross); Panama $39 = the deepen gate concentrating into the ONE
rung with 400h of proven no-fill tape. RYAN'S TWO STANDING QUESTIONS FROM THE RUN:
(a) same brain as v5 → same board with bigger seats — concentration is history-gated
so day-one money flows to v5's old rungs only; (b) THE ESTIMATOR IS UNVALIDATED and
yesterday's tape contradicts it ($19.7 projected gross → ~$2 payable; EV cluster 2c in
12h vs ~$1/window estimated). Zero settlements have flowed through the estimate→paid
proof gate. Prime suspect: competition depth S mismeasurement. THIS is the open
question of the program.
FIX IN FLIGHT (builder, no deploy pressure): THE STICKY BOOK — placed money never
moves on rank/size improvements; only hard invalidations (settle/close, fill, off-best
beyond dead-band persistent N reads, hard viability failure); plan seeds from the
resting book and only ADDS; pinned by "unchanged/jittered world ⇒ ZERO cancels over
100 cycles." Redeploy gate: replay against the RECORDED live tape (~60 moves/min) must
show ~0. Builder dropped once on an API error mid-build and was resumed.

## RYAN'S RULING on validation — 2026-07-31 ~1 PM MT
"We don't need to wait for settlement. Estimates are accurate, if anything a little
low. We have access to every estimate — use them. The rate for a resting order at a
given contract number is very stable — measure and change if needed." → THE
MEASURED-RATE LOOP, folded into the sticky-book freeze: every funded seat's realized
accrual rate measured off the estimates feed (probe machinery generalized to all
seats); measured beats modeled after 2 batches; a seat is DEAD iff accrued +
measured_rate × h_left < the $1 cliff sustained past grace → exactly ONE recall,
refund to next rung (this is the sticky book's only measured mover); per-seat
measured/predicted calibration row logged every batch. Redeploy gate: tape replay
~0 requotes + measurement sane on the recorded estimates. Ryan: "it was close to
correct" — back up as soon as the gate passes.

## SPEC VIOLATION CAUGHT BY RYAN — 2026-07-31 ~1:30 PM MT
The code kept "one MARKET per cluster" (marginal.py §2, relaxed only for quiet
ladders). THE NOTE SAYS THE OPPOSITE, in the cluster-cap derivation above: the $66
rail "naturally holds ~2 knee markets," and $66 was chosen OVER $33 precisely so the
second knee funds in the double-fast clusters. The rule was dropped in the v6 spec;
the cluster DOLLAR cap is the correlation bound. Live evidence: 35 cluster_taken
refusals in the 15-min run — the top-heavy clusters' second markets sent down-rank.
Survived NINE review rounds because everyone (builder, three lanes × 3 rounds, and
me) verified the code's §2 comment as self-consistent instead of diffing it against
the note's derivation section — and I then explained the code's behavior to Ryan AS
his law. Fix in the current freeze: gate deleted, dollar-rail-only intra-cluster,
knee saturation emergent, quiet-ladder license subsumed, "~2 knee markets" pinned as
a fixture citing this note. NEW REVIEW RULE: any code comment citing a law gets
diffed against the note text it cites.

## THE MANUAL RANKING + SEED MODE — 2026-07-31 ~2 PM MT (Ryan's order)
Ryan ordered a MANUAL rank off live Kalshi data (not our logs). Findings (live API,
this hour): **gas daily = richest per-market pools on the entire board by ~10×**
($100/rung over ~16h ≈ $6.26/h × 17 rungs, 11.9h left) — and ALL 17 books EMPTY both
sides, zero competition. Today's settles (4.115+ all NO) locate AAA. **Treasuries have
NO live reward window right now** (the "8 live UST" was a prefix false-match — KXPLTR-
AUGCUST contains "UST"). Next tier: hourly index HUDs ~$57/h but 0.9h left; RAIN
$2.34/h; META/YTVIEWSHIGH/MCD ~$1-1.6/h. The clusters v6 funded: $0.06-0.6/h — 10-100×
poorer. Ryan's 4×$30 guess priced manually: $30@3c = 1000 contracts = target_size →
~100% share of a half-pool = ~$37/seat over 12h, ~$150 estimate on $120 staked, vs the
funded book's measured 2c/12h. Caveats stated: empty book = no ref (may score zero) —
v3/v4 solved by quoting BOTH SIDES (manufactures the ref — THE answer to why two-sided
earned); pick-off bounded at wing collateral; July tape says gas wall fills rare.
**THE DEFECT THIS EXPOSED: the machine cannot INITIATE a market** — empty book →
"unpriced" → scanner skip → the board's richest pools structurally invisible. v3/v4's
$70 days were initiation. **SEED MODE ordered into the freeze:** live pool + empty
book → seed candidate; quote both sides wing-priced (≤~3c), each side sized to clear
target_size; estimate carries the S=0 discount; the measured-rate loop governs within
~30 min (dead-recall kills duds in one move); existing rails bound everything ($66
cluster rail ≈ 2 seeded rungs — the note's own "~2 knee markets"); narrow license,
non-reward unpriced still skipped. Freeze = seed + sticky book + measured-rate +
multi-market-per-cluster + truncation.

## RYAN'S STANDING CORRECTION — 2026-07-31 afternoon
v3 CHOSE RUNGS, AND CHOSE THEM BETTER. Do not characterize it as ranking-less. Its
realized book = an empirical rank table (rank by NET per market-side: rewards − fill
losses, from the v3/v4 ledgers + July payments). NOT being implemented right now on
his order — the current freeze proceeds as designed (measurement-first ranking, seed
mode, W1/W2/W3) — but the v3-net-snapshot boot prior is a READY OPTION he can invoke
with a word; it drops into the same measurement store W2 pre-seeds.

## STAND-DOWN, FINAL FOR THE DAY — 2026-07-31 ~5:15 PM MT (Ryan: "same exact shit. pull it down")
Three deploys in one afternoon, three pull-downs. v6 STOPPED AND DISABLED (cannot
return on reboot). Orders cancelled at shutdown; small positions ride. Where it ended:
sticky book PROVEN live (churn 0 across all checks); money arithmetic PROVEN live
(exposure_ok true, reconciled row); estimator guard WORKING (29 honest pages);
the REAL estimator bug found and fixed (qualification zero → share=1.0 on thin
unqualified ladders — the EV lie); seed mode shipped but NEVER CONFIRMED LIVE — two
serial blockers found by contact (divergence gate, then the classifier-table catch-22,
fixed), and the machine was pulled before the third check could confirm gas candidates
flow. My errors on the record: bare restart bypassing the arming script (one wasted
cycle); the DF theory accepted before the builder falsified it itself.
STATE OF PLAY for the next session: latest commit on v6-build has everything incl.
the seed-source program-iteration fix (untested live). The open question is ONLY
whether seeds now emit on the wire. Ryan's standing alternatives: (a) one more fire
check (~4 min) to confirm/deny seeds; (b) the v4-style chooser (first-dollar rate at
land-grab prices — lip_maker_v4.py:1292, read and understood) as the ranking, with
v6's rails; (c) v4 itself still exists as a service. NOTHING RUNS until Ryan says.

## STOPPED, 2026-07-31 ~5:45 PM MT — Ryan: "zero orders. stop v6."
Fourth deploy of the day (v4 rung chooser + seeds + truncation armed) placed ZERO
orders per Ryan's UI; stopped and disabled on his order before diagnosis. Watcher
agent killed. UNDIAGNOSED: why zero orders under V4_RANK (candidates exist, rank
should top gas — the block is somewhere between rank and place; the last cycle rows
were not pulled). v6 down, disabled, nothing runs until Ryan says.

## FINAL STOP — 2026-07-31 11:33 AM MDT (Ryan: "stop v6. now")
Fifth and final deploy of the morning (v4 chooser with the CORRECTED full-wall
denominator + seeds + truncation) was stopped ~2 minutes after boot, mid-startup —
before its first plan pass could place anything. The corrected chooser and the seed
path remain UNTESTED on the wire. v6: inactive, disabled, cannot restart on reboot.
Positions ride (never sells). Everything is committed on v6-build; the deploy script
(rev 3) arms truncation+seeds+V4RANK. Sequence of true timestamps for the day's five
deploys: all between ~10:20 AM and 11:33 AM MDT.
For the next session: the ONLY unanswered question is what book the corrected chooser
builds — it needs ~4 undisturbed minutes from a cold boot (startup recon sweep ≈2 min
on this account's position count) before the first orders can exist.

## THE ROOT CAUSE OF THE DAY + CLEAN-SLATE DEPLOY — 2026-07-31 ~12:00 PM MDT
The day's every-deploy-buys-EV mystery, fully closed by dead-log forensics + offline
reproduction: (1) gas was excluded from candidacy by THREE stacked gates — position-
divergence, unpriced-skip, and the seed close-unknown gate that refused 3,345/3,345
candidates board-wide (a defensive check demanding a classifier field that BY
DEFINITION doesn't exist for unclassified markets; fixed: falls back to the program's
own window end; offline proof: 6,356 candidates emit incl. all 17 gas pairs at
est_rate 0.78 ≈ 100× the field); (2) even with seeds emitting live, THE LEDGER
CARRIED THE DEAD BOOK ACROSS DEPLOYS — stop cancels wire orders, but the committed
book replays at boot, the sticky floor adopts it, the requoter RE-PLACES it (fresh
timestamps, same EV/SME book), and the budget is eaten before the ranking spends a
dollar (gas pair $60 vs budget_left $29 → cant_afford_entry). Deploy script rev 4:
ARCHIVE THE LEDGER AT DEPLOY (clean slate; positions ride). Also fixed en route: the
v4-rate denominator (marginal entry block → FULL wall cost — Ryan caught the
inversion from his own order snapshot). Manual live math on record: gas $100/rung
16h ρ=6.26/h vs EV $40/rung ρ=1.48/h — gas 13-90× better per dollar; EV was only
ever "best of the poisoned remainder." Clean-slate deploy live ~12:00 PM MDT;
expectation: 2 gas pairs (~4 orders × ~$30) + remainder down-rank; ~$16/pair over
the remaining ~10h window if the walls accrue as the tape says.
