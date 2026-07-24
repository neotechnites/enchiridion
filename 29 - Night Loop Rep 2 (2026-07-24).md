# 29 — Night Loop Rep 2 · 2026-07-24

TOP MIND close of one overnight ideator rep (playbook note 20, steps 4–7). Two ideators ran Lane A (Structure & Venue) and Lane B (Attention & Information), 8 ideas each, 0 pulls, 0 sub-agents. This note applies the trap list (note 20 step 6) to every non-DEAD survivor and freezes verdicts. Opus/medium, capped rep. One extra disk-only recount run (no pulls).

## What ran

- **Lane A** (`night-loop-A.md`): 8 fresh doors — the 15M timescale and the Polymarket-US venue, neither walked by prior reps. 2 inline harness passes + placebos.
- **Lane B** (`night-loop-B.md`): 8 fresh Attention/Information doors, 1 harness pass (`laneB_night2.py`) over cwing 5 commodity families + hourly BTC/ETH.
- **This rep (TOP MIND):** 1 disk-only recount on the sole survivor (L5) — era split, unconditional-baseline marginal, per-event distribution. Ran against the on-disk `cwing_obs_*.jsonl` which turned out to span **May 18 – Jul 22 2026 (9 Mondays)**, wider than Lane B's stated Jun–Jul window.

## Counts

- **Lane A: 8 → 7 DEAD, 0 CONDITIONAL+, 0 survivors.** (A5–A8 DEAD-cited from doctrine, not re-run.)
- **Lane B: 8 → 4 DEAD, 1 CONDITIONAL (L5), 2 CONDITIONAL-research (L6/L7, untestable on disk), 1 confirmation-only (L8).**
- **Rep total: 16 ideas → 11 DEAD, 1 CONDITIONAL, 2 CONDITIONAL-research, 1 confirmation, 1 dead-cited overlap. 0 TRADE, 0 holdout cleared.**

## Consolidated ledger

| id | idea | verdict | frozen number |
|----|------|---------|---------------|
| A1 | 15M near-ATM opening-print calibration (BTC/ETH/SOL/XRP) | DEAD (structural, calibrated) | open-print bias <2c/bucket; fade EV −1 to −3.5c, n=4324 |
| A2 | DOGE 15M opening-fade | DEAD (stale-open mirage) | open +5.9c → established(60-120s) **−1.6c**, n=201; open→est move 13.3c; one-era (late +14.9/early −4.2) |
| A3 | Poly-US crypto-ladder monotonicity dutch | DEAD (near-tight) | 11/738 BTC, 18/719 ETH inversions ~2%, sub-0.5c |
| A4 | Poly-US crypto-ladder calibration fade | DEAD (regime-fake down-drift) | uniform −0.03 to −0.10c; stale snapshots age~84h |
| A5 | Being-house at DOGE 15M stale-rich open | DEAD-cited (taker-only, note 18) | 13.3c open-move IS the pickoff |
| A6 | Fee-cliff C* structural | DEAD-as-edge (cost, not counterparty) | doctrine, fee_floor.py |
| A7 | KBT frozen-book being-house | DEAD (no counterparty, note 18) | ~48% books frozen |
| A8 | Cross-venue Poly↔Kalshi settlement identity | DEAD-cited (graveyard I6/I7) | — |
| L1 | EIA storage-day (Thu) natgas wing richness | DEAD (wrong dir + placebo) | Thu evw −13.05 (storage wings PAY OFF); Gold-Thu placebo −3.35; n=5 |
| L2 | volume-as-attention reflexivity, commodity wings | DEAD (placebo hash ≈ signal) | Gold Hi +12.6/Lo +4.8 vs placebo +3.6/+11.1; Brent/WTI sign-flip |
| L3 | bid-ask spread thinness, crypto hourly wings | DEAD (sign-flip + placebo) | BTC W +0.10/N +0.87 (wrong dir); ETH sep ⊂ placebo |
| L4 | paxg_ret180 slow gold risk-off, crypto wings | DEAD-subsumed (collinear w/ dead B9 paxg60) | — |
| **L5** | **Monday commodity-daily wing richness** | **CONDITIONAL (calendar gate)** | **Mon evw +8.51c, win 96.4%, n=56 rungs / 30 events; 29/30 events positive** |
| L6 | natgas storage-SURPRISE magnitude gate (BUY wings) | CONDITIONAL-research (needs EIA surprise feed) | n=5 hint only |
| L7 | Deribit max-pain / OI pin, crypto hourly wings | CONDITIONAL-research (needs OI pull) | — |
| L8 | Thursday commodity-wing weakness | confirmation-only (same calendar factor as L5) | Thu evw −3.17c, n=61 |

## Survivors (ranked, small-bankroll first) — trap list applied

### 1. L5 — Monday commodity-daily wing richness · **CONDITIONAL** (capacity-small → small-bankroll first)

**Mechanism / who's wrong:** On Monday, sell (buy-NO) the 3–25c YES wings of the commodity-daily ladders (NatGas/Gold/Silver/Brent/WTI) the cwing engine already trades. Monday-AM wing buyers price accumulated weekend gap/geopolitics anxiety into far-OTM strikes; scheduled catalysts (EIA petroleum Wed, gas storage Thu, most macro mid/late-week) land *later*, so the realized Monday move is usually small and wings expire worthless more often than priced. Crowd never prices the quiet-Monday base rate. Orthogonal to rep28 crypto rv24/staleness — this is a commodity **calendar** gate.

**Frozen numbers** (fresh print age≤300s, buy-NO 3–25c, +1c slip, fee in; event-weighted = correlated-sample guard):
- Pooled Monday: **n=56 rungs / 30 events, win 96.4%, rung-mean +7.53c, event-weighted +8.51c.**
- Weekday separation clean: Mon +8.51 / Tue +0.01 / Wed +3.38 / Thu −3.17 / Fri +5.15c.
- Cross-family drop-worst-event, Monday evw, **5/5 positive**: NatGas +10.28, Gold +15.16, Silver +16.02, Brent +12.84, WTI +8.53c (WTI raw −3.34 was one −86.41 blowup event).

**Trap list applied (this rep's recount, disk-only):**
- **One-event / correlated-sample illusion — PASSES.** 30 distinct Monday events, per-event evw distribution min −86.4 / p25 +8.4 / median +11.7 / max +22.7; **29 of 30 events positive** (the single negative is the known WTI blowup). Not a few-event artifact; event-weighting already in place.
- **Regime-fake / unconditional-baseline — PASSES (with caveat).** The whole wing-sell book is already +2.17c unconditional (metals-are-rich, rep27 A6). Monday's **marginal over the all-week baseline is +6.34c** (8.51 − 2.17), so Monday adds edge rather than merely re-slicing the always-on richness. **Caveat:** the +2.17c always-on component that Monday rides could itself mean-revert.
- **One-window / one-era — MITIGATED, not retired.** On-disk data is wider than Lane B thought (May 18–Jul 22, 9 Mondays). Within-window era split holds same sign both halves: EARLY (≤6/18) evw **+12.76** (10 events, win 100%), LATE (>6/18) evw **+6.39** (20 events, win 95.1%). Reassuring, but this is still one contiguous ~2-month macro regime — **not a true cross-season / cross-year holdout.**
- **Candle lookahead / stale quotes — n/a.** Entry is a real fresh ladder print (age≤300s), settle is the daily outcome; no lookahead, no stale mark.
- **Tick-weighting — PASSES.** Reported on event-weighted EV, not rung-weighted.

**Verdict: CONDITIONAL (holds, capped).** Trips no trap decisively; the era split actually strengthens it beyond the Lane B ledger. Capped below TRADE for two live reasons: (a) single contiguous 2-month macro regime — no out-of-regime/seasonal holdout; (b) fill realization on thin commodity dailies is unproven (same blocker as the cwing engine). Small-bankroll suited (thin dailies = small capacity). **Next gate:** `cwing_pull.py` extension to ≥6 months incl. a distinct regime, then out-of-window Monday recount (disk-only, one pull <15min). If Monday holds cross-regime, promote toward TRADE as a free calendar overlay on the live cwing sell-band.

### 2. L6 / L7 — CONDITIONAL-research (not testable on disk)
- **L6 natgas storage-surprise magnitude gate** (BUY wings on big-surprise Thu — inverse of dead L1): needs an EIA storage-surprise feed to gate direction. n=5 hint only. No cheap disk test exists; leave parked.
- **L7 Deribit max-pain / OI pin** on crypto hourly wings: needs a Deribit OI/max-pain pull, none on disk. Parked. Neither ran; neither costs anything until a feed is pulled.

### Lane A — no survivors
All 7 live Lane A ideas DEAD with frozen kill numbers; A5–A8 dead-cited from standing doctrine (taker-only note 18, fee-floor, graveyard I6/I7). The two fresh doors (15M timescale, Poly-US venue) are now closed with numbers: 15M near-ATM binaries are efficient at the open for liquid coins (bias <2c); the DOGE 15M open-fade that lit up (+5.9c) is a stale-open-print mirage (−1.6c at fill-realistic established entry); Poly-US crypto ladders are monotone within ~2% and their apparent calibration bias is stale-snapshot down-drift, not an entry edge.

## Pipeline notes

- **L5 is the only thing worth another dollar of compute this rep**, and its next step is deterministic and cheap: one `cwing_pull.py` extension to a longer + distinct regime, then a disk-only Monday recount. Everything needed already exists in `laneB_night2.py` + `cwing_analyze.py`; no new harness.
- **On-disk cwing coverage is wider than the Lane B ledger stated** (May 18–Jul 22, 9 Mondays, not "single Jun–Jul"). Future reps should read the actual date span off disk before quoting window size — the extra era split was free and materially strengthened the survivor.
- **L5 rides a positive unconditional baseline (+2.17c always-on wing richness).** Any promotion to TRADE must confirm the baseline richness itself hasn't decayed, else the calendar overlay inherits a mean-reverting substrate.
- **Two research doors (L6 EIA-surprise, L7 Deribit OI) are the cheapest un-opened doors left** — each is one feed-pull away from testable and both are catalyst/attention channels the loop hasn't touched. Flag for a rep that has a pull budget.
- Lane A's 15M-open→established 13.3c move is a standing venue-promo watch item: if Kalshi ever runs a maker-rebate on thin 15M books, that uninformed spread flips from trap to being-the-house edge. Not a current trade.
