# 09 - Deep-Favorite Lock test (DEAD)

> ⚠️ **Disambiguation (2026-07-23): this is a STRUCTURAL kill (favorite-underpricing mirage, OOS-fail) and stays dead. It is a DIFFERENT idea from [[08 - The Lock Edge - Settlement-Lock Favorite]]** — that later Z≥4 settlement-lock edge was REAL, then died separately by DECAY in Jul 2026. Do not conflate the two deaths: this one never worked; that one worked and got eaten. Taxonomy: note 15.

> Session 2026-06-25. Ryan's "1% on near-guaranteed trades, 10×/day, pick which to grab" idea = buy deep favorites (95–99¢) where Kalshi's fee is near-zero at scale, selecting the ones that are genuinely locked. **Tested thoroughly. It's a mirage. Do NOT revisit without a genuinely new framing.** Same class as the favorite-underpricing trap in [[00 - START HERE]] / [[07 - Overfitting & Validation Discipline]].

## The idea
- Fee at scale = `0.07·P·(1−P)`/contract → ~0.07¢ at 99¢ (the ceil is on the aggregate order, negligible at size). So deep favorites are nearly fee-free = off-50 in the fee sense.
- Buy a 95–99¢ favorite, win the small gap, ~1%/trade. Edge exists **only if** the favorite is underpriced (true win > price + fee).
- Selection signal tried: **the settlement lock.** Settlement = 60-sec BRTI average, so a contract far from strike with little time left is mechanically ~locked. Measure lock as `z = (BTC distance from strike) / (1-min σ × √minutes-left)` = "how many normal moves clear of the line."

## What the data said (`deepfav_calib.py`, `lock_edge2.py`, `lock_discriminate.py`, `your_proposal.py`)
1. **Naive deep-favorite calibration ≈ break-even.** 99¢ band wins ~99.5–100% but you need >99.07% just to clear fee; one loss at 99¢ wipes ~100 wins. Pooled BTC+ETH.
2. **Win-rate vs lock-strength is NON-MONOTONE = noise.** Pooled, 97–99¢ band by z: 100% → 97% → 100% → 97% → 100%. If lock were real it would rise monotonically. Operating cell 96–99¢ z≥2 = **+0.01¢ EV** (zero).
3. **Regime split incoherent:** rule's low-vol half −0.56¢, high-vol half +0.88¢ — no stable mechanism.
4. **Ryan's exact wording** ("95–97¢, ~2 min left, 4+ normal moves clear"): only **11 such cases exist in the whole dataset** (10 ETH, 1 BTC). Structural reason — **if the coin is truly 4+ moves clear, the market has already priced it to 98–99¢, not 95–97¢.** "4 moves clear" and "still 95–97¢" contradict each other; the 11 that exist are vol-estimate lag, not edge. moves-clear sweep: 0–2→97.6%, 2–3→100%, **3–4→94.3% (−2.3¢, loses)**, 4–6→100% = noise. Placebo (<4 moves) still wins 97.7% → the filter adds nothing actionable.

## Verdict
The off-50 deep-favorite space is the favorite-underpricing trap wearing a lock costume: cherry-picked cells look great, evaporate on pooling/monotonicity/regime checks, and the thin edge can't survive the binary steamroller tail. **Dead.** The real off-50 money is on the CHEAP contrarian side, not the expensive sure-thing side → [[08 - Cheap-Side Reversal (off-50 candidate)]].

Related: [[07 - Overfitting & Validation Discipline]] · [[03 - All Strategies Tested]] · [[08 - Cheap-Side Reversal (off-50 candidate)]]
