# VERIFY: mid-window fair-value dislocation scan (2026-07-26)

Charter: `work/steer-midwindow-fv.md`. Priors: `work/verify-streak-conditioning.md`, note 13
(graveyard: LOCK decay, "book reprices in 1.9s", strike-knowledge-at-second-0 priced by the
opening ask), note 07 discipline. Mac-on-disk only, no nestor changes, no subagents.

## Bottom line — **DEAD, with a decisive number**

A calibrated fair-value model over the full 15m window was built, honestly split, and scanned
against executable mid-window prices on 3 assets / 94k window-minutes.

**The market's own mid-window mark is a strictly better probability than the model at every
minute of the window, and the optimal weight on the model in a blend is exactly 0.00 out of
sample on all three assets.** Every dislocation threshold, every minute band, every asset, both
entry conventions: **-1.1c to -2.9c per trade**. Not one positive cell survives a 2c crossing
haircut with a stable era split.

This is not "we own a better number at T0 generalised to the window." At T0 the strike is set
mechanically and we know something. At minute 2 onward, we know nothing the tape does not.

---

## Data & provenance (all on-disk, no network)

| what | file | coverage |
|---|---|---|
| settlement corpus | `~/kalshi_data/{S}_mkts_full.json` | 6,339 windows/coin, 2026-05-15..07-22 |
| mid-window market price | `KXBTC15M_fullpaths.jsonl.gz` (n=2,101, 100% minute-coverage), `{S}_virgin.jsonl.gz` (BTC 2,541 / ETH 2,563 / SOL 2,571) | 2026-06-25..07-22 |
| mid-window spot | `bitstamp_btcusd_1min.csv`, `eth_1min_full2.csv`, `sol_1min_full2.csv` | 2026-04-15..07-22, 1-min |

Tape record format (`pull_alt_virgin.py`): `secs = [[sec_offset, last_px, taker_yes_count, taker_no_count], ...]`.
A `taker_yes` print is the YES **ask**; a `taker_no` print is the YES **bid** (cell-4 convention).

**Candle-lag audit (the 60s lookahead trap), done first.** Compared each candle timestamp offset
against `K` (= Kalshi index at window open, proven lookahead-free by the conditioning lane):

| offset | BTC/bitstamp | BTC/full2 | ETH | SOL |
|---|---|---|---|---|
| t-120 | 1.57 bps | 1.67 | 1.82 | 2.32 |
| **t-60** | **1.29** | 1.66 | 1.87 | 2.32 |
| t+0 | 2.90 | 3.20 | 4.04 | 5.04 |
| t+60 | 4.06 | 4.24 | 5.45 | 6.79 |

(median |candle − K| in bps, n=6,339). So the CSV rows are `(bar_open_ts, close_px)` and the bar
timestamped `T-60` closes at `T`. **I used `C[T-120]` throughout** — the bar that closed a full
60s *before* the decision instant. Every spot input is therefore stale by 60-120s, i.e. strictly
conservative; there is no candle lookahead anywhere in this ledger.

**Basis cancellation.** Instead of feeding raw candle price, the model uses
`r_m = ln(C[open+60m-120] / C[open-120])` and treats the index as `K·exp(r_m)`. The 1.3-2.3 bps
venue basis between Bitstamp/Coinbase and the Kalshi index cancels out of the ratio; only the
15-minute drift in basis survives, which is second-order to a 19-28 bps σ.

Scripts (scratchpad, not in any repo):
`/private/tmp/claude-501/-Users-ryanwhitehead/449dc817-6064-457d-a116-2df58b67bcb2/scratchpad/{fv_lib,align,calib,mktcal,fit_k,scan,mktgrid,harden,final}.py`

---

## Model spec

For window with strike `K` (= index at open) and settlement `1[index_close > K]`:

```
sigma      = stdev of the last 96 completed 15m log-returns of the K series,
             window ends strictly BEFORE this window opens        (lookahead-free)
r_m        = ln( C[open + 60m - 120] / C[open - 120] )            (60s-lagged candles)
tau        = (900 - 60m) / 900
z          = r_m / (sigma * sqrt(tau))
P(yes)     = Phi( k_m * z )
```

`k_m` is a **single per-minute calibration scalar** fit by log-loss on windows with
`open < 2026-06-25` (n≈3,725/asset) — i.e. on the 41 days that carry **no tape at all**, so the
entire 27-day dislocation scan is out of sample. Fourteen parameters, one per minute, nothing
else fitted.

Fitted `k_m` (cal set, three assets independently):

| m | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| BTC | 0.38 | 0.78 | 0.84 | 0.84 | 0.82 | 0.84 | 0.90 | 0.96 | 0.98 | 1.00 | 1.08 | 1.12 | 1.06 | 0.90 |
| ETH | 0.34 | 0.74 | 0.86 | 0.94 | 0.90 | 0.88 | 0.90 | 0.98 | 0.98 | 1.06 | 1.08 | 1.10 | 1.06 | 1.06 |
| SOL | 0.50 | 0.80 | 0.90 | 0.92 | 0.90 | 0.90 | 0.92 | 1.04 | 1.02 | 1.08 | 1.12 | 1.12 | 0.88 | 1.08 |

Real, replicated, and tiny: driftless GBM on trailing vol is ~15% over-confident early
(vol clusters up after a fast move) and ~10% under-confident at m10-13, and `m=1` is junk
because a 120s-lagged candle carries almost no in-window information. Log-loss improvement of
the whole calibration over plain `k=1` is 0.0005–0.0020 nats. **The term structure is a fact;
it is not money.**

---

## Model calibration table (raw Gaussian, full 67-day corpus, n≈6,290 windows/asset)

BTC, model-P decile → realized settle rate (the shape is identical on ETH and SOL):

| model P | m1-3 real | m4-7 real | m8-11 real | m12-14 real |
|---|---|---|---|---|
| 0-10% | 19.4 (n=160) | 11.5 (n=1,318) | 5.8 (n=3,461) | 3.2 (n=5,238) |
| 10-20% | 25.4 | 15.6 | 11.8 | 10.1 |
| 20-30% | 26.6 | 22.8 | 17.6 | 16.3 |
| 30-40% | 36.0 | 31.6 | 29.7 | 28.4 |
| 40-50% | 45.1 | 41.9 | 41.8 | 42.7 |
| 50-60% | 53.4 | 56.1 | 57.3 | 59.3 |
| 60-70% | 62.8 | 68.4 | 69.9 | 74.8 |
| 70-80% | 69.2 | 76.6 | 82.3 | 82.4 |
| 80-90% | 76.6 | 84.8 | 88.2 | 90.0 |
| 90-100% | 80.9 (n=188) | 89.0 (n=1,256) | 95.2 (n=3,283) | 97.5 (n=4,908) |

Median trailing σ per 15m window: **BTC 18.9 bps, ETH 25.7 bps, SOL 28.4 bps.**

### The decisive comparison: model vs the market's own mark

Same bins, restricted to the tape period, adding the market's last trade. Error columns are
`realized − forecast` in pp. BTC:

| band | model P | model | mkt | real | mkt_err | mdl_err |
|---|---|---|---|---|---|---|
| m9-11 | 70-80% | 75.0 | 81.2 | 82.8 (n=512) | **+1.6** | +7.8 |
| m9-11 | 80-90% | 84.9 | 89.1 | 90.1 (n=573) | **+0.9** | +5.1 |
| m12-14 | 60-70% | 65.0 | 74.8 | 73.7 (n=411) | **−1.1** | +8.8 |
| m12-14 | 70-80% | 75.1 | 84.2 | 84.3 (n=428) | **+0.1** | +9.2 |
| m12-14 | 90-100% | 98.0 | 97.7 | 97.8 (n=1,979) | **+0.1** | −0.2 |
| m5-7 | 30-40% | 35.2 | 33.2 | 34.9 (n=762) | **+1.7** | −0.3 |
| m5-7 | 80-90% | 84.9 | 82.8 | 89.4 (n=426) | +6.6 | +4.5 |
| m2-3 | 70-80% | 74.2 | 69.2 | 76.4 (n=216) | +7.2 | +2.2 |

Across all 40 (minute-band × decile) cells on all 3 assets, from m5 onward `|mkt_err|` is
typically <2.5pp with no consistent sign, while `|mdl_err|` runs 5-11pp. **Formally (OOS test
period, Brier ×100, lower better):**

| | n | model | market | const 0.5 |
|---|---|---|---|---|
| KXBTC15M | 29,304 | 16.03 | **13.38** | 25.0 |
| KXETH15M | 31,815 | 15.92 | **13.16** | 25.0 |
| KXSOL15M | 32,703 | 16.02 | **13.49** | 25.0 |

Per minute band (BTC / ETH / SOL, model vs market): m2-4 22.6/22.0/22.0 vs 20.9/19.9/20.1 ·
m5-7 19.4/18.8/18.7 vs 17.3/16.4/16.6 · m8-11 14.7/14.6/14.3 vs 12.1/11.9/11.8 ·
m12-14 9.4/9.3/9.2 vs 5.4/5.4/5.4. **The market wins at every minute of the window, and its
margin widens as expiry approaches** — the opposite of what a "the crowd gets sloppy late"
thesis needs.

### The blend test — the number that closes the lane

`p = w·model + (1−w)·market`, `w` chosen by Brier on tape-half-1 (Jun 25–Jul 6), evaluated on
tape-half-2 (Jul 6–22):

| asset | w* fit on H1 | H2 blend | H2 market | H2 model |
|---|---|---|---|---|
| KXBTC15M | **0.00** | 13.965 | 13.965 | 16.634 |
| KXETH15M | **0.00** | 13.268 | 13.268 | 15.971 |
| KXSOL15M | **0.00** | 13.471 | 13.471 | 15.909 |

The optimiser puts **zero** weight on the model, independently on three assets. The model adds
no information to the price. Everything below is confirmation.

---

## Dislocation frequency, size, duration — net of costs

**Size distribution of `|calibrated model − market last|`** (test period, m2-14):

| asset | n | p50 | p75 | p90 | ≥5c | ≥10c | ≥20c | ≥30c |
|---|---|---|---|---|---|---|---|---|
| BTC | 29,304 | 8.6c | 16.9c | 26.0c | 64.7% | 44.9% | 18.6% | 6.6% |
| ETH | 31,815 | 8.9c | 16.8c | 25.0c | 66.1% | 46.0% | 17.7% | 5.7% |
| SOL | 32,703 | 8.3c | 16.2c | 25.0c | 64.1% | 43.9% | 16.9% | 5.9% |

Apparent "dislocations" are enormous and constant — ~65% of all window-minutes show a ≥5c gap.
That is the tell: a real edge is rare, a broken model is everywhere.

**Duration / information content.** For every gap ≥5c (mean |gap| = 15.1-15.3c), how far does
the market subsequently move *toward* the model?

| asset | n | +120s | +240s |
|---|---|---|---|
| BTC | 15,108 | +0.37c (t=+2.6) | +0.16c (t=+0.7) |
| ETH | 17,073 | +0.27c (t=+2.0) | −0.29c (t=−1.5) |
| SOL | 17,296 | +0.58c (t=+4.3) | +0.17c (t=+0.8) |

A 15c gap converges by **0.3-0.6c in two minutes and stops**. Information content ≈ **2-4% of
the nominal gap**, against a 2-3c round-trip cost. There is no dislocation that closes; there
is a model that is wrong by 15c and right by 0.4c of it.

**Executable backtest.** Decision at `t = 60m`, m=2..14. Buy whichever side the model prefers.
Entry crosses the spread two ways: (a) *takerside* — YES at the last `taker_yes` print, NO at
`100 −` the last `taker_no` print, both ≤20s old; (b) *cross1* — last print ±1c. Fee
`0.07·p(1−p)` on entry, settlement fee-free, hold to settle.

takerside, 20s staleness cap:

| gate | BTC | ETH | SOL |
|---|---|---|---|
| edge ≥0c | n=27,089 −1.13c (t=−5.1) | n=28,367 −2.09c (t=−9.7) | n=26,243 −2.02c (t=−8.8) |
| edge ≥2c | n=21,782 −1.32c | n=23,153 −2.52c | n=21,448 −2.16c |
| edge ≥4c | n=18,755 −1.33c | n=20,126 −2.71c | n=18,404 −2.30c |
| edge ≥6c | n=16,269 −1.43c | n=17,423 −2.68c | n=15,910 −2.21c |
| edge ≥8c | n=14,001 −1.31c | n=15,026 −2.75c | n=13,564 −2.25c |
| edge ≥12c | n=10,125 −1.41c | n=10,792 −2.91c | n= 9,632 −2.47c |

By minute band at the ≥4c gate (BTC / ETH / SOL): m2-4 −0.65 / −3.98 / −3.00 ·
m5-7 −1.74 / −2.60 / −1.80 · m8-11 −1.24 / −2.35 / −2.13 · m12-14 −1.78 / −1.99 / −2.32.
The `cross1` convention is uniformly ~0.8c worse (BTC −1.9 to −2.4c, ETH −2.7 to −3.5c,
SOL −2.3 to −3.0c). Loosening staleness to 60s changes nothing (≤0.06c).

**Twenty-four gate/asset cells, zero positive.** Average entry price on the flagged trades is
24-31c — the model's disagreements systematically point at the *cheap* side, so the scan
independently re-derives cell 1's dead unconditional cheap-side buy at −1.1 to −2.9c/trade on
n=81,699. That is a coherence check on both lanes, not a new finding.

**Measured spread, mid-window** (tape-reconstructed ask−bid, 20s window, p50/p75/p90):
BTC 0.0/0.0/0.0-0.1 · ETH 0.0/1.0/2.0-3.0 · SOL 0.8-1.0/1.0-2.0/4.0. As cell 4 flagged, the
tape method understates crossing (consecutive taker-yes/taker-no prints land on the same cent),
so the takerside column is an **optimistic** bound and the `cross1`/`H≥1c` numbers are honest.

---

## Gate hunt (note 15 obligation — a conditional kill is not a kill)

Two regions of the surface were positive at zero haircut. Both were hunted to the ground.

### Gate A — early emergent favorite (m2-3, favorite at 80-90c)
Mechanism candidate: a fast 2-minute move creates a favorite the tape has not fully repriced.
This is the only candidate that is *not* LOCK-shaped (it lives at the front of the window).

| asset | H=0c | H=1c | H=2c | H1 (H=0) | H2 (H=0) |
|---|---|---|---|---|---|
| BTC (n=367) | +3.16 (t=1.82) | +2.21 (t=1.27) | +1.25 (t=0.72) | +4.01 (t=1.38) | +2.71 (t=1.25) |
| ETH (n=417) | +2.86 (t=1.78) | +1.91 (t=1.19) | +0.96 (t=0.60) | +2.73 (t=1.13) | +2.97 (t=1.38) |
| SOL (n=441) | +2.91 (t=1.85) | +1.96 (t=1.25) | +1.01 (t=0.64) | +1.51 (t=0.63) | +4.17 (t=2.02) |

Sign replicates on 3 assets, magnitude is stable, and it is **still not evidence**: it is 3 of
90 grid cells (expect ~7 cells past |t|=1.8 by chance at this grid size), no cell reaches
t=2 on its own, it dies to t≤0.7 at a 2c haircut, and the assets are 0.89-correlated so this is
one observation, not three. Note 13 already recorded the adjacent kill: *"the early-drift signal
is real (69.4% conditional) but the opening ask prices it exactly — negative after fees at every
entry second."* Same signal, two minutes later, same verdict. **Not gate-worthy.**

### Gate B — mid-window confident favorite (m4-9, favorite 80-96c) = **this is LOCK**
Reported for the graveyard check the charter demanded, **not as a finding**:

| asset | H=0c | H=1c | H=2c | H1 (H=0) | H2 (H=0) |
|---|---|---|---|---|---|
| BTC (n=4,309) | +1.32 (t=2.92) | +0.38 | −0.57 | +2.14 | +0.97 |
| ETH (n=5,017) | +2.01 (t=4.96) | +1.06 | +0.12 | +3.00 | +1.31 |
| SOL (n=4,629) | +0.45 (t=1.00) | −0.50 | −1.44 | −0.88 | +1.41 |

Buy-the-confident-side-late, decaying era over era on both coins that show it (BTC 2.14→0.97,
ETH 3.00→1.31 across a two-week split), inverting on SOL, negative at a 2c haircut on 2 of 3.
This is the LOCK edge and its documented decay (note 13: strict lock +1.72c first 6 weeks →
**−1.07c last 4 weeks**, flip rate 1.2%→4.1%, adverse selection). **Independently reproduced
here, on different data, in the same direction — and explicitly NOT claimed as new.** Nothing in
this lane resurrects it; if anything the era split is one more decay datapoint.

Other gates tested and empty: staleness cap (20s vs 60s), entry convention (takerside vs ±1c vs
±2c), minute band (4 bands × 3 assets), edge threshold (6 levels), price band (6 levels),
model-P decile (10 levels). No conditioning variable produces a cell that is positive with a
stable era split at a realistic haircut.

---

## What each finding would change in nestor

**Nothing. Zero parameters.** No mid-window entry rule, no fair-value overlay, no take-profit or
add-on trigger. The one durable output is a doctrine line: *the 15m crypto book is a better
probability estimator than any model we can build from free spot + trailing vol, at every minute
of the window, and its advantage grows toward expiry.* Any future crypto-15m edge must come from
information the tape does not have — not from re-pricing what it already prices.

## What capture is missing (if anyone wants to re-open this)

1. **A real top-of-book ladder time series on the 15m crypto series.** Everything here prices
   off the trade tape because `kbt_books_*.jsonl` covers 2.5 days and stores stale high-water
   `yes_best_bid`/`no_best_bid` with the ladder discarded (`capture_kbt.py:compact()`). Without
   real quotes, the takerside column is an optimistic bound. This is the *same* missing
   capability cell 4 flagged — a second lane has now been forced into the same bound. If books
   are ever captured properly, gate A (m2-3, favorite 80-90c) is the one cell worth re-running,
   and it needs ~4× the n to matter.
2. **Sub-minute spot.** All spot here is 60-120s stale by construction. Free 1s/tick spot over
   the corpus would sharpen `r_m` at m2-4 — but note 13's 100ms burst study already showed the
   book reprices in 1.9s and a poller loses 82-86% of races, so this would buy calibration, not
   a race.
3. It would **not** be worth re-running with a fancier model (jump-diffusion, GARCH, implied
   vol). The blend test already granted the model its best possible weight against the market
   and the answer was 0.00. A better model has to beat a mark that is already unbiased to
   ~1pp — the ceiling is the spread, and the spread is the cost.
