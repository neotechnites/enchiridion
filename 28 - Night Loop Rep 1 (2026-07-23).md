# Night Loop — Rep 1 · Closing Rep (2026-07-23)

**Role:** TOP MIND closing rep 1 (playbook note 20, steps 4-7). Consolidates the two overnight ideators — Lane A (Structure & Venue) and Lane B (Attention & Information) — applies the trap list (note 20 step 6) to every non-DEAD survivor, and ranks what stands.

- **Runner:** Opus/medium, capped rep. One decisive disk test run (I1 collinearity), zero pulls, zero sub-agents. Relaunch fold-in 2026-07-24: both ideators appended relaunch-continuation doors (A8, F1, F3) after the first close; trap-reviewed and folded in below, prior close preserved intact.
- **Source ledgers:** `implementation/night-loop-r1-A.md`, `implementation/night-loop-r1-B.md`. Harnesses: `kalshi_data/scripts/laneA_r1.py`, `laneB_r1.py`, `laneA_r1_round.py`, `laneB_r1_fresh.py`.

## Tally

- **18 numeric ideas across both lanes** (15 first-pass + 3 relaunch doors: A8, F1, F3). 12 DEAD-as-stated / DEAD-cited / subsumed, 3 CONDITIONAL going in, 3 CONDITIONAL-research/-collinear.
- **After trap review:** **1 PROMISING** (I1, upgraded — its named blocker was refuted on disk this rep), **1 CONDITIONAL** (A3, capped by an intrinsic stale-quote/fill trap), **2 CONDITIONAL-research** (A6, A8), **1 CONDITIONAL-collinear** (F3, confirmation-only). **0 TRADE, 0 cleared a holdout.**
- **Consolidation result:** the three relaunch doors added zero orthogonal edge. Weekend (F1), equity-hours (F3), and rv24 (I1) all proxy ONE factor — crypto wings are rich only in the quiet, low-realized-vol, TradFi-asleep regime. Slate keeps the single sharp rv24 gate, not stacked collinear calendar/clock filters.

## Consolidated ledger

| lane | id | idea | n | EV-net | verdict (this rep) | trap flag |
|---|---|---|---|--------|-------|-----------|
| B | **I1** | rv24 low-realized-vol regime gate on BTC wing-fade | 720 | **+2.30c** | **PROMISING** (up from CONDITIONAL) | correlated-sample: rv24 autocorrelates → effective n << 720 |
| A | **A3** | staleness-selection overlay on commodity-daily wings | 406 | **+4.57c** | **CONDITIONAL** (held) | stale-quote / fill: signal *is* an unfillable stale price |
| A | A6 | WTI daily↔hourly settlement identity (dutch) | — | — | CONDITIONAL-research | needs ticker/settle-time alignment pull |
| A | **A8** | round-number strike anchoring on commodity wings | 13 rnd | round +9.78 / off +1.48 | CONDITIONAL-research | correlated-sample + pooled: 11/13 = Brent $5-lines |
| B | **F3** | equity-hours cross-condition (sell when TradFi asleep) | 209 eq | eq −0.70 / non-eq +0.78 | CONDITIONAL-collinear | inverse-image of calm-clock; confirmation, not new EV |
| B | F1 | weekend attention gate | 800 wknd | wknd +1.61 / wk +0.06 | DEAD-subsumed | regime-fake (era-fragile) + ⊂ I1 rv24 (wknd-frac 0.70 in LOW-rv24) |
| A | A1 | thinnest wings richest | 285 | mid +3.56 / thick −0.53 | DEAD-as-stated | wrong direction (mid best) |
| A | A2 | new-listing early richness → sell early | 73 | drift +3.18 (richens late) | DEAD-as-stated | wrong direction |
| A | A4 | commodity point-settle favorite fade | 175 | ~breakeven | DEAD | 2.3pp overprice < round-trip fee |
| A | A5 | WTIH session clock | 104 | OFF +4.34 / US −3.14 | NOISE/underpowered | inverts unified-book; n⊂noise |
| A | A7 | richest-single-rung concentration | 285 | richest +0.71 / rest +3.00 | DEAD-as-stated | edge is in the whole band |
| B | I2 | cross-asset "X-while-A" momentum | 720/321 | calm +0.68 / hot +1.04 | DEAD | wrong direction, ⊂placebo |
| B | I3 | streak-magnitude reflexivity | 960/454 | flat / wrong-dir | DEAD | no separation |
| B | I4 | EIA report-day calendar-attention | 24/6 | report BETTER | DEAD | wrong direction, n⊂noise |
| B | I5 | directional per-tail streak-chase | 1044/473 | aligned −0.05 | DEAD | BTC wrong-dir, ETH ⊂placebo |
| B | I6 | Poly→Kalshi crypto info-lead | — | — | DEAD-cited | graveyard: basis-not-dutch |
| B | I7 | offshore-premium fade | — | −3.3 to −6.5c | DEAD-cited | Coinbase/BRTI is the better predictor |
| B | I8 | IV−RV wedge gate | — | — | DEAD-dominated | cousin of dead DVOL; realized side ⊂I1 |

## Survivors, ranked (small-bankroll first)

### 1. I1 — rv24 low-realized-vol regime gate · **PROMISING**

**Mechanism.** Hourly-wing buyers price off headline/implied vol and don't reprice on the *slow* trailing-24h realized-vol state. When realized vol has been quietly low, the standing 3-25c YES wing curve is stale-rich; buy-NO captures it. Distinct from the DEAD implied-vol (DVOL, B2) and instantaneous-momentum (B6) gates.

**Frozen numbers (BTC, buy-NO 3-25c, dt=3300 fresh, one obs/mkt):**
- LOW rv24 tercile (annualized ≤29.94): **n=720, win 92.1%, EV +2.30c**. HIGH tercile (≥38.28): +0.39c. Unconditional BTC base: **+0.63c** → the gate lifts +1.67c over base.
- Era-robust: LOW cell positive in both halves (+2.52 early / +1.82 late).
- ETH version DEAD (era sign-flip, ⊂3.5c placebo). BTC-only.

**Named blocker RESOLVED this rep (the reason for the upgrade).** The ideator capped I1 at CONDITIONAL pending proof it isn't just re-selecting calm-clock hours. Ran that test on disk (`scratchpad/i1_collinear.py`):
- **Not collinear:** calm-window (22-12 UTC) fraction is flat across rv24 terciles — LOW 0.63, HIGH 0.68, ALL 0.68. Low rv24 does not preferentially pick calm hours.
- **Stacks with the clock:** inside the calm window, rv24-LOW still separates +2.34 vs +0.25 (**spread +2.09c**); inside non-calm, +2.50 vs +0.23. The gate is independent of and additive to calm-clock (calm base +0.98 vs non −0.10).

**Remaining trap → why still PROMISING, not TRADE (correlated-sample).** rv24 is a 24h trailing statistic, so adjacent hourly markets share nearly-identical rv24. A "low-rv24 regime" is a handful of multi-hour episodes, not 720 independent draws — effective n is much smaller than the reported 720. Era-robustness mitigates but doesn't retire it. **Next gate:** episode-clustered / block-holdout test (cluster obs by rv24 regime episode, re-measure) before any TRADE call. Does not clear a holdout; cap at PROMISING.

### 2. A3 — staleness-selection overlay on the commodity-daily wing engine · **CONDITIONAL** (held)

**Mechanism.** Among the 3-25c commodity-daily YES wings the cwing engine already sells, prioritise the rungs whose last trade is oldest — a far-OTM rung nobody refreshed holds a stale-high quote while realized prob drifts to ~0. Last-print age (age3) is a free richness selector. Exclude WTI.

**Frozen numbers (sell-YES = buy-NO, +1c slip, fee in):**
- STALE tercile: **n=406, win 99.8%, EV +4.57c**, monotone above FRESH +3.09 / MID +3.52.
- Price-controlled (3-12c): STALE +4.17 vs FRESH +2.85 (~+1.3c independent staleness premium).
- Event-weighted (correlated-sample control): +3.47c / 104 events.
- 4/4 families + (Brent/Gold/NatGas/Silver, WTI excluded); meat-stale placebo −1.88.

**Trap that caps it (stale-quote / fill).** The signal *is* a stale price — the same staleness that flags richness means the NO offer you'd need to take may be thin or absent. The measured +4.57c assumes you transact at the stale mark; you may not. This is intrinsic, not disk-testable, and identical to the fill-realization blocker A6-maker carries. **Not upgradable without live commodity-daily book/quote capture.** Held at CONDITIONAL.

### 3. A6 — WTI daily↔hourly settlement identity · CONDITIONAL-research
Strike grids / settle-times don't align in current cubes; needs one ticker + settlement-time alignment metadata pull to confirm any shared print. Cheap; queue with the A8 new-rung watcher.

### 4. A8 — round-strike anchoring overlay · CONDITIONAL-research
Predicted direction holds within Brent (round $5-lines +10.29c vs off −2.88c) and clears the round-meat placebo (−22.7c), but pooled n=13 with 11/13 = Brent $5-lines — a single-family correlated cell far below the n≥60 bar. **Trips correlated-sample + pooled-cross-asset traps → cannot promote.** If later powered (more commodity strike history), it's a free strike-level selector that would stack on the A3 staleness selector over the same cwing sell-band. Do not weight yet.

### 5. F3 — equity-hours cross-condition · CONDITIONAL-collinear (confirmation only)
BTC eq-hours (13:30–20:00 UTC) buy-NO **−0.70c (n=209)** vs non-eq **+0.78c (n=1948)**, era-consistent in both halves — the one era-robust relaunch door. But eq-hours is the exact inverse-image of the slate calm-clock window (22–12 UTC), so it re-derives the calm-clock exclusion from the attention axis rather than adding EV. **Collinearity trap → held at confirmation-only.** ETH null. Value = independent cross-axis confirmation that US-active hours are where crypto wings are NOT rich; do not stack as a separate filter.

*(F1 weekend dropped to DEAD-subsumed: era-fragile — early era inverted (wknd +0.51 < wk +1.51), only the late era carries it (+2.72 vs −1.38 = regime-fake) — and its edge lives entirely inside LOW-rv24 (weekend-fraction 0.70 in LOW vs 0.12 in HIGH; weekend EV inside HIGH-rv24 = −3.88c). It is the I1 realized-vol regime wearing a calendar mask.)*

## Pipeline notes

- **One probe unblocks three survivors.** A3-staleness, A6-maker wing, and the A8 new-rung watcher all wait on the *same* build: **live commodity-daily book/quote capture** (do stale/wing marks have takeable NO offers?). Highest-leverage next build; it converts A3 from CONDITIONAL toward a fill-realistic TRADE test.
- **I1's next step is disk-only, not a pull.** Episode-clustered holdout on existing hourly obs — no capture needed. Cheapest path to promoting the night's best survivor; do it first.
- **Structural facts retained (not edges):** thick commodity wings are arbed dead (A1, −0.53) → cwing is a thin/mid-capacity edge suited to a small bankroll; commodity wings richen into T-3h (A2, +3.18) validating current entry timing; sell the whole 3-25c band, not the richest rung (A7); commodity favorites flip ~2.3pp more than priced but sub-fee (A4).
- **One attention factor, three masks (consolidation).** The relaunch doors confirm the wing-richness attention channel has a single underlying factor: weekend (F1), equity-hours (F3), and rv24 (I1) are calendar / clock / realized-vol parameterizations of the same quiet-regime richness. rv24 is the sharpest and is independent of the calm-clock (stacks +2.09c inside it, per the I1 test); the calendar/clock variants are collinear proxies that add no orthogonal EV. **Keep the single rv24 gate; do not stack collinear calendar/clock filters** — that would be double-counting one edge and inflating apparent capacity.
- **Doctrine held.** Reflexivity door re-closes on two fresh parameterizations (I3/I5); cross-asset lead-lag stays dead (I2); placebo calibration (BTC 0.84c / ETH 3.5c) correctly killed the ETH/relaunch noise (I2/I3/I5 ETH, F2 spread). No graveyard re-litigation.
- **Cost:** 1 decisive disk test (I1 collinearity), 0 pulls, 0 sub-agents. Cap respected.
