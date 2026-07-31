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
