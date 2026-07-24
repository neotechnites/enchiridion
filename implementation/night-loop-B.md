# Night Loop — Lane B (Attention & Information)
UTC 2026-07-24 ~09:05 · Lane B ideator · Opus/medium · capped rep · playbook note 20

Fresh-door mandate: crypto rv/momentum/cross-asset attention is SATURATED — note 30 doctrine (D7 proved cross-asset rv24 perfectly collinear w/ I1, 0 off-diagonal; every rv/momentum/calm skin collapses to I1). So this rep walked a **channel the loop has never touched: WEATHER markets** (KXTEMPNYCH hourly-temp, KXHIGH* daily-high, obs_*.csv METARs, ens_forward) plus one entertainment feed. 2 real disk passes, 0 pulls, 0 sub-agents.

## Ledger

| id | idea | mechanism (who's wrong) | kill-test | n | win%/realY | EV-net | verdict | files |
|----|------|-------------------------|-----------|---|-----------|--------|---------|-------|
| W1 | Weather hourly-temp longshot richness (KXTEMPNYCH) | weather retail overpays far-tail temp lottery tickets; climatology/METAR feed nobody prices | banded realized-YES vs price; pre-settle live-quote midpoint | 1569/198ev (last-price); 1440/144ev (live-quote) | tail 2-10c realY **0.0%** (163); ATM realY 55.7% | tail sellYES **+1.46c** (fee-noise); live book degenerate | **DEAD** (structural, on-disk) | settled_KXTEMPNYCH.json |
| W2 | Weather daily-HIGH monotone-lock / METAR-lag (KXHIGH*) | daily high = cumulative-max (monotone ↑); once running observed max crosses a bucket floor YES is LOCKED, but thin all-day weather book may not reprice at the hourly METAR update few watch | frac of already-locked-YES buckets still buyable <98c (needs intraday KXHIGH book keyed to METAR post-times) | — (not on disk) | — | — | **CONDITIONAL-research +gate** | ens_forward.jsonl, obs_*.csv |
| W3 | Cross-city heat-dome correlation nobody prices | one synoptic system → NY/DC/ORD/AUS highs co-move; a multi-city wing basket is NOT N independent bets | pairwise corr of daily-high residuals across obs cities | obs Apr–Jul 5 cities | — | — | CONDITIONAL-research (capacity fact, not standalone edge) | obs_*.csv |
| N1 | Netflix/entertainment Top-10 stale-list channel | FlixPatrol/Tudum daily rankings update before Kalshi weekly Top-10 settle; info channel nobody on Kalshi reads | rank-vs-price on settle week (needs a market pull) | — (no market data on disk) | — | — | RESEARCH-DOOR (1 pull; not run, cost discipline) | — |
| R1 | Weather taker-flow reflexivity (1h window) | attention burst = YES-buying spike → overshoot → reversion | flow-imbalance → next-price | trades_temp = single date + 429s | — | — | DEAD-cited (reflexivity graveyard B7/I3/D4) + underpowered | trades_temp.jsonl.gz |
| X1 | "Sell wings while a DIFFERENT family is calm" cross-condition | cross-family calm as orthogonal gate | 2×2 diagonal count | — | — | — | DEAD-cited (note 30: collapses to I1; D7 = 0 off-diagonal) | — |
| X2 | Weather-heat × commodity-wing cross-condition (heat→energy demand) | extreme-heat day → natgas/WTI demand tail → daily-wing behavior; "commodity wing while heat-anomaly" | split cwing EV by same-day city heat-anomaly (obs) vs L5-Monday + always-on base marginal + placebo | — | — | — | CONDITIONAL-research (must clear L5/base marginal like D8 failed; low prior) | obs_*.csv, cwing_obs_* |
| F0 | (structural fact) KXTEMPNYCH hourly reading efficient | 1h trading window closes AT the reading; near-settlement prices only | ATM realized-YES vs mid | 1421 | realY 55.7% @ mid 0.500 | ~0 | structural fact (no edge; supports W2 — the weather edge, if any, is in the all-day HIGH ladder) | settled_KXTEMPNYCH.json |

## Frozen kill numbers

### W1 — Weather hourly-temp longshot · DEAD (structural, on-disk)
- **last_price entry (maximally-informed):** 2-10c YES band n=163, realized-YES **0.0%**, mean price 2.6c → sell-YES EV **+1.46c** net (fee in). Same **deep-tail-fair fee-noise class as rep27-A5** (+0.46c), new venue — 163/163 already-dead longshots at the last print, not a tradeable window.
- **Pre-settlement live-quote entry:** 1421/1440 markets have a degenerate previous quote (bid 0 / ask 1 → mid 0.500, spread 100c). **No usable pre-settlement mid on disk** — the KXTEMPNYCH book is empty/one-sided until the near-certain last trade. Door closed with numbers.
- ATM (mid 0.50) realized-YES 55.7% → hourly reading efficiently priced at settlement.

### W2 — Weather daily-HIGH monotone-lock / METAR-lag · CONDITIONAL-research (the fresh survivor)
- **Mechanism real and un-tested:** unlike the 1h KXTEMPNYCH (efficient, F0), KXHIGH* markets trade ALL DAY and settle on the **cumulative daily max — a monotone non-decreasing quantity**. Once the observed running max (hourly METAR) crosses a bucket floor, that bucket YES is mathematically LOCKED for the rest of the day, yet the thin weather book has few watchers refreshing KNYC/KORD METARs at the top of each hour. Textbook "feed nobody watches at the moment it updates" — NOT latency racing (updates hourly).
- **Why no number:** ens_forward is a single ~13:00Z morning snapshot (before the high); obs_*.csv has METARs but no intraday KXHIGH best-quote to pair against. NOT disk-testable this rep.
- **Named gate (cheapest decisive):** intraday KXHIGH best-quote capture (5 ens cities already listed) snapping at METAR post-times (~:51/:15). Kill = fraction of already-locked-YES buckets whose YES ask is still ≤98c (or busted buckets whose NO is still buyable) at the next snap, n≥60 locked buckets. Lag = capacity-small edge; instant reprice = DEAD-decay. Zero build cost beyond the capture loop.

## Counts
8 ideas. **1 DEAD structural on-disk (W1)** + 2 dead-cited (R1, X1) = 3 killed; **1 structural-fact (F0)**; **3 CONDITIONAL-research (W2, W3, X2)**; **1 research-door (N1)**. **0 TRADE, 0 PROMISING, 0 holdout cleared.** No numeric survivor — the one on-disk-testable fresh channel (weather hourly-temp) closed with numbers; the promising fresh door (W2 daily-high lock) is capture-gated.

## Pipeline note
Freshest un-opened attention door on the whole slate is now **W2: intraday KXHIGH best-quote capture keyed to hourly METAR updates** — orthogonal to every crypto/commodity edge (different venue, genuinely monotone settlement identity, thin low-attention book), one small capture loop. Add weather HIGH markets to the standing "one book-capture unblocks several survivors" build (A3/A5/A8).
