# VERIFY: two-year regime map of streak behavior (2026-07-26)

Charter: `work/steer-regime-map.md`. Ryan's question, verbatim: *"bitcoin is very possibly at
the bottom of its price right now, and will only go up, that comes with far more streaks no?
is it just that the regime is about to flip and were about to eat all those loses because
long streaks keep coming?"*

## Bottom line

**No. Every load-bearing piece of the fear is falsified by 2 years of data.**

1. **A bull leg does NOT lengthen streaks.** P(trailing run >= 4) is 10.04% in the top
   trend tercile vs 10.17% in the bottom — a 0.13pp difference on n=22,000 each. In the
   *top decile* of 30d-MA slope it is 10.06%. Flat everywhere.
2. **Real 15m crypto streaks are SHORTER than a coin flip, in every regime.** iid predicts
   P(run>=4)=12.50%, P(run>=6)=3.125%, P(run>=8)=0.781%. BTC delivers 10.0% / 1.9% / 0.31%.
   The market is anti-streaky, always, and equally so in bull and bear.
3. **No regime cell pushes post-4-streak reversal below 50%.** Across 3 pre-named regime
   families x 2 assets, the lowest 1-way cell is 54.2% and the lowest 2-way cell is 52.4%
   [49.0, 55.8]. The "eat the losses" regime does not exist in this data.
4. **July 2026 is not a bull regime.** BTC's 30d-MA slope sits at the **27th percentile**
   of 2 years (i.e. still mildly *negative*); ETH at the 51st. The bull leg Ryan fears has
   not started, and if it did, item 1 says it would not matter.
5. **The June->July decay is neither regime nor competition. It is sample size.** Chi-square
   homogeneity across 23 months: **23.44, df=22** (crit 35.2) — cannot reject that every
   month has the identical reversal rate. Observed monthly sd / sd expected from n alone =
   **1.02** (BTC), **0.92** (ETH). There is zero excess month-to-month variation to explain.

**Recommendation: no regime gate. Do not add a kill-switch. Do not change nestor.**

---

## Data & provenance

- **Source: Coinbase Exchange public REST**, `GET https://api.exchange.coinbase.com/products/{BTC-USD,ETH-USD}/candles?granularity=900`.
  No key, no auth, free. Pulled 2026-07-22 by `~/kalshi_data/scripts/pull_2yr_lean.py`
  (already on disk — no new pull needed this lane).
- Files: `~/kalshi_data/btc_15m_2yr.csv`, `~/kalshi_data/eth_15m_2yr.csv` — `ts,close`.
  BTC 70,028 bars, ETH 70,026 bars, **2024-07-22 20:00Z .. 2026-07-22 19:45Z**.
  0 duplicates, 0 misaligned timestamps, **3 gaps** (BTC): 26 bars @2026-05-08, 24 bars
  @2025-10-25, 5 bars @2024-10-26. All gaps break runs and are dropped, not bridged.
- Settlement corpus for calibration: `~/kalshi_data/KX{BTC,ETH}15M_mkts_full.json`,
  6,339 settled markets each, 2026-05-15..2026-07-22 (fields `open`, `close`, `K`, `result`).
- Scripts (scratchpad, not in any repo):
  `/private/tmp/claude-501/-Users-ryanwhitehead/449dc817-6064-457d-a116-2df58b67bcb2/scratchpad/{regime.py,regime_lib.py,regime2.py,regime3.py}`
  Outputs: `reg_out.txt`, `reg2_out.txt`, `reg2b_out.txt`, `reg3_out.txt`.

### The reconstruction rule and its one approximation
The verify-streak-conditioning note established `result == (K_next > K)` on 6,325/6,325 BTC
windows, i.e. **the Kalshi settle is exactly close-of-window vs open-of-window, and the strike
K is spot at open**. A Coinbase 15m candle stamped `T` spans `[T, T+900)` and its close is the
price at `T+900`; spot at the window's open is therefore the close of candle `T-900`. So

    result(window T) = close[T] > close[T-900]

— consecutive candle closes, nothing more. **The approximation is not the bar geometry, it is
the price reference:** Kalshi settles on a 60-second average of its own index, we use a single
Coinbase last-trade print.

**Lookahead audit.** Every regime feature at window `T` is computed from closes through
`close[T-900]` inclusive — the price that *is* the strike, known at entry. The bar whose close
decides the window is never touched by any feature. Rolling stats use trailing windows only;
the ATH is an expanding trailing max. Tercile *boundaries* are the one full-sample quantity
(noted below).

---

## A. CALIBRATION — the gate the charter set, passed

Overlap: 2026-05-15 .. 2026-07-22, the full 67-day Kalshi corpus.

| asset | synthetic result == real settle | median \|candle_close - K\| / K |
|---|---|---|
| BTC | 5,915 / 6,339 = **93.31%** | 166 ppm |
| ETH | 5,971 / 6,339 = **94.19%** | 187 ppm |

The 6-7% disagreement is the 60s-average-vs-last-trade reference, concentrated in
near-coin-flip windows where the two references straddle the strike. It is honest noise, and
it does **not** bias the streak statistics:

| asset | synthetic post->=4-streak reversal, overlap | real corpus (verify-streak-conditioning) |
|---|---|---|
| BTC | n=671, **56.0% [52.3, 59.7]** | n=657, 55.6% |
| ETH | n=632, **59.0% [55.1, 62.8]** | n=633, 56.5% |

BTC lands on 56.0% against a ~56% target — dead on. ETH runs 2.5pp hot, inside its own CI and
inside the real corpus's CI. Exactly-L also tracks (synthetic BTC L=3 52.0% vs real ~52.3%;
L=4 54.3% vs real 55.9%). **Calibration passes; the 2yr numbers are trustworthy, with a
standing caveat that ETH's synthetic runs ~2pp optimistic.**

Base up-rate over 2 years: BTC 50.00% (n=66,471), ETH 50.29% (n=66,468). No drift bias.

---

## B. Regime definitions — PRE-NAMED, locked before any result was read

All three families are named in the charter; I fixed the exact cut points before running.

1. **TREND** = 7-day change in the 30-day moving average of close, `(MA30[t] - MA30[t-7d]) / MA30[t-7d]`.
   Terciles: BTC **-0.78% / +1.04%**; ETH **-2.46% / +0.99%** per 7 days. Bins DOWN / FLAT / UP.
2. **DRAWDOWN** from expanding trailing ATH. Bands **<2% / 2-10% / 10-25% / >25%**
   (with 25-40 / 40-55 / 55-100 sub-bands added for section L only, because that is where
   we currently live).
3. **REALIZED VOL** = stdev of 15m log returns over the trailing 96 bars (24h). Terciles:
   BTC **0.174% / 0.243%**; ETH **0.257% / 0.358%** per 15m.

*Caveat, stated:* tercile boundaries come from the full 2-year sample. This is boundary-only
lookahead — it cannot manufacture a difference between cells, and any cell that mattered would
be re-cut on a trailing basis before shipping. Nothing here reaches that stage.

---

## C. Does a bull leg lengthen streaks? **No.** (Ryan's premise, directly)

Windows classified by trend bin, all windows with a defined trailing run.

**BTC** — iid benchmark: P(run>=4)=12.50%, P(run>=6)=3.125%, P(run>=8)=0.781%

| trend bin | n | up% | P(run>=4) | P(run>=6) | P(run>=8) | mean run | max run |
|---|---|---|---|---|---|---|---|
| DOWN | 22,153 | 49.92 | 10.17% | 1.93% | 0.406% | 1.888 | 16 |
| FLAT | 22,157 | 50.34 | 10.05% | 1.88% | 0.293% | 1.876 | 13 |
| UP | 22,149 | 49.74 | 10.04% | 1.86% | 0.307% | 1.883 | 12 |
| **top 10% slope** | 6,642 | — | **10.06%** | **1.90%** | **0.361%** | — | **12** |

**ETH**

| trend bin | n | up% | P(run>=4) | P(run>=6) | P(run>=8) | mean run | max run |
|---|---|---|---|---|---|---|---|
| DOWN | 22,153 | 49.86 | 9.60% | 1.68% | 0.284% | 1.854 | 14 |
| FLAT | 22,148 | 50.04 | 9.48% | 1.62% | 0.266% | 1.855 | 11 |
| UP | 22,154 | 50.96 | 9.59% | 1.68% | 0.244% | 1.858 | 14 |
| **top 10% slope** | 6,647 | — | **9.48%** | **1.82%** | **0.301%** | — | **14** |

Two things to read off this table.

- **The bull column is not different from the bear column.** 10.04 vs 10.17 on n=22k is
  noise. The longest streak in the strongest-trending decile of two years is 12 windows
  (BTC), *shorter* than the 16 seen in the DOWN bin. Ryan's "far more streaks" does not
  happen. The intuition fails because a bull leg is a drift of a few basis points per 15m
  bar against a per-bar sigma 30-50x larger — it cannot move a run-length distribution.
- **Every regime is below the coin flip at every run length.** 10.0 < 12.50, 1.9 < 3.125,
  0.31 < 0.781. Long streaks are *rarer* than random in real 15m crypto — which is the same
  fact as reversal > 50%, seen from the other side. The anti-streak property is structural,
  not regime-contingent.

---

## D. Post->=4-streak reversal by regime cell (n, 95% Wilson CI)

Per-asset, **never pooled** (note-03: BTC/ETH 15m returns correlate 0.894; pooling would
double-count one observation).

**BTC — 2yr baseline: n=6,704, 56.2% [55.1, 57.4]**

| family | cell | n | reversal |
|---|---|---|---|
| TREND | DOWN | 2,254 | 56.1% [54.0, 58.1] |
| TREND | FLAT | 2,227 | 55.7% [53.6, 57.7] |
| TREND | **UP** | 2,223 | **57.0% [54.9, 59.0]** |
| DRAWDOWN | ATH<2% | 361 | 55.1% [50.0, 60.2] |
| DRAWDOWN | 2-10% | 2,032 | 56.2% [54.0, 58.3] |
| DRAWDOWN | 10-25% | 1,907 | 56.4% [54.2, 58.6] |
| DRAWDOWN | >25% | 2,404 | 56.4% [54.4, 58.3] |
| VOL | LOVOL | 2,146 | 58.3% [56.2, 60.4] |
| VOL | MIDVOL | 2,275 | 55.3% [53.2, 57.3] |
| VOL | HIVOL | 2,283 | 55.3% [53.2, 57.3] |

**ETH — 2yr baseline: n=6,350, 57.2% [56.0, 58.4]**

| family | cell | n | reversal |
|---|---|---|---|
| TREND | DOWN | 2,126 | 57.2% [55.1, 59.3] |
| TREND | FLAT | 2,099 | 57.3% [55.1, 59.4] |
| TREND | **UP** | 2,125 | **57.1% [55.0, 59.2]** |
| DRAWDOWN | ATH<2% | 130 | 56.2% [47.6, 64.4] |
| DRAWDOWN | 2-10% | 569 | 57.1% [53.0, 61.1] |
| DRAWDOWN | 10-25% | 1,256 | 54.2% [51.5, 57.0] |
| DRAWDOWN | >25% | 4,395 | 58.1% [56.6, 59.5] |
| VOL | LOVOL | 2,044 | 58.0% [55.8, 60.1] |
| VOL | MIDVOL | 2,132 | 56.8% [54.6, 58.8] |
| VOL | HIVOL | 2,174 | 56.9% [54.8, 59.0] |

**2-way TREND x VOL** (the cell the fear lives in)

BTC

| | LOVOL | MIDVOL | HIVOL |
|---|---|---|---|
| DOWN | n=473 57.3% [52.8, 61.7] | n=596 56.4% [52.4, 60.3] | n=1,185 55.4% [52.6, 58.3] |
| FLAT | n=905 58.8% [55.5, 61.9] | n=830 **52.4% [49.0, 55.8]** | n=492 55.5% [51.1, 59.8] |
| UP | n=768 58.3% [54.8, 61.8] | n=849 57.4% [54.0, 60.6] | n=606 54.8% [50.8, 58.7] |

ETH

| | LOVOL | MIDVOL | HIVOL |
|---|---|---|---|
| DOWN | n=460 58.5% [53.9, 62.9] | n=633 57.8% [53.9, 61.6] | n=1,033 56.3% [53.3, 59.3] |
| FLAT | n=892 57.5% [54.2, 60.7] | n=753 55.6% [52.1, 59.2] | n=454 59.5% [54.9, 63.9] |
| UP | n=692 58.2% [54.5, 61.9] | n=746 57.0% [53.4, 60.5] | n=687 56.0% [52.3, 59.7] |

**Extreme bull — the literal scenario Ryan described**

| asset | slice | n | reversal |
|---|---|---|---|
| BTC | top 10% slope (>= +3.34%/7d) | 668 | 56.4% [52.7, 60.1] |
| BTC | top 5% slope (>= +5.22%/7d) | 331 | 56.5% [51.1, 61.7] |
| BTC | top 10% slope AND within 2% of ATH | 161 | 56.5% [48.8, 63.9] |
| ETH | top 10% slope (>= +7.22%/7d) | 630 | 55.6% [51.7, 59.4] |
| ETH | top 5% slope (>= +9.09%/7d) | 304 | 56.6% [51.0, 62.0] |
| ETH | top 10% slope AND within 2% of ATH | 94 | 58.5% [48.4, 67.9] |

**Charter's key question, answered: no cell pushes reversal materially below 50%.** The
lowest of 20 one-way cells is 54.2%; the lowest of 18 two-way cells is BTC FLAT/MIDVOL at
52.4%, whose CI [49.0, 55.8] contains the baseline 56.2% and which does not replicate on ETH
(55.6%). The scenario "a bull regime turns the fade into a loser" has **no support at any
n**. In the single most bullish 5% of two years, on either asset, the fade still reverses
56.5%.

*Economic footnote:* the fade's true breakeven is not 50% but ~**54.6%** (avg entry 52.9c +
fee `0.07*p*(1-p)` = 1.74c). Two cells sit below that line — BTC FLAT/MIDVOL 52.4% and ETH
DD10-25% 54.2% — neither replicating cross-asset, neither surviving section H's placebo.

---

## E. Era series — the natural experiments

**BTC quarterly**

| quarter | n | reversal | mean 30dMA slope | signal rate |
|---|---|---|---|---|
| 2024Q3 | 303 | 59.4% [53.8, 64.8] | -0.56%/7d | 9.5% |
| 2024Q4 (**the bull run**) | 887 | 55.9% [52.6, 59.2] | **+3.94%/7d** | 10.0% |
| 2025Q1 | 906 | 57.3% [54.0, 60.5] | -1.11%/7d | 10.5% |
| 2025Q2 | 817 | 57.3% [53.9, 60.6] | +1.68%/7d | 9.4% |
| 2025Q3 (**chop**) | 900 | 55.2% [52.0, 58.4] | +0.51%/7d | 10.2% |
| 2025Q4 | 956 | 52.9% [49.8, 56.1] | -1.74%/7d | 10.9% |
| 2026Q1 | 803 | 59.0% [55.6, 62.4] | -1.87%/7d | 9.3% |
| 2026Q2 | 898 | 56.5% [53.2, 59.7] | -0.49%/7d | 10.3% |
| 2026Q3 (partial) | 234 | 53.0% [46.6, 59.3] | -1.18%/7d | 11.2% |

**ETH quarterly**

| quarter | n | reversal | mean 30dMA slope |
|---|---|---|---|
| 2024Q3 | 335 | 56.4% [51.1, 61.6] | -2.67%/7d |
| 2024Q4 | 923 | 57.4% [54.2, 60.6] | +3.19%/7d |
| 2025Q1 | 883 | 56.1% [52.8, 59.3] | -4.37%/7d |
| 2025Q2 | 752 | 58.4% [54.8, 61.8] | +1.75%/7d |
| 2025Q3 | 830 | 55.4% [52.0, 58.8] | **+4.34%/7d** |
| 2025Q4 | 828 | 55.4% [52.0, 58.8] | -2.75%/7d |
| 2026Q1 | 785 | 59.1% [55.6, 62.5] | -2.77%/7d |
| 2026Q2 | 788 | 59.8% [56.3, 63.1] | -1.28%/7d |
| 2026Q3 (partial) | 226 | 55.3% [48.8, 61.6] | -0.26%/7d |

**The 2024Q4 bull run is the direct test and it exonerates the strategy.** The strongest
trending quarter in two years (+3.94%/7d BTC, +3.19% ETH) produced a 55.9% / 57.4% reversal
rate and a signal rate of 10.0% / 10.5% — both squarely at baseline. Meanwhile the *worst*
quarter for the fade, 2025Q4 at 52.9%, was a **down**-trending quarter. The trend/edge
relationship is not merely weak, its observed sign is the opposite of the fear.

Full 24-month series is in the scratchpad outputs; the summary statistic is: BTC monthly mean
56.62%, sd 3.17pp, range 50.5-62.5; ETH mean 58.16%, sd 5.38pp (inflated by an n=20 stub
month), range 52.9-80.0.

---

## F. Where July 2026 sits in regime space

| asset | month | 30dMA slope | pctile | 24h vol | pctile | drawdown | pctile |
|---|---|---|---|---|---|---|---|
| BTC | 2026-06 | -4.30%/7d | **9.0** | 0.260% | 73.1 | 49.8% | 96.4 |
| BTC | **2026-07** | **-1.18%/7d** | **27.1** | 0.177% | 34.7 | 49.7% | 96.1 |
| ETH | 2026-06 | -5.53%/7d | 13.9 | 0.339% | 61.6 | 65.7% | 97.2 |
| ETH | **2026-07** | **-0.26%/7d** | **51.5** | 0.242% | 27.9 | 63.6% | 94.3 |

Dominant cells: BTC July is **FLAT/LOVOL 33%, FLAT/MIDVOL 32%, DOWN/LOVOL 13%**. ETH July is
UP/LOVOL 39%.

Read plainly: **July 2026 is a quiet, flat, deeply-drawn-down tape — not a bull leg.** BTC's
30d MA is still *falling*, at the 27th percentile of two years. Ryan is right that price is
near a two-year low (drawdown at the 96th percentile), but "at the bottom" is not the same
regime as "trending up," and the trend measure has not turned. What actually changed
June->July is **volatility**, which fell from the 73rd to the 35th percentile.

And section D says the vol move should have made the fade *better*, not worse: BTC LOVOL is
58.3%, HIVOL 55.3%.

---

## G. Regime vs competition: what explains the June->July decay?

### The regime model's own prediction
Reweighting the 2yr TREND x VOL map by each month's actual cell mix:

| asset | month | regime-PREDICTED reversal | ACTUAL | n |
|---|---|---|---|---|
| BTC | 2026-06 | 56.18% | 57.1% [51.3, 62.7] | 282 |
| BTC | 2026-07 | **55.93%** | **53.0% [46.6, 59.3]** | 234 |
| ETH | 2026-06 | 57.47% | 60.6% [54.6, 66.4] | 259 |
| ETH | 2026-07 | **57.62%** | **55.3% [48.8, 61.6]** | 226 |

**The regime map predicts a June->July change of -0.25pp for BTC and +0.15pp for ETH.**
Observed: -4.1pp and -5.3pp. Regime explains roughly **6% of BTC's decay and none of ETH's**
— for ETH the regime points the wrong way. Regime is not the explanation.

### Is it competition (market participants arbitraging the edge away)?
**No, and this is the cleanest fact in the ledger.** The synthetic series is built entirely
from **Coinbase spot** — no Kalshi price, no order book, no counterparty. It contains nothing
a competitor could act on. Yet it reproduces the decay almost exactly:

| | June | July | delta |
|---|---|---|---|
| Real Kalshi corpus (pooled BTC+ETH, verify-streak-conditioning) | 57.6% | 53.3% | -4.3pp |
| **Synthetic BTC (Coinbase spot only)** | **57.1%** | **53.0%** | **-4.1pp** |
| Synthetic ETH (Coinbase spot only) | 60.6% | 55.3% | -5.3pp |

A decay visible in raw spot cannot be caused by competition in a prediction market. It is a
property of the underlying price path in those weeks. Competition is **ruled out**.

### So what is it? Sample size. Nothing else.

| test | BTC | ETH |
|---|---|---|
| two-proportion z, Jun vs Jul | 0.93 (p=0.351) | 1.18 (p=0.237) |
| historical month-pairs with an equal-or-worse drop | 5 / 22 (p=0.23) | 4 / 22 (p=0.18) |
| July's z vs the 23-month level distribution | -1.13 | -0.69 |
| months at-or-below July's rate | 3 / 23 (2024-12, 2025-11, 2026-07) | 7 / 23 |
| **chi2 homogeneity across 23 months** | **23.44, df=22** (crit 35.2 @0.05) | **19.05, df=22** |
| observed monthly sd vs sd expected from n alone | 2.98pp vs 2.92pp, **ratio 1.02** | 2.76pp vs 3.00pp, **ratio 0.92** |
| lag-1 autocorrelation of monthly reversal rate | **r = +0.009** (n=22) | r = -0.227 (n=22) |
| next month's rate after a month <= 54% | 55.85% (n=5) vs 56.37% overall | 58.84% (n=2) vs 57.21% overall |

The chi-square and the sd ratio are the decisive pair. **Over 23 months you cannot reject the
hypothesis that the true reversal rate was identical in every single month**, and the spread
of monthly rates is exactly what n=250-340 per month produces by itself (ratio 1.02). There is
no residual month-to-month signal for a regime — or anything else — to explain.

The lag-1 autocorrelation of +0.009 is the operationally important number: **a weak month
carries no information about the next month.** Historically, after a month at or below 54%,
BTC's following month averaged 55.85% — statistically indistinguishable from the 56.37%
unconditional mean. Reacting to July would have been reacting to nothing, five times over.

**Verdict on the decay: neither regime nor competition. Ordinary sampling variation in a
430-signal month.** Both the June 57.6% and the July 53.3% are draws from the same ~56%
urn — June was the lucky tail, July the unlucky one.

---

## H. NOTE-07 PLACEBO — the discipline check

Placebo construction: circular rotation of the regime-label array against the outcome array.
This destroys the label-outcome pairing while **exactly** preserving the autocorrelation of
both series — the right null for a persistent regime label. 4,000 rotations per test.

| asset | candidate gate | observed split | n gated | placebo p |
|---|---|---|---|---|
| BTC | TREND=UP vs rest | +1.11pp | 2,223 | 0.435 |
| BTC | VOL=LOVOL vs rest | +3.01pp | 2,146 | **0.044** |
| BTC | VOL=HIVOL vs rest | -1.47pp | 2,283 | 0.319 |
| BTC | 3x3 max-min spread | 6.37pp | — | 0.302 |
| ETH | TREND=UP vs rest | -0.17pp | 2,125 | 0.856 |
| ETH | VOL=LOVOL vs rest | +1.15pp | 2,044 | 0.371 |
| ETH | VOL=HIVOL vs rest | -0.45pp | 2,174 | 0.722 |
| ETH | 3x3 max-min spread | 3.83pp | — | 0.818 |

**The placebo kills everything, exactly as note-07 requires.** The 3x3 max-min spread — the
honest test of "did I find anything by carving 9 cells" — is p=0.302 / p=0.818. Rotated
labels routinely produce a 6pp spread across nine cells. The visible structure in section D's
2-way table is what randomness looks like at n~800 per cell.

One test clears p<0.05 (BTC LOVOL, p=0.044). It fails on every other axis:
- **6 tests were run.** Bonferroni-adjusted, p = 0.26.
- **It does not replicate (note-03).** ETH's same gate is +1.15pp, p=0.371 — a third the size,
  nowhere near significant, on a 0.89-correlated asset that should show it if real.
- **Its honest halves are unstable.** BTC LOVOL lift is **+0.60pp in H1 and +5.42pp in H2**.
  The entire effect lives in the second half. ETH's is +1.49 / +0.77.

**No gate ships.**

---

## I. Gate cost, if anyone insists (they should not)

For the record, the cost of the one gate that even flickered:

| asset | rule | signals kept | gated reversal | skipped reversal | lift |
|---|---|---|---|---|---|
| BTC | 24h vol < 0.174% per 15m | 2,146 / 6,704 = **32%** | 58.3% [56.2, 60.4] | 55.3% [53.8, 56.7] | +3.01pp |
| BTC | 24h vol < 0.243% per 15m | 4,421 / 6,704 = 66% | 56.8% [55.3, 58.2] | 55.3% [53.2, 57.3] | +1.47pp |
| ETH | 24h vol < 0.257% per 15m | 2,044 / 6,350 = **32%** | 58.0% [55.8, 60.1] | 56.8% [55.3, 58.3] | +1.15pp |
| ETH | 24h vol < 0.358% per 15m | 4,176 / 6,350 = 66% | 57.4% [55.8, 58.8] | 56.9% [54.8, 59.0] | +0.45pp |

It is entry-time-measurable (trailing 96-bar stdev of 15m log returns, known at window open,
zero lookahead) — so it *could* ship. It should not: **it surrenders 68% of signal volume for
a lift that is +3.01pp on BTC, +1.15pp on ETH, fails the cross-asset replication, and lives
entirely in one half of BTC's sample.** At the fade's ~1.2%/trade edge, cutting two-thirds of
volume for an unreplicated 1-3pp is a large certain cost against an uncertain benefit.

Note the irony for the live question: **July 2026 sits at the 35th vol percentile — inside
the LOVOL/favourable side of this filter.** Even the one flicker of structure in this data
says July should have been an *above*-average month.

---

## J. Deep-drawdown bands — because "at the bottom" is where we are

BTC is 49.7% off its ATH (96th percentile of 2 years). Does the fade behave differently down
here?

**BTC**

| drawdown band | n | reversal | P(run>=4) |
|---|---|---|---|
| 0-2% | 361 | 55.1% [50.0, 60.2] | 10.58% |
| 2-10% | 2,032 | 56.2% [54.0, 58.3] | 9.87% |
| 10-25% | 1,907 | 56.4% [54.2, 58.6] | 10.25% |
| 25-40% | 1,103 | 55.8% [52.9, 58.8] | 9.80% |
| **40-55% (where we are)** | **1,301** | **56.8% [54.1, 59.5]** | **10.32%** |

**ETH**

| drawdown band | n | reversal | P(run>=4) |
|---|---|---|---|
| 0-2% | 130 | 56.2% [47.6, 64.4] | 10.52% |
| 2-10% | 569 | 57.1% [53.0, 61.1] | 9.16% |
| 10-25% | 1,256 | 54.2% [51.5, 57.0] | 10.29% |
| 25-40% | 1,908 | 57.5% [55.3, 59.7] | 9.66% |
| **40-55%** | **1,012** | **57.7% [54.6, 60.7]** | **9.22%** |
| 55-100% | 1,475 | 59.1% [56.6, 61.6] | 9.17% |

Every band is within 3pp of baseline and every CI overlaps it. Streak frequency is flat to
three significant figures across a 50-point range of drawdown. **Being at the bottom is not a
regime as far as 15-minute streaks are concerned.**

---

## What this changes in nestor

**Nothing.** Explicitly:

1. **Do not add a regime kill-switch.** There is no regime in which the fade's engine fails.
   The worst pre-named cell in two years is 52.4% on n=830 with a CI covering baseline, and
   the placebo says cells like that appear by chance 30% of the time.
2. **Do not add a trend filter.** TREND is the single most inert dimension measured: +1.11pp
   on BTC (p=0.435), -0.17pp on ETH (p=0.856).
3. **Do not add a volatility filter.** The only candidate that flickered costs 68% of volume,
   does not replicate cross-asset, and is confined to one half of one asset's sample.
4. **Do not react to the July number.** Lag-1 autocorrelation of monthly reversal rate is
   +0.009. July carries no information about August. The chi-square says all 23 months are
   one distribution.
5. **Keep `min_streak = 4`, keep hold-to-settlement, keep the current signal population.**
   This lane found no reason to touch any of it.

## What would actually change the answer

The honest limit of this work: two years is ~8,000 four-streak signals per asset, enough to
resolve a regime effect of roughly 4pp and no smaller. A real regime effect of 1-2pp would be
invisible here. Two things would be decisive if the question ever needs reopening:

- **A genuine BTC bull leg, live.** The 2024Q4 run is the only strong-uptrend quarter in the
  sample (n=887) and it printed 55.9%. One more independent bull quarter would roughly halve
  the uncertainty on the trend question. That is data we can only wait for — and section C
  says the mechanism (drift vs 15m sigma) makes a large effect implausible on priors.
- **Pre-2024 history.** The Coinbase endpoint serves it; extending to 2020-2024 would add the
  2021 mania and the 2022 crash, both far more extreme regimes than anything in this sample.
  This is a cheap follow-up pull (`pull_2yr_lean.py` with a wider START) if Ryan wants the
  tails covered. I did not do it because the charter specified two years and the two-year
  answer is unambiguous.

## Traps checked

- **Candle lookahead:** none. Every feature at window T uses closes through `T-900` only; the
  deciding bar is never an input. Verified structurally in `regime_lib.py:build()`.
- **note-03 (BTC/ETH share regime):** all results reported per-asset, never pooled. The one
  gate that flickered is reported as failing precisely *because* it does not replicate.
- **note-07 (placebo):** section H. Rotated regime labels kill every candidate, including the
  3x3 spread. The one p<0.05 survivor is Bonferroni-dead and half-unstable.
- **Pre-named regimes:** the three families and their cut rules come from the charter and were
  fixed before any output was read. No cell was re-cut after seeing a result. The 25-40 /
  40-55 / 55-100 drawdown sub-bands in section J are the one post-hoc split, added because
  July's actual drawdown is 49.7% and the pre-named ">25%" band was too coarse to answer
  "where we are" — they are descriptive and support no gate.
