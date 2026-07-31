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

## ERRATA to §4 / RUIN-p (2026-07-31 night MT — logged 08-01 UTC; validated by convergent evidence)
Both v6 builders, working blind, independently found §4's p-definition self-contradictory:
the literal always-filled/board-price reading gives p ≈ 0.87–0.97 ≫ d=0.20 ⇒ N→∞ ⇒ v6
funds NOTHING. Both independently shipped the same resolution, and the adjudicator
reproduced the arithmetic: **the measured 8–10% cluster-days prior owns the LEVEL of p;
the funded mix enters as a RATIO of calibration-degraded settle-against probabilities**
(preserving floor↓⇒p↑⇒N↑⇒A↓ one-directionally and reproducing this note's own N=25–36
band and A=C/30 values). The implied fill factor is logged on every derivation so the
cluster-days tape can falsify the anchoring. §4's always-filled framing survives only as
a diagnostic instrument (RUIN_ALWAYS_FILLED pages "don't play").

## V6 ADJUDICATION (2026-07-31 night MT — logged 08-01 UTC): Opus lane wins as base + 2 blocking grafts
Winner v6-build (850 tests, exact cancel-all spine, disarmed-mode = bit-for-bit green v5
fallback, floor-dial correct: band → min tick when armed, bleed/net screens do the
refusing — the Fable lane's kept 6¢ band refused the 1¢ treasury wall outright, deploy
plan inexecutable there). Grafts from Fable: N = max(30, formula) (diversification floor,
"run 30"); deepening carries the oversize evidence clauses (proven: 0.5 contract-hour
rung deepened to the $19.95 rail — the quiet-afternoon incident through a new door).
Minor: probe seat-exemption fix; PROBE_FAMILIES = KXAAAGASD + boot pages on zero-slot
probe family. Final battery after grafts → APPROVED FINAL → merge to v6, deploy on deposit.

## V6 APPROVED FINAL — 1af221a (2026-07-31 night MT — logged 08-01 UTC), merged to branch v6
> ⚠ "DEPLOY-READY" was PREMATURE — Ryan's mandated triple review (next section) found real
> MAJORs for nine more rounds. Kept for the convergence evidence and the milestone.
Final battery clean (7 mutations re-run by the adjudicator, 872 green armed AND disarmed).
THE STRONGEST SIGNAL THIS PROCESS PRODUCES: after grafts, the two blind-built lanes yield
the IDENTICAL funded book on the shared board (wall $10.01 / mid $2.85 / rich $20.00 /
rescue $1.92 / thin $1.20). Note's printed dial pairs reproduce exactly. Disarmed mode =
green v5 fallback. Deploy = deposit lands → set C → boot (README deploy checklist: verify
PROBE_FAMILIES against the live board — armed-probe-zero-slots pages). The 120/480 probe
fires on boot; estimates feed adjudicates the wall thesis within ~2 reward batches.
v5 soaks meanwhile on lip-v5-build; v6-f (Fable lane) frozen unmerged, its two winning
ideas live in G1/G2.

## THE NINE-ROUND TRI-LANE REVIEW — COMPACTED RECORD (2026-07-31)
> Full round-by-round logs (Rounds 1-9, every lane record) live in this note's git history
> (pre-compaction commit). This section keeps only what remains load-bearing.

Ryan mandated triple review after the "APPROVED FINAL" above — and it was right to: nine
rounds of three blind Fable lanes (theory / premises-vs-data / red team), defend protocol
(no claimed defect accepted without executed reproduction), every round on a frozen
commit: e2061ff → 02b6ed8 → 36de3dd → 27544f6 → 50303e0 → e7d74c2 → 1585994 → f6a9f37.
Tests 924 → 1108; mutation battery grew to 115, in-tree.

**What held under nine rounds of attack, on all lanes:** the safety law (s1-s4), every
rail, the economics — dials, g-tables, ruin math reproduced independently to the digit by
every lane — and the enforcement machinery. **Where every defect lived: the INPUTS and the
SEAMS.** Guessed symbols (invented gas/treasury aliases — one a live 404; prefixes
over-matching foreign markets like KXUSTESTSMATH); caller-less lifecycle hooks (probe
verdict never wired, disarm never called — the retirement-bug class, three times over);
plan/place parity seams (B18 variance, cluster ledgers, draining routing); verdict-gate
accounting (false-PASS and false-REPORT through restarts and window rollovers, found
blind-convergently by all three lanes); unpinned clauses inside fix sites. Builder's
honest close: "the machine survived attack once written; the inputs were the defect
source."

**Round 9 tally: 7 of 9 approves** (Lanes A+B full approvals; Lane C rejected on ONE
localized seam — the verdict gate's batch-counting dead zone, C9-1/2/3: daily windows
re-key tickers so per-ticker batches can never reach 2 → no verdict, no page,
indefinitely, restart-durable, $120 riding). Round 10 (that seam + the prose sweep) was
folded into the rank-truncation rebuild and overtaken by deploy day (below).

**PROCESS RULES LEARNED (permanent; enforced by machinery, not memory):**
- Reviews run on FROZEN commits only — never a tree mid-fix-round.
- The mutation battery ships IN THE TREE, mutates a COPY, and asserts a green baseline
  first (the in-place harness FAILED GREEN for two rounds — every mutant looked killed).
- Each guard has exactly ONE reachable expression; a defensive duplicate is a clause no
  mutation can kill (seven inert duplicates found across the rounds).
- Before deleting a guard as "unreachable," write the test that pins the PROPERTY it
  expressed; a refactor that widens a domain re-runs the invariants of everything reading
  that domain (two Round-4 defects were RESURRECTIONS caused by earlier fixes).
- Scripted edits assert their anchor exists (`assert old in s`) — silent no-op edits
  caused a majority of the cross-round survivors.
- SYMBOLS ARE PREMISES: exact enumeration, evidence-validated against calib2's `ser`
  field (the strongest offline statement of the wire), with foreign-collision EXCLUSION
  pinned on real stranger symbols; prefix matching provably cannot work
  (KXAAAGASMAX startswith KXAAAGASM). The evidence exception goes to INCLUSION only
  (bounded), dated and test-enforced; omission is the unbounded direction.
- Lifecycle hooks get has-a-caller AST tests — "I asserted a premise instead of
  verifying one" was the defect class of the whole build.
- Any code comment citing a law gets diffed against the NOTE TEXT it cites (the
  one-market-per-cluster inversion below survived nine rounds because everyone verified
  the comment's self-consistency instead).
- Harness-vs-production parity is itself a review item (a divergent fuzz harness
  manufactured a false 0/3000 twice; both honest corrections are the standing rule).
- Live pages need a condition-keyed LATCH + a derived re-page cadence — a page firing at
  cycle rate on a healthy system equals no page. Stale prose is killed by a
  superseded-numbers BLACKLIST test, never token-presence tests.


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

## LIVE INCIDENT + STAND-DOWN — 2026-07-31 (stamp unreliable; see the five-deploy timeline correction below)
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

## STAND-DOWN — 2026-07-31 (~5:15 PM stamp was a UTC MISREAD ≈ 11:15 AM MDT; Ryan: "same exact shit. pull it down")
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

## STOPPED — 2026-07-31 (~5:45 PM stamp was a UTC MISREAD ≈ 11:45 AM MDT; Ryan: "zero orders. stop v6.")
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

## TRIPLE-FORENSICS VERDICT — 2026-07-31 ~12:20 PM MDT (three parallel agents, all rows quoted)
The "immortal book" is NEITHER inherited NOR resurrected: (1) boot recovers zero
(ledger 0 rows/0 orders/0 positions; adoption deleted, sync subtract-only, handback
empty); (2) ALL stops fully cancel (cash delta drains to exactly 0.0 — exchange-side
proof; zero cancel refusals all day — Ryan was right that it comes down properly);
(3) held:9/floored:8/$111 = pass-2 correctly counting the machine's OWN pass-1
placements. EVERY RUN FRESHLY CHOSButtonS THE SAME CRUMB BOOK — accrual (exchange-side,
$14.13, survives any wipe) makes those markets toll-free and cheap-to-finish, so they
win instantly; SME won on plain rate with $0.03 accrued.
THE ONE REMAINING SEAM: ~3,400 seed slots (gas first) injected EVERY pass; the queue's
refusal accounting covers ~148 candidates — **~3,200 seeds DIE SILENTLY between the
engine hook and queue admission.** Suspects: plan-path p6/classify re-check on fields
seeds don't carry; the SETTLE_LAG 8h haircut vs the $1 cliff; Curve-admission gates
(cliff_unreachable ×126 in an earlier run) untouched by the V4-rank edit. Builder
tasked: one offline script pushing a real gas seed slot through the real admission,
name the killer line, fix, and add CANDIDATE CONSERVATION to the provenance logging
(slots in = funded + named refusals + counted drops, tested) so silent death is
structurally impossible. Machine down throughout.

## RYAN'S ULTIMATUM + THE PERMANENT DEPLOY GATE — 2026-07-31 ~12:30 PM MDT
Seventh deploy bought SME fat legs BOTH SIDES + Panama 731@92c again despite the
rank-order fix and seed-discount parity — under V4 rank SME at 62c cannot
mathematically outrank gas, so a NON-heap placement lane or a wire-vs-theory
divergence exists; unproven. Ryan: one more deploy that doesn't match expectation
and he cancels. Machine STOPPED+DISABLED. **NEW PERMANENT RULE — DEPLOY GATE #0:
v6 never deploys blind again. A dry-run book printer (lip_v5.dryrun) runs the EXACT
production plan against the live board offline and prints the full would-buy book
(every order + rates + top refusals). Ryan approves the PRINTED book before anything
runs. CORRECTED PER RYAN: he is NOT an approval step — the burden is OURS. The
dryrun print is verified AGAINST EXPECTATION BY US before any deploy; Ryan only ever
sees a machine already doing the right thing. Builder building it now + must name
the line that placed SME.** Every future session: no v6 deploy until the dryrun
book has been printed and verified correct by the operator-side (us), offline, first.
