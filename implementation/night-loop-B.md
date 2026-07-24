# Night Loop Lane B — Attention & Information (2026-07-24 ~08:10 UTC)

Lane B, Opus/medium, capped rep. 8 FRESH attention/information doors, 0 pulls, 0 sub-agents.
Harness: `kalshi_data/scripts/laneB_night3.py` (crypto hourly BTC/ETH obs, dt=3300, fresh stale==0, band px 0.03-0.25, buy-NO wing-fade, one obs/mkt, event-weight guard, hash placebo, era split). Follow-up recounts inline (D7 2x2 vs I1; D8 cwing turn-of-month).
Doors chosen to NOT re-skin prior Night reps (27/28/29): none repeat rv24/momentum/funding-roc/kimchi/DVOL/offshore-roc/weekend/equity-hours/Monday/Thursday/EIA/volume/spread/PAXG. Two dead crypto feeds (funding, offshore) re-entered ONLY via conditional-kill + NEW named gate (level-as-sentiment, not roc).

## Ledger

| id | idea | mechanism (who's wrong) | kill-test | n | win% | EV-net (evw) | verdict | files |
|----|------|-------------------------|-----------|---|------|--------------|---------|-------|
| D1 | BTC-ETH decoupling gate on BTC wings | crowd prices shared-beta narrative; decoupled hrs = idiosyncratic, lower shared tail → predict wings richer | DECOUPLED vs coupled evw spread, era, placebo | 372 | 87.9 | evw +0.87 vs coupled +0.84, spread **-0.12** | **DEAD** (wrong-dir + era-flip early+1.13/late-0.36, <placebo 0.94) | laneB_night3.py |
| D2 | ETH wings while BTC-quiet (X-while-A cross-asset rv24) | ETH wing crowd follows BTC vol perception, misses ETH's own quiet | BTCrv-LOW vs rest, ETH placebo, era | 280 | 93.2 | +1.42 (evw +1.21) vs rest +0.20, spread +1.22 | **DEAD-as-stated** (signal +1.22 < ETH hash placebo +2.52; era sign-flip early+4.05/late-1.20) | laneB_night3.py |
| D3 | Bitstamp US-venue dislocation gate | \|bstamp_prem\| small = uninformed retail regime → predict LOW-disloc wings richer | LOW\|disloc\| vs HIGH tercile | 752 | 87.8 | LOW **-0.11** (evw+0.61) / HIGH **+1.94** (evw+1.82) | **DEAD-as-stated** (predicted-wrong-direction). Inverse candidate flagged below, not mined | laneB_night3.py |
| D4 | 15m micro-streak reflexivity on hourly wing | streak15 chase → post-streak wings overpriced vs flat | \|streak15\|<=1 vs >=2 | 1244 | 88.7 | +0.56 vs +0.40, spread **+0.16** | **DEAD** (< placebo 0.94) | laneB_night3.py |
| D5 | fund_btc positioning LEVEL (cond. re-entry of dead B4 roc-door; NEW gate=level-as-sentiment) | crowded longs (high +funding) = euphoria → downside wing underpriced | HIGHfund tercile vs rest, era | 752 | 87.9 | +0.83 (evw+1.45) vs +0.31, spread +0.52 | **DEAD** (regime-fake: era sign-flip early+3.74/late-2.06) | laneB_night3.py |
| D6 | okx_prem LEVEL sign (cond. re-entry of dead I7 roc-fade; NEW gate=level regime) | offshore-venue directional pressure as US-blind info channel | okx_prem>0 vs <=0 | 2255 | 88.2 | degenerate: **100% obs >0**, n=0 negative | **DEAD (structural)** — feed has no sign dispersion, no counterparty (cf B1 mempool) | laneB_night3.py |
| D7 | BTC wings while ETH-quiet (mirror of D2; cross-asset rv24 as attention signal) | is ETH-rv24 a better/independent BTC-wing richness gate than BTC's own rv24 (I1)? | ETHrv-LOW vs rest; **2x2 orthogonality vs I1**; era | 397 | 94.5 | **+5.23 (evw +5.44)** vs rest +0.08, spread +5.15, placebo +0.84 | **CONDITIONAL-collinear** — confirms I1, adds NO orthogonal EV | laneB_night3.py |
| D8 | Turn-of-month commodity wing calendar attention | month-end rebalance/expiry attention drawn off far-OTM strikes; realized TOM move small | TOM(day>=26 or <=3) vs MID, per-family, even/odd placebo | 88 | — | TOM rung +5.12 (evw **+3.93**) vs MID evw +1.53 | **CONDITIONAL-research** (underpowered n=88; metals+crude carry, NatGas -11.96 n=6; overlaps L5 Monday factor) | laneB_night3.py |

## Survivors (frozen numbers)

### D7 — cross-asset rv24 gate = I1, CONFIRMED but COLLINEAR (not a new edge)
**2x2 kill is decisive:** BTCrv-LOW and ETHrv-LOW are *perfectly collinear* — every ETHrv-LOW obs is also BTCrv-LOW (n=397 on both diagonal cells, **0 in both off-diagonal cells**). "BTC wings while ETH-quiet" is I1 (rep28 PROMISING, BTC-by-BTC-rv24) relabeled. It adds no independent information.
**Value:** confirms the rv24 low-vol regime gate is robust on a wider matched obs base: LOW cell EV **+5.23c, evw +5.44c, win 94.5%, n=397**, era-robust same sign both halves (early +6.15 / late +4.32). Strengthens I1; does not extend the slate. **Doctrine reinforced: BTC and ETH realized vol are ONE gate — do not stack cross-asset rv24 as a separate filter (would double-count I1).**

### D8 — turn-of-month commodity wing · CONDITIONAL-research (underpowered)
TOM evw +3.93 vs MID +1.53 (marginal +2.40 over MID; placebo even/odd spread only +1.14 → clears placebo but thinly). Per-family metals+crude positive (Gold +12.53, Brent +7.67, WTI +3.04), NatGas negative (-11.96, n=6) — same family profile as the always-on richness (rep27 A6) and likely overlaps the L5 Monday calendar factor (rep29). n=88 rungs is below the n≥60 event bar once event-weighted; WTI-blowup exposure. **Not promotable without ≥6mo cwing history to (a) power it and (b) test orthogonality vs L5 Monday.** Queue with the L5 `cwing_pull.py` extension already pending.

### D3-inverse — Bitstamp HIGH-dislocation richness · candidate (NOT promoted)
Predicted direction was wrong, so DEAD-as-stated per predicted-direction-or-dead. But the *inverse* separates cleanly (HIGH \|bstamp_prem\| evw +1.82 vs rest +0.96, n=753) with a plausible mechanism: large US-venue dislocation = crowd panic/attention spike → wing overpay. Flagged for a future rep to test with its OWN placebo + era split before any conditional call. Not slice-mined tonight.

## Rep verdict
**8 fresh doors. 6 DEAD (D1,D4 <placebo; D2 <placebo+era-flip; D3 wrong-dir; D5 regime-fake era-flip; D6 structural degenerate-feed). 2 CONDITIONAL+ (D7 collinear-confirm, D8 research-underpowered). 0 new orthogonal TRADE/PROMISING, 0 holdout cleared.**
Best result is a CONSOLIDATION, not a new edge: D7 proves cross-asset (ETH) rv24 is perfectly collinear with I1's BTC-rv24 gate and confirms it era-robust at **evw +5.44c, win 94.5%, n=397**. The attention channel keeps collapsing to the single quiet-realized-vol regime factor (rep28's finding, now cross-asset-confirmed).
Cost: 1 harness + 2 inline recounts, 0 pulls, 0 sub-agents.
