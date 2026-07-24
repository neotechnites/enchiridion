# 30 — Night Loop Rep 3 · 2026-07-24

TOP MIND close of one overnight ideator rep (playbook note 20, steps 4–7). Two ideators ran Lane A (Structure & Venue) and Lane B (Attention & Information), 8 ideas each, 0 pulls, 0 sub-agents. This note applies the trap list (note 20 step 6) to every non-DEAD survivor and freezes verdicts. Opus/medium, capped rep. One extra disk-only recount (no pulls) — a decisive orthogonality kill on the best new candidate (D8).

## What ran

- **Lane A** (`night-loop-A.md`): 8 fresh doors — the KXINXU S&P-hourly-wing family and the idx bracket complete-partition dutch, neither walked by reps 27–29. 2 inline harness passes + placebos.
- **Lane B** (`night-loop-B.md`): 8 fresh Attention/Information doors, 1 harness pass (`laneB_night3.py`) over hourly BTC/ETH + inline cwing recounts.
- **This rep (TOP MIND):** 1 disk-only recount (`scratchpad/d8_recount.py`) on D8 turn-of-month, reusing the exact `cwing_analyze.py` methodology (y3, buyNO 3–25c, +1c slip, fee in, age≤5400s, event-weighted by series-day). On-disk cwing spans **May 18 – Jul 22 2026**, n=893 tradeable rungs / 183 series-day events — wider than the Lane B D8 slice (n=88).

## Counts

- **Lane A: 8 → 7 DEAD, 1 CONDITIONAL-research (A5).** A7 downgraded DEAD (regime-fake, see below); A6/A8 DEAD-cited/characterized from doctrine.
- **Lane B: 8 → 7 DEAD, 0 CONDITIONAL+, 1 confirmation-only (D7).** D8 downgraded DEAD by this rep's recount (placebo fail + subsumed).
- **Rep total: 16 doors → 14 DEAD, 1 CONDITIONAL-research (A5), 1 confirmation-only (D7). 0 TRADE, 0 PROMISING, 0 holdout cleared.**

## Consolidated ledger

| id | idea | verdict | frozen number |
|----|------|---------|---------------|
| A1 | Index-hourly wing-sell richness (KXINXU) | DEAD (artifact) | buyNO 3–25c evw −3.86c; ATM 40–60c placebo −10.07c → base is bull-drift-directional |
| A2 | Idx bracket complete-partition dutch (INX) | DEAD (structural) | partition complete 43/43 exactly-1-yes; over-round 1.180 is house vig, eaten by spread + ~12×ceil-fee |
| A3 | Deep-tail fee-cliff dead-zone (INXU 1–3c) | DEAD (structural, 2-family) | +0.15c; ceil-fee ≈ tail overprice (matches rep27-A5) |
| A4 | NDX bracket dutch (thinner variant) | DEAD (structural) | over-round 1.085; 1/43 not exactly-1-yes → settlement-ID risk |
| A5 | New-listing first-quote sloppiness, 15M crypto (KBT) | **CONDITIONAL-research** | testable but unpowered n≈19 « 60; frozen gate n≥60 live-book |
| A6 | Being-house / maker on index-hourly wings | DEAD-cited (taker-only, note 18) | subsumed by A1 (base directional, no stable overpay) |
| A7 | INXU close-hour venue-thinness gate | **DEAD** (regime-fake, downgraded) | can't gate on a base whose unconditional ATM placebo is −10c; n≈18/hr |
| A8 | KBT L2 spread-floor = 2×ceil-fee | DEAD-as-edge (cost fact) | ATM 15M book 1c spread where 2×fee≈3.4c → quoted inside fee floor |
| D1 | BTC-ETH decoupling gate, BTC wings | DEAD (wrong-dir + era-flip) | spread −0.12; early +1.13/late −0.36 < placebo 0.94 |
| D2 | ETH wings while BTC-quiet (X-while-A) | DEAD (< placebo + era-flip) | signal +1.22 < ETH placebo +2.52; early +4.05/late −1.20 |
| D3 | Bitstamp US-venue dislocation gate | DEAD-as-stated (predicted wrong-dir) | LOW −0.11 / HIGH +1.94; inverse parked, not mined |
| D4 | 15m micro-streak reflexivity | DEAD (< placebo) | spread +0.16 < placebo 0.94 |
| D5 | fund_btc positioning LEVEL | DEAD (regime-fake era-flip) | early +3.74 / late −2.06 |
| D6 | okx_prem LEVEL sign | DEAD (structural, degenerate feed) | 100% obs one sign, n=0 counterparty |
| D7 | BTC wings while ETH-quiet (cross-asset rv24) | **confirmation-only** (collinear w/ I1) | 2x2: n=397 both diagonals, 0 off-diagonal; confirms I1 evw +5.44c, win 94.5%, era-robust |
| D8 | Turn-of-month commodity wing calendar | **DEAD** (placebo fail + subsumed, downgraded) | see recount below |

## Survivors (ranked, small-bankroll first) — trap list applied

### 1. A5 — KBT new-listing first-quote sloppiness, 15M crypto · **CONDITIONAL-research**

**Mechanism / who's wrong:** A freshly-listed 15M crypto market may carry a stale/default quote in the seconds before MMs arrive; `capture_kbt.py` now snaps at open+0.6s. If the first quote is systematically mispriced vs settle, take it. Small 15M markets → small-bankroll suited.

**Trap list applied:** The mechanism *is itself* a stale-quote hypothesis, which is legitimate as an edge (the pickoff is the counterparty's stale mark, not ours) — but it means the measurement must compare the first live snap to settle with no candle lookahead, exactly as framed. **No trap trips; the only blocker is power.** Only ~37 KBT markets captured, ~half frozen → n≈19 « 60.

**Verdict: CONDITIONAL-research (capped).** Cheapest decisive test is defined (first-snap best-quote vs settle, live-book markets only) and the data pipe already captures from open. **Frozen gate: re-run at n≥60 live-book KBT markets (~2wk accrual on the running capture machine).** Zero build cost. The single cheapest un-opened structural door on the slate.

### 2. D7 — cross-asset (ETH) rv24 gate · **confirmation-only, NOT a new survivor**

**Trap list applied — correlated-sample illusion / pooled cross-asset (TRIPS, as Lane B found):** "BTC wings while ETH-quiet" is *perfectly collinear* with I1's BTC-by-BTC-rv24 gate — the 2x2 shows n=397 on both diagonal cells and **0 in both off-diagonals**. It is I1 relabeled; it adds no orthogonal information. **Doctrine reinforced: BTC and ETH realized vol are ONE gate — never stack cross-asset rv24 as a second filter (double-counts I1).**

**Value:** strengthens the standing PROMISING I1 (rep28) as era-robust on a wider matched base — LOW cell **evw +5.44c, win 94.5%, n=397**, same sign both halves (early +6.15 / late +4.32). Does **not** extend the slate. Caps at confirmation-only; clears no holdout.

### D8 — turn-of-month commodity wing · **DEAD (downgraded from CONDITIONAL-research)**

**This rep's decisive disk-only recount** (n=893 rungs / 183 events, full cwing methodology, event-weighted):
- **Even/odd-day placebo FAILS:** even-day evw **+4.82** vs odd-day **+1.33** → placebo spread **+3.49c**, ≈ D8's own TOM-vs-MID marginal (TOM +4.43 vs MID +2.71 = **+1.72c**). The day-of-month axis separates as much under a *random* even/odd partition as under the turn-of-month hypothesis → **inside its own noise floor.** Trips the regime-fake / correlated-sample trap.
- **Orthogonality to L5 (rep29 Monday) FAILS:** stripped of Monday and of the always-on base (MID-&-not-Monday evw **+2.15c**, the rep27-A6 always-on richness), the residual **TOM-&-not-Monday is only +3.40c evw → +1.25c marginal over base** on 39 events. Most of D8's headline came from TOM-&-Monday (evw +8.90, 9 events), which is L5 re-sliced.
- **Family-inconsistent / one-event:** NatGas TOM evw **−4.50** on only 6 events; the pooled number is carried by Gold/Brent/Silver in a single 2-month macro window.

**Verdict: DEAD.** A calendar re-slice of L5 Monday + the always-on wing richness, with a placebo as large as the effect. Not an orthogonal edge.

### Lane A / Lane B — no new survivors
All other doors DEAD with frozen kill numbers above. D3-inverse (Bitstamp HIGH-dislocation) remains a **parked candidate, NOT promoted** — it is a post-hoc direction flip (predicted wrong-dir, inverse mined) with no own placebo or era split yet; promoting it now would be garden-of-forking-paths. Leave for a rep that will run it with its own placebo first.

## Pipeline notes

- **A5 (KBT first-quote) is the only live thread worth another dollar of compute**, and it costs nothing until data accrues: no pull, no new harness, just ride `capture_kbt.py` until n≥60 live-book markets, then one first-snap-vs-settle pass. Flag for a future rep with accrued data.
- **The attention channel keeps collapsing to one factor.** D7 is the third confirmation (now cross-asset) that crypto-hourly wing richness gates on the single quiet-realized-vol regime (I1). Stop proposing new rv/momentum/cross-asset skins — they re-derive I1. New orthogonal information must come from a *different* channel (order-flow, OI/max-pain, venue microstructure), not another realized-vol transform.
- **Calendar factors on the cwing base are saturated by L5 Monday + the +2.15c always-on richness.** D8 (turn-of-month) joins L8 (Thursday) as a re-slice, not a new gate. Any future calendar door on commodity dailies must show a marginal over *both* Monday and the always-on base, with an even/odd (or hash) placebo below its effect — D8 failed exactly that bar.
- **Standing best edge remains I1** (crypto-hourly wing-sell gated on low BTC rv24), PROMISING and now cross-asset era-confirmed at evw +5.44c / win 94.5% / n=397. Its blockers are unchanged: single ~2-month macro regime (no cross-season holdout) and unproven fill realization on thin hourly books. Next gate is a longer/distinct-regime pull, not another idea.
- **No pulls this rep; recount reused `cwing_analyze.py` + `laneB_night3.py` patterns.** Cost: 1 disk-only recount, 0 pulls, 0 sub-agents.
