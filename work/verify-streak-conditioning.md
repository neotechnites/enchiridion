# VERIFY: streak conditioning batch (2026-07-26) — 4 cells, real backtests

Charter: `work/steer-streak-conditioning.md` (cells 1-3) + coordinator addenda (cell 4,
streak-length distribution, run-quality tells). No nestor changes, no subagents,
Mac-on-disk data only.

## Bottom line

| cell | question | verdict |
|---|---|---|
| 1 | unconditional first-min cheap-side buying | **DEAD**, -10.7%/tr BTC, -16.0%/tr ETH, n=3,148 |
| 2 | BTC-context gate on ETH fades (+ run-quality tells) | **NOT GATE-WORTHY** — hypothesis sign falsified; best tell fails cross-asset replication |
| 3 | reversal rate by streak length | **FLAT** — chi2=1.81 df=4; bow-out-at-N adds nothing |
| 4 | mid-window take-profit exit | **DEAD** — marks are calibrated-to-cheap; selling loses the friction |

**Nothing here changes a nestor parameter.** The one directional finding worth a future
lane is the *reverse* of Ryan's cell-2 hypothesis (see cell 2).

---

## Data & provenance

- `~/kalshi_data/{SERIES}_mkts_full.json` — 6,339 settled 15m markets per coin,
  2026-05-15 .. 2026-07-22 (67.1d), 13 gaps, 0 dups. **This is the settlement corpus for
  streak reconstruction** (all 5 coins).
- Price paths: `{S}_virgin.jsonl.gz` (per-second trade bars, last ~27d),
  `KXBTC15M_fullpaths.jsonl.gz`, `all_ticks.jsonl.gz`. First-minute price coverage:
  BTC 2,559/6,339, ETH 2,156/6,339 (the virgin pull is capped at the last 3,000 trades,
  so early-window coverage is partial and concentrated in the last 27 days).
- Polymarket BTC-5m: `poly_btc5m_full2.json`, 57,516 markets / 216d.
- Loaders reused: `~/kalshi_data/scripts/streak_cheap.py` (fee model `0.07*p*(1-p)`,
  `open_price`, 4-streak fade construction), `pull_alt_virgin.py` record format,
  `poly_streaklen.py` (contiguity-checked run reconstruction).
- Scripts written this run (scratchpad, not in any repo):
  `/private/tmp/claude-501/-Users-ryanwhitehead/449dc817-6064-457d-a116-2df58b67bcb2/scratchpad/{streak_cond,cell2,cell2b,cell4,cell4b,addenda}.py`

**Sanity check passed:** `result == (K_next > K)` for 6,325/6,325 BTC (100.00%) and
6,319/6,325 ETH (99.91%). So the strike series K *is* spot-at-open, and it can be used as
a lookahead-free spot series (K at window open is set at open = known at entry). This is
what cells 2/addenda use instead of candles — no 60s lag question arises.

---

## CELL 1 — unconditional first-minute cheap-side calibration: **DEAD**

Rule: last trade in the first 60s; buy the cheap side (`min(p, 100-p)`); hold to settle.
Breakeven win rate = entry + fee.

**KXBTC15M** (n=2,559 priced windows)

| bucket | n | avg entry | win | 95% CI | breakeven | EV |
|---|---|---|---|---|---|---|
| <=25 | 149 | 21.2c | 18.8% | [13.3, 25.8] | 22.3% | -3.53c (-16.7%/tr) |
| 26-35 | 514 | 31.4c | 28.2% | [24.5, 32.3] | 33.0% | -4.74c (-15.1%/tr) |
| 36-44 | 1,057 | 40.4c | 38.6% | [35.7, 41.6] | 42.0% | -3.44c (-8.5%/tr) |
| 45-49 | 761 | 47.0c | 46.1% | [42.6, 49.7] | 48.8% | -2.63c (-5.6%/tr) |
| **ALL <=44** | **1,720** | **36.0c** | **33.8%** | **[31.6, 36.0]** | **37.6%** | **-3.84c (-10.7%/tr)** |

**KXETH15M** (n=2,156 priced windows)

| bucket | n | avg entry | win | 95% CI | breakeven | EV |
|---|---|---|---|---|---|---|
| <=25 | 145 | 20.4c | 17.9% | [12.5, 25.0] | 21.5% | -3.61c (-17.7%/tr) |
| 26-35 | 405 | 31.3c | 25.2% | [21.2, 29.6] | 32.8% | -7.66c (-24.4%/tr) |
| 36-44 | 878 | 40.4c | 36.9% | [33.8, 40.1] | 42.1% | -5.21c (-12.9%/tr) |
| 45-49 | 657 | 47.0c | 41.9% | [38.1, 45.7] | 48.8% | -6.93c (-14.7%/tr) |
| **ALL <=44** | **1,428** | **35.8c** | **31.7%** | **[29.3, 34.1]** | **37.4%** | **-5.74c (-16.0%/tr)** |

Splits (within the priced subset): BTC H1 -16.7%/tr, H2 -4.7%/tr. ETH H1 -15.2%/tr,
H2 -16.8%/tr. **Every bucket, every asset, every half loses.** Upper CI never reaches
breakeven in any <=44 bucket. Vault expectation confirmed and now measured
*first-minute-specific*: n=3,148, no bucket is even close.

Contrast — conditional on a prior 4-streak (the strategy's own population): BTC ALL<=44
n=179 win 38.0% vs breakeven 38.0% (EV +0.02c); ETH n=131 win 34.4% vs 37.8%
(EV -3.46c). So the streak condition lifts BTC to *exactly breakeven* and leaves ETH
negative. The 4-streak edge does not live in cheap prices.

Strategy population (fade side at first minute, any price): BTC n=278 entry 53.4c win
55.8% EV +0.66c (+1.2%/tr); ETH n=206 entry 52.1c win 54.4% EV +0.67c (+1.3%/tr). That
is where the edge is, and it is thin.

**The mirror (flagged, NOT claimed).** Buying the *expensive* side is the exact
complement: BTC n=1,720 entry 64.0c win 66.2% [64.0, 68.4] vs breakeven 65.6%,
EV +0.67c (+1.1%/tr); ETH n=1,428 entry 64.2c win 68.3% [65.9, 70.7] vs breakeven 65.8%,
EV +2.59c (+4.0%/tr). ETH's lower CI clears breakeven by 0.1pp. **Do not act on this.**
It is (a) the mirror of an already-dead vault result (favorite-buying, n≈4,700), (b) a
hypothesis I formed *after* seeing the cheap side fail, on the same n, (c) entirely
inside the 27-day priced window so the "halves" are one regime, and (d) priced at last
*trade*, not at the offer — crossing 1-2c of spread eats the whole +0.7c/+2.6c. Labelled
UNVERIFIED. If anyone wants it, the decisive test is a book-based executable-ask backtest,
not more of this tape.

---

## CELL 2 — cross-asset / run-quality gates: **NOT GATE-WORTHY** (hypothesis sign falsified)

### The stated hypothesis is untestable as posed
BTC/ETH per-15m-window return correlation = **0.894** (n=6,325). A sign-agreement gate is
therefore degenerate: 626/633 ETH 4-streak signals (99%) have BTC "confirming". This is
the same structural fact that killed the original BTC-confirm gate (note 15) — it was
never a stale-quote problem alone.

So the gate was rebuilt on vol-normalized magnitudes: `z = (log move over the L streak
windows) / (trailing-96-window stdev * sqrt(L))`, signed so +ve = in the streak's
direction. Trailing vol window ends *before* the streak starts. No lookahead.

### The three tells (ETH n=626 / BTC n=651, baseline reversal 56.5% / 55.6%)

| tell | ETH<-BTC split | placebo p | BTC<-ETH split | placebo p | replicates? |
|---|---|---|---|---|---|
| T1 \|z_cross\| >= 33rd pct (not-quiet context) | +6.2pp | 0.137 | +5.3pp | 0.210 | sign yes, sig no |
| T2a streak quality, min \|margin\|/vol, top tercile | +4.2pp | 0.348 | **+9.8pp** | **0.015** | **no** (ETH non-monotone) |
| T2b streak quality, median \|margin\|/vol, top tercile | -2.3pp | 0.610 | **+9.9pp** | **0.019** | **no** (ETH sign flips) |
| T3 cross-coin breadth >= 2 of 5 | +5.1pp | 0.275 | +4.5pp | 0.320 | sign yes, sig no |
| T3 breadth >= 3 | +2.0pp | 0.622 | +6.2pp | 0.118 | weak |
| COMBINED (not-quiet AND blowout) | +3.0pp | 0.460 | +5.3pp | 0.169 | no |

Placebo = 4,000 reps shuffling the tell across signals; two-sided p on the split.

**Direction: the hypothesis is backwards.** Ryan's prior was *BTC-driven streak =
information, fade loses; idiosyncratic = fish, fade wins*. Measured, on both assets:

- ETH: idiosyncratic (rho = z_ctx/z_own bottom tercile) reversal **52.4%**; BTC-driven
  (top tercile) **60.3%**.
- Quiet-context tercile is the *worst* bucket on both coins: ETH 52.4% [45.6, 59.1],
  BTC 52.1% [45.4, 58.6]; priced EV in that bucket ETH -3.90c, BTC -7.99c.
- BTC's quality tell points the same way: blowout margins reverse **62.1%** vs squeaker
  54.8% vs mid 49.8%.
- BTC's breadth is monotone: breadth 1 → 52.3%, 2 → 52.8%, 3 → 55.6%, 4 → 59.5%,
  5 → 62.2%.

Coherent story if real: a 4-streak riding a *big, broad, high-margin* move is an
overextension that mean-reverts; a 4-streak that is a quiet idiosyncratic grind is real
drift and continues. That is the opposite of the hot-hand framing.

**Why it still fails the bar.** Twelve tests were run (6 tells x 2 assets). The only two
that clear p<0.05 are BTC's quality tells; Bonferroni-adjusted that is p≈0.18, and they do
*not* replicate on ETH (T2b sign flips). T1 and T3 have the right sign on both assets but
neither reaches p<0.10 on either. Honest halves are also unstable: ETH's T1 gate is
entirely H2 (H1 +/-0: 58.6% vs 59.2%; H2: 58.7% vs 45.7%). BTC/ETH are 0.89-correlated
so pooling is illegitimate (note 03) — the pooled +5.0pp on n=1,277 is reported only for
completeness, not as evidence.

**Verdict: no gate ships.** Directionally suggestive, statistically absent.

---

## CELL 3 — reversal rate by streak length: **FLAT**

Exactly-L trailing run, gap-broken chains dropped. Null under iid: reversal after a run of
L = 1 - p_up, flat in L. Base up-rates ~49.3-49.8% for all coins.

| L | BTC n | BTC rev [CI] | ETH n | ETH rev [CI] | pooled n | pooled rev [CI] |
|---|---|---|---|---|---|---|
| 3 | 758 | 51.7% [48.2, 55.3] | 762 | 52.9% [49.3, 56.4] | — | — |
| 4 | 365 | 55.9% [50.8, 60.9] | 357 | 56.6% [51.4, 61.6] | 722 | 56.2% [52.6, 59.8] |
| 5 | 161 | 55.3% [47.6, 62.7] | 155 | 54.8% [47.0, 62.5] | 316 | 55.1% [49.6, 60.5] |
| 6 | 72 | 55.6% [44.1, 66.5] | 70 | 64.3% [52.6, 74.5] | 142 | 59.9% [51.6, 67.6] |
| 7 | 32 | 53.1% [36.4, 69.1] | 25 | 48.0% [30.0, 66.5] | 57 | 50.9% [38.3, 63.4] |
| 8+ | 27 | 55.6% [37.3, 72.4] | 26 | 50.0% [32.1, 67.9] | 53 | 52.8% [39.7, 65.6] |

SOL/XRP/DOGE (correlated, reported per-asset not pooled): L=4 reversal 55.5% / 53.0% /
53.7%; no monotone decay in any of them either.

**Flatness test:** pooled BTC+ETH >=4, n=1,290, reversal 56.0%. chi2 across L=4..8+ =
**1.81, df=4** (crit 9.49 @ 0.05) → **cannot reject flat.** Expected n collapse is real
(n=53 at 8+), so this is "no evidence of decay", not "proof of flatness" — but it is also
no evidence of *persistence* of the edge at long L.

**Bow-out simulation** (pooled, reversal rate only, no pricing):

| rule | n | reversal | CI |
|---|---|---|---|
| trade L in [4,4] | 722 | 56.2% | [52.6, 59.8] |
| trade L in [4,5] | 1,038 | 55.9% | [52.8, 58.9] |
| trade L in [4,6] | 1,180 | 56.4% | [53.5, 59.2] |
| trade L in [4,7] | 1,237 | 56.1% | [53.3, 58.8] |
| trade L in [4,inf) (current) | 1,290 | 56.0% | [53.2, 58.7] |

A bow-out-at-N rule buys 0.2pp of win rate for a 44% cut in volume. **It burns edge; do
not add it.**

The threshold itself is validated: L=3 pools to ~52.3%, L=4 to 56.2%. The edge appears at
4, not before.

**Regime note (the real caveat).** Pooled >=4: H1 58.1% [54.3, 61.9] vs H2 53.8%
[49.9, 57.6]. Monthly: 2026-05 56.8% (n=296), 2026-06 57.6% (n=564), **2026-07 53.3%
(n=430)**. CIs overlap, but the edge is measurably weaker in the most recent month. This
is the number to watch, not streak length.

### Addendum — full run-length distribution and MAX streak observed

Kalshi 15m, 6,339 windows/coin, 67 days (counts are *exactly-L* completed runs):

| coin | **max run** | when (UTC) | dir | L1 | L2 | L3 | L4 | L5 | L6 | L7 | L8 | L9 | L10 | L11 | L12 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| KXBTC15M | **11** (2h45m) | 07-02 00:30 | down | 1673 | 864 | 394 | 205 | 89 | 40 | 17 | 8 | 4 | 1 | 2 | – |
| **KXETH15M** | **15** (3h45m) | 06-15 13:15 | **up** | 1700 | 856 | 405 | 204 | 85 | 45 | 12 | 8 | 3 | – | 1 | – |
| KXSOL15M | 12 | 06-16 08:45 | up | 1697 | 798 | 401 | 214 | 94 | 42 | 16 | 10 | 4 | 1 | 2 | 1 |
| KXXRP15M | 12 | 07-12 00:30 | down | 1586 | 806 | 393 | 214 | 101 | 42 | 26 | 12 | 6 | – | 1 | 1 |
| KXDOGE15M | 12 | 07-12 00:30 | down | 1710 | 861 | 410 | 189 | 95 | 36 | 21 | 5 | 3 | 1 | – | 1 |

Survivor counts (windows whose trailing run is >= L — i.e. how often the fade rule would
still be firing):

| coin | >=3 | >=4 | >=5 | >=6 | >=7 | >=8 | >=9 | >=10 | >=11 |
|---|---|---|---|---|---|---|---|---|---|
| KXBTC15M | 1415 | 657 | 292 | 131 | 59 | 27 | 12 | 5 | 2 |
| KXETH15M | 1395 | 633 | 276 | 121 | 51 | 26 | 13 | 8 | 6 |
| KXSOL15M | 1479 | 696 | 314 | 144 | 68 | 34 | 16 | 8 | 4 |
| KXXRP15M | 1552 | 756 | 354 | 166 | 79 | 33 | 13 | 5 | 3 |
| KXDOGE15M | 1389 | 628 | 278 | 116 | 50 | 19 | 9 | 4 | 2 |

**Headline: ETH's longest observed run in 67 days is 15 consecutive 15-minute windows
(3h45m), on 2026-06-15. BTC's is 11 (2h45m), 2026-07-02.** A 6-streak is a ~1-in-52 event
per window (121/6,339) and ETH reached >=6 on 121 occasions; three consecutive fade losses
inside one run is entirely ordinary — ETH went >=8 on 26 occasions.

Polymarket BTC 5-minute, 57,516 markets / 216 days (12x5m == 1h == Kalshi 4x15m):
max run **15 windows (75 min)**, 2026-03-24 07:30Z, up.
Distribution: L1:14829 L2:7295 L3:3765 L4:1764 L5:850 L6:407 L7:222 L8:88 L9:36 L10:21
L11:9 L12:9 L13:2 L14:1 L15:1.
Cross-venue reversal check: exactly-4 (20 min) n=3,409 rev **51.7% [50.0, 53.4]**;
exactly-8 (40 min) n=167 rev 52.7% [45.1, 60.1]; >=12 (60 min, the Kalshi-equivalent
horizon) n=20 rev 65.0% [43.3, 81.9] — too thin to say anything. Also flat, and notably
*weaker* than Kalshi at the same nominal L, consistent with the effect being horizon-
dependent rather than count-dependent.

---

## CELL 4 — mid-window take-profit exit: **DEAD**

### Data problem, stated plainly
The charter's route (kbt 100ms books) does **not** work. `kbt_books_*.jsonl` covers only
~2.5 days (182 ETH windows, 2026-07-24..26), and worse, its `yes_best_bid`/`no_best_bid`
fields are stale high-water marks: they rise monotonically through a window and end at
`yes_bid=0.999, no_bid=0.992` (sum 1.99). `capture_kbt.py:compact()` discards the ladder
prices, so no spread can be recovered. **These are exactly the stale quotes of note 15 and
must not be used as sell prices.** If a real book-based exit study is ever wanted, what is
missing is a per-snapshot *ladder* capture (or top-of-book with timestamps) over the
streak-fade population — nothing on disk has it.

### What was used instead
Executable exits reconstructed from the trade tape's taker side, which is real executed
data: on Kalshi a `taker_side=yes` print is the YES **ask** (taker lifted the offer) and a
`taker_side=no` print is the YES **bid**. Selling a YES position fills at the last
taker-no print; selling a NO position fills at `100 - ` the last taker-yes print. Prints
older than 120s are treated as unavailable. Settlement is fee-free; every exit pays
`0.07*p*(1-p)`.

Population: 484 4+streak fades with a first-minute print and a full tape (BTC 278,
ETH 206). Hold-all baseline: **+0.66c/position** (win 55.2%, entry 52.9c).

### B) Mark calibration — this is the answer
Our side's mark at time-to-close Y vs realized settlement of our side. `edge = realized -
mark`; positive = our side is *underpriced*, holding wins.

| TTC | bucket | n | avg mark | realized | 95% CI | edge |
|---|---|---|---|---|---|---|
| 5 min | 0-20 | 131 | 6.6c | 6.1% | [3.1, 11.6] | -0.5pp |
| 5 min | 20-40 | 43 | 28.6c | 30.2% | [18.6, 45.1] | +1.7pp |
| 5 min | 40-60 | 67 | 50.3c | 47.8% | [36.3, 59.5] | -2.5pp |
| 5 min | 60-80 | 53 | 69.5c | 71.7% | [58.4, 82.0] | +2.2pp |
| 5 min | 80-90 | 50 | 85.3c | 80.0% | [67.0, 88.8] | -5.3pp |
| 5 min | 90-100 | 140 | 96.0c | 97.1% | [92.9, 98.9] | +1.1pp |
| 8 min | 80-90 | 69 | 84.7c | 89.9% | [80.5, 95.0] | **+5.1pp** |
| 8 min | 90-100 | 71 | 93.9c | 95.8% | [88.3, 98.6] | +1.9pp |
| 2 min | 90-100 | 213 | 98.4c | 99.1% | [96.6, 99.7] | +0.6pp |

Conditional on the actual trigger (a big favorable move):

| condition | TTC | n | avg mark | realized | 95% CI | edge |
|---|---|---|---|---|---|---|
| mark >= entry+30 | 2 min | 196 | 96.9c | 98.5% | [95.6, 99.5] | +1.5pp |
| mark >= entry+30 | 5 min | 136 | 93.1c | 92.6% | [87.0, 96.0] | -0.4pp |
| mark >= entry+30 | 8 min | 85 | 86.4c | 87.1% | [78.3, 92.6] | +0.7pp |
| mark >= entry+20 | 2 min | 239 | 96.2c | 97.1% | [94.1, 98.6] | +0.9pp |
| mark >= entry+20 | 5 min | 201 | 91.3c | 89.6% | [84.6, 93.1] | -1.7pp |
| mark >= entry+20 | 8 min | 159 | 85.2c | 89.3% | [83.5, 93.2] | **+4.1pp** |

**Every edge is inside its own CI, and the sign is if anything positive.** After a sharp
favorable move the crowd does **not** overprice our side — the marks are calibrated, and
at 8 minutes out they slightly *under*price the lead (+4.1pp / +5.1pp), which is the LOCK
prior (b) showing up exactly where it was predicted. The hot-hand-overpricing premise is
falsified. With calibrated marks, structural prior (a) is decisive: holding is fee-free,
exiting is not, so holding dominates by the friction.

### C) Rule grid (9 cells; note-07 — the best cell is not evidence)
Per-position P&L over all 484, vs hold, at increasing extra crossing haircut H:

| X | Y | H=0 | H=1 | H=2 | H=3 | #fired |
|---|---|---|---|---|---|---|
| 20c | 2m | -0.11c | -0.87c | -1.63c | -2.40c | 358 (74%) |
| 20c | 5m | **+1.31c** | +0.62c | -0.07c | -0.75c | 323 (67%) |
| 20c | 8m | +0.23c | -0.33c | -0.90c | -1.47c | 267 (55%) |
| 30c | 2m | -1.59c | -2.13c | -2.68c | -3.22c | 254 (52%) |
| 30c | 5m | -1.01c | -1.43c | -1.84c | -2.25c | 192 (40%) |
| 30c | 8m | -0.90c | -1.17c | -1.45c | -1.72c | 128 (26%) |
| 40c | 2m | -0.14c | -0.46c | -0.79c | -1.11c | 150 (31%) |
| 40c | 5m | -0.31c | -0.52c | -0.73c | -0.94c | 97 (20%) |
| 40c | 8m | -0.12c | -0.21c | -0.30c | -0.39c | 41 (8%) |

**1 of 9 cells is positive, and it dies at a 2c haircut.** Its own statistics kill it:
fired n=323, delta +1.96c, se 2.42, **t = 0.81**. Splits: BTC +2.25c (se 3.20), ETH +1.60c
(se 3.68), H1 +0.97c (se 3.34), H2 +2.95c (se 3.49) — all indistinguishable from zero. It
is non-monotone in X (positive at 20c, -1.0c at 30c, -0.3c at 40c), which is the signature
of a grid artifact, not a surface. The frictionless upper bound confirms: sell-at-mark
with zero cost gives +4.29c at X=20 but **-0.76c at X=30 and +0.63c at X=40** — the marks
themselves are not systematically rich at any threshold.

Measured friction: median crossing cost from the tape is ~0.0-0.2c (these books are
tight); the binding cost is the exit fee, 0.35c at 2-5min out rising to 1.6c at 8-15min.
Note the tape-reconstructed spread understates true crossing (median ask-bid = 0.0c
because consecutive taker-yes/taker-no prints often land on the same cent), so the H=0
column is an optimistic bound and the H=1-2c columns are the honest ones.

**Verdict: DEAD.** Tonight's +90% mark at T-5min was a fairly-priced position, not a
selling opportunity. Round-tripping to a coin flip is what a calibrated 90c mark does 10%
of the time.

---

## What each cell would change IF adopted (parameters only)

1. **Cell 1** — nothing. It would *forbid* a price-only entry rule: no unconditional
   `price <= 44` buy trigger on any 15m crypto window. If anything it argues the fade
   should not be widened to cheap entries (BTC fade at entry<=44 is +4.2%/tr on n=61,
   ETH is **-4.2%/tr** on n=52 — no signal either way).
2. **Cell 2** — nothing ships. The candidate parameter, if the bar were lowered, would be
   `min_cross_z = 0.85` (ETH) / `0.76` (BTC) — skip fades where the cross-coin move over
   the streak span is under ~0.8 sigma. That removes 33% of signals for a claimed +5 to
   +6pp. It fails placebo (p=0.14/0.21) and ETH's honest split. **Recommend: no change.**
   The reverse-direction finding is a lane, not a parameter.
3. **Cell 3** — nothing. Explicitly *reject* a `max_streak_len` / bow-out-at-N parameter:
   at N=6 it cuts 9% of volume for +0.4pp, at N=4 it cuts 44% for +0.2pp. Keep
   `min_streak = 4` (validated: L=3 is 52.3%, L=4 is 56.2%). The only parameter worth
   discussing is a regime kill-switch on the July decay (53.3%, n=430), which is a
   different lane.
4. **Cell 4** — nothing. Explicitly reject a take-profit parameter. If someone insists on
   one, the only cell that was ever positive is `X=20c, Y>=5min` and it is t=0.81 and
   negative at 2c of slippage. **Hold to settlement.**

## Loose ends / what is missing

- Real executable-ask data for the cell-1 mirror (favorite side) and for cell 4's exits.
  The kbt capture would need to store ladder prices, not just aggregate depth.
- First-minute price coverage is 27 days deep, not 67 — cell 1's "halves" are two halves
  of one regime.
- The July reversal-rate decay (57.6% June → 53.3% July, n=430) is the highest-value
  unanswered question this batch surfaced and is not something these three cells test.
