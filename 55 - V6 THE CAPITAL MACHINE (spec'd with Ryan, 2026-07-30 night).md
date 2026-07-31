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
