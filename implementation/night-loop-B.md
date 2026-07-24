# Night Loop — Lane B (Attention & Information) · Rep 2

**UTC:** 2026-07-24 ~07:30Z · Lane B · Opus/medium, capped rep. FRESH doors only (nothing re-skinned from reps 27/28).
Harness: `kalshi_data/scripts/laneB_night2.py` (one pass, cwing 5 families + hourly BTC/ETH). 0 pulls, 0 sub-agents.
Method: sell-YES wing = BUY NO, band YES px 0.03–0.25, entry NO=(1−y)·100+1¢ slip, fee 7·y(1−y)¢, fresh print age≤300s, event-weighted EV (correlated-sample guard) + placebo cell. Predicted-direction-or-dead.

## Ledger

| id | idea | mechanism (who's wrong) | kill-test | n | win% | EV-net (evw) | verdict | files |
|----|------|------|------|----|------|------|---------|-------|
| L1 | EIA storage-day (Thu) natgas wing richness | crowd buys natgas move-lottery on Thu 14:30Z storage print → wings overpriced | Thu vs non-Thu buy-NO; placebo=Gold Thu (no storage feed) | 5 | 80.0 | **−13.05** (nonThu −4.03) | **DEAD** (wrong dir — storage day wings PAY OFF; placebo Gold-Thu also −3.35 → general Thu effect, not storage; n=5) | laneB_night2.py |
| L2 | volume-as-attention reflexivity, commodity wings | high-vol rung = crowd pile-in → overpaid lottery → sell | per-family vol median split; placebo=event-hash parity | 21–45/fam | — | Gold Hi+12.6/Lo+4.8, Brent Hi−2.3 | **DEAD** (placebo hash spread +3.6/+11.1 ≈ signal; Brent/WTI wrong dir → cross-family inconsistent = artifact) | laneB_night2.py |
| L3 | bid-ask spread as thinness/attention signal, crypto hourly wings | wide spread = MMs absent → wing stale-rich | wide vs narrow spread buy-NO; placebo=hash | 211–507 | 86.7–92.6 | BTC W+0.10/N+0.87; ETH W+1.54/N−0.66 | **DEAD** (BTC wrong dir; ETH sep ⊂ placebo +1.13/−0.00) | laneB_night2.py |
| L4 | paxg_ret180 slow gold risk-off on crypto wings | slower gold-trend channel nobody reprices | cited | — | — | — | **DEAD-subsumed** (collinear w/ dead B9 paxg60; slower ⇒ weaker) | rep27 B9 |
| **L5** | **Monday commodity-wing calendar richness** | **Mon-AM wings price weekend-gap/geopolitics anxiety; no catalyst hits before daily settle (EIA Wed/Thu) → systematically overpriced → sell** | **weekday split, pooled + per-family + drop-worst-event; complement=Thu** | **56 (~30 ev)** | **96.4** | **+8.51** | **CONDITIONAL (calendar gate)** | laneB_night2.py |
| L6 | natgas storage-SURPRISE magnitude gate (BUY wings on big-surprise Thu) | inverse of L1: storage day natgas MOVES → wings hit | needs EIA surprise feed to gate direction | — | — | — | **CONDITIONAL-research** (untestable on disk; n=5 hint only) | — |
| L7 | Deribit max-pain / OI pin channel on crypto hourly wings | option OI pins spot near max-pain; wings don't price it | needs Deribit OI/max-pain pull | — | — | — | **CONDITIONAL-research** (no OI on disk) | — |
| L8 | Thursday commodity-wing weakness | inverse image of L5 (catalyst days) | weekday split | 61 | 88.5 | −3.17 | **confirmation-only** (same calendar factor as L5, not new EV) | laneB_night2.py |

**Tally: 8 ideas — 4 DEAD (structural, placebo/direction), 1 CONDITIONAL, 2 CONDITIONAL-research, 1 confirmation. 0 TRADE, 0 holdout cleared.**

## Survivor (frozen kill numbers)

### L5 — Monday commodity-daily wing richness · CONDITIONAL (calendar gate)
**Rule (plain English):** On Monday, sell (buy-NO) the 3–25¢ YES wings of the commodity-daily ladders you already trade (cwing engine). The wing curve carries weekend-anxiety premium that no Monday catalyst realizes before the daily settle.

**Mechanism / who's wrong:** Monday-AM wing buyers price accumulated weekend gap/geopolitics risk into far-OTM strikes; scheduled catalysts (EIA petroleum Wed, gas storage Thu; most macro mid/late-week) land later in the week, so the Monday realized move is usually small → wings expire worthless more often than priced. The crowd never prices the "quiet-Monday" base rate. Attention/calendar door, not covered by reps 27/28 (crypto weekend F1 was DEAD-subsumed; commodity-daily **Monday** is fresh).

**Frozen numbers (fresh print age≤300s, buy-NO 3–25¢, +1¢ slip, fee in):**
- Pooled Monday: **n=56 rungs (~30 events), win 96.4%, rung-mean +7.53¢, event-weighted +8.51¢.**
- Complement Thu −3.17¢, Tue +0.01¢, Wed +3.38¢, Fri +5.15¢ → **Monday cleanly separates** (predicted direction held).
- **Cross-family (correlated-sample guard), Monday event-weighted, drop-worst-event:** NatGas +10.28¢, Gold +15.16¢, Silver +16.02¢, Brent +12.84¢, WTI +8.53¢ — **5/5 families positive.** NatGas (negative in every other slice in rep27) flips positive on Monday. WTI raw −3.34 was one blowup event (min −86.41); drops to +8.53.

**Why capped at CONDITIONAL, not TRADE:** single era (Jun–Jul 2026), ~30 Monday events; cross-family consistency mitigates but does not retire the correlated-sample/one-window trap. Same fill-realization blocker as the cwing engine (thin dailies). **Next gate:** pull ≥6-month commodity-daily wing history and re-measure the Monday cell out-of-window (disk-only after one `cwing_pull.py` extension — cheap). If it holds cross-era, promote toward TRADE as a free calendar overlay on the live cwing sell-band.

## Notes
- L2/L3 both died the same way (placebo hash spread as large as the signal + cross-family/cross-asset sign flips) — attention-proxy selectors (volume, spread) inside the wing band re-slice the metals-are-rich fact (rep27 A6), they don't add orthogonal EV.
- L5 is orthogonal to rep28's rv24 (crypto) and A3 (staleness) — it's a commodity **calendar** gate, capacity-small (thin dailies) = small-bankroll suited. Best survivor of this rep.
- Cost: 1 harness pass + 1 per-family guard recount. 0 pulls, 0 sub-agents.
