# VERIFY-ARCTIC-WINGS — deep verification of ARCTIC ICE WINGS (2026-07-27)

Target: `work/lane-MUSK-jul27.md` idea **M5** — `KXARCTICICEMIN-26OCT01` deep-wing sale.
Ryan's stated rule: *"Buy NO on any rung the satellite regression puts under ~3% probability,
when NO is offered under ~88¢, hold to September settlement."*
Read first: note 07, [[33 The Mesh]] (§BENTER two-factor law, §graveyard, §API field quirk),
note 15 §THE REAL GOAL, note 40 §bankroll, `work/verify-tail-book.md` (the precedent kill).

## VERDICT: **DEAD**

Not one kill but three, each independently sufficient, in ascending order of finality:

1. **The trigger never fires.** The "~3%" is a small-sample-Gaussian artifact. Honest range on the
   deepest rung (T3.6) is **4.1% – 10.5%**. No rung on the live ladder is under 3% under any
   defensible estimator. The rule as written has no legal entry.
2. **The named fish does not exist.** Across the whole 9-rung ladder the tape is **580.00 contracts
   taken on the NO side vs 1.43 on the YES side.** There is no tail-fearing YES buyer. 100% of the
   open interest was manufactured by NO takers hitting a maker's resting YES-bid ladder — i.e. the
   trade the lane proposes has *already been done*, 580 contracts of it, by someone else, and the
   maker has repriced down 3¢ while it happened.
3. **n=1, forever.** The Arctic minimum is annual. This edge cannot accrue a sample. Graveyard rule
   ("one-event edges = PROMISING never TRADE") applies permanently, not conditionally.

The capital math is the fourth kill and it is the same one that shelved the tail-book: **100% of a
$93.09 bankroll, locked 67–72 days, for +0.12 to +0.21 %/day.**

---

## 1. PRIMARY SOURCE — re-derived independently, and the lane reproduces (with one error)

Pull: `curl -A '<Chrome UA>' https://noaadata.apps.nsidc.org/NOAA/G02135/north/daily/data/N_seaice_extent_daily_v4.0.csv`
→ **HTTP 200, 1,882,434 bytes, 15,790 data rows, 1978-10-26 → 2026-07-26.** (The lane's "curl with a
browser UA; WebFetch 500s" tooling note in note 33 / lane-MUSK §4 is correct and still true today.)

**2026-07-26 extent = 6.787** ✓ (lane exact). Jul 20–26 mean = 7.022.

| quantity | lane M5 | re-derived | note |
|---|---|---|---|
| Jul-26 2026 extent | 6.787 | **6.787** ✓ | primary CSV |
| OLS intercept | −0.390 | **−0.178** | ✗ differs |
| OLS slope | 0.685 | **0.654** (se 0.241) | ✗ differs |
| point forecast 2026 | 4.258 | **4.264** | ✓ to 6/1000 |
| residual sd | 0.340 | **0.367** (n−2) / 0.347 (n divisor) | lane used the **n divisor** |
| market-implied μ, σ | 4.22, 0.334 | **4.226, 0.356** (centre-rung fit) | ✓ close |

The lane's forecast is right; its **error bar is not**. Two compounding understatements:
- divisor: 0.340 vs 0.367 — the lane divided SSE by n, not n−2;
- **no parameter uncertainty at all.** The honest predictive sd is
  `s·√(1 + 1/n + (x₀−x̄)²/Sxx)` = **0.382**, and with n=19 the right reference law is t₁₇, not normal.

That correction alone doubles the deepest tail: **P(min<3.6) 0.025 → 0.050.**

---

## 2. IS 3% REAL? — no. Four estimators, one table

All computed at the live rungs, 2007–2025 fit (n=19), predictor = 2026 Jul-26 = 6.787.

| rung | lane N(pred, .34) | honest t₁₇(pred, .382) | LOO-empirical | melt-drop analog | **market mid** |
|---|---|---|---|---|---|
| **T3.6** | 0.025 | **0.050** | 1/19 = **0.053** | 2/19 = **0.105** | 0.215 |
| **T3.8** | 0.086 | **0.120** | 2/19 = 0.105 | 4/19 = **0.211** | 0.285 |
| **T4.0** | 0.219 | **0.249** | 4/19 = 0.211 | 5/19 = **0.263** | 0.380 |
| T4.2 | 0.425 | 0.434 | 7/19 = 0.368 | 7/19 = 0.368 | 0.475 |

**The two model families disagree by 2× and the disagreement is the whole trade.**

- **OLS-with-shrinkage** (the lane's): slope 0.654 < 1 pulls a low July state *up* toward the mean.
- **Additive melt-drop** (min = Jul26 − drop, i.e. slope ≡ 1): 2007-25 drop mean 2.613, sd 0.377 →
  point forecast **4.174**, ~0.09 lower, and the drop distribution's left tail is empirically fat.

**Which slope is right? Not 0.654.** Fit-era table (same CSV, same target):

| fit window | n | slope b | se(b) | resid sd | 2026 point forecast |
|---|---|---|---|---|---|
| 2007–2025 (lane) | 19 | **0.654** | 0.241 | 0.367 | 4.264 |
| 2013–2025 | 13 | 0.608 | 0.245 | 0.300 | 4.352 |
| 2000–2012 | 13 | 1.149 | 0.193 | 0.463 | 4.046 |
| 1996–2025 | 30 | **1.021** | 0.094 | 0.411 | 4.227 |
| 1979–2025 | 42 | **1.069** | 0.060 | 0.375 | 4.226 |

The full-record slope is **1.069 ± 0.060** — additive, and it rejects 0.654 by >6 se. The 2007-2025
slope of 0.65 is **range restriction**: the modern-era Jul-26 predictor spans only 6.24–7.75 (1.5)
vs 6.2–9.3 over the full record, and attenuation from restricted range plus noise-in-x biases the
slope toward zero. The shrinkage that makes the forecast look safe is a fit artifact, not physics.
Era-to-era the *central* forecast alone swings **4.046 → 4.352** (0.31 = 0.8σ) with no new data.

### The out-of-sample record (note-07's gold standard)

**Leave-one-out**, fit on the other 18 modern years, all 19 predicted:

```
2007 −0.19  2008 −0.47  2009 +0.46  2010 −0.03  2011 +0.06  2012 −1.02  2013 +0.56
2014 +0.37  2015 −0.24  2016 −0.38  2017 +0.15  2018 +0.13  2019 +0.04  2020 −0.17
2021 +0.50  2022 +0.12  2023 −0.43  2024 +0.10  2025 +0.30
```
LOO RMSE **0.382** vs the lane's claimed 0.340 — a 12% understated error even before eras.

**True out-of-period** (note 07's actual bar — a different era, not a within-window split):
fit 1979–2006 (a=−1.218, b=0.869, resid sd 0.346) → predict 2007–2025:
**mean error −0.470, RMSE 0.589 against a claimed 0.346.** A model calibrated on one era is biased
half a σ low on the next and its true error is 1.7× its advertised error. This is the single most
important number in the file: **the satellite regression has never once been validated out of
period, and the one time we test it that way it fails exactly the way note 07 predicts.**

### Fat tails — 2012 in sigma

| framing | 2012 in sigma | Gaussian implies |
|---|---|---|
| in-sample (the lane's own fit, which *contains* 2012) | **−2.56σ** | 1-in-190 |
| **LOO (fit excludes 2012 — the only honest framing)** | **−3.38σ** | **1-in-2,900** |
| melt-drop, 2012 excluded (drop 3.469 vs mean 2.566, sd 0.325) | **+2.78σ** | 1-in-360 |

It happened **once in 19 years = 1-in-19.** At the depth this trade lives at, the empirical left tail
is **~150× fatter than the model's Gaussian.** In-sample the record year looks like a 2.6σ blip; the
moment you refuse to let the model see it in advance it becomes a 3.4σ impossibility that occurred.
Skew of the residuals is −0.64 (left-skewed) with excess kurtosis −0.08 — the fat tail is *not*
visible as kurtosis in n=19; it is one point. Which is precisely why a Gaussian sd fitted to 19
points cannot be trusted to price a 3% event.

### The mechanism behind the fat tail is named and physical
2012's 3.469 drop was the Great Arctic Cyclone (early August 2012) breaking up an already-thin pack.
That is a *compound* hazard — a single synoptic event, not the smooth accumulation of melt-days the
regression's Gaussian residual assumes. A model whose residual is "weather noise" systematically
under-prices "one storm at the wrong time."

---

## 3. THE COUNTEREXAMPLE HUNT (the 2004-style question, answered)

**Yes — repeatedly, and the closest analogs are the worst.** Applying each historical year's own
Jul-26→minimum drop to 2026's 6.787:

```
2021 4.71 · 2013 4.66 · 2025 4.54 · 2009 4.50 · 2024 4.40 · 2014 4.38 · 2019 4.36 · 2020 4.34
2011 4.31 · 2017 4.29 · 2018 4.25 · 2022 4.20 · 2010 4.06 · 2007 4.05 · 2015 3.87 · 2016 3.78
2023 3.68 · 2008 3.59 · 2012 3.32
```
**Two of nineteen land below 3.6 (2008, 2012). Four below 3.8. Five below 4.0.** 2008's projection
(3.589) misses the T3.6 strike by 11 thousandths of a million km².

Ranked by "late July looked safe → September broke it" (z of Jul-26 vs trailing-10y, minus z of the
realised minimum):

| year | z(Jul-26) | z(min) | gap | Jul-26 | min | drop |
|---|---|---|---|---|---|---|
| **1999** | +0.24 | −1.91 | **−2.15** | 8.908 | 5.676 | 3.232 |
| **2023** | +0.90 | −0.71 | **−1.61** | 7.353 | 4.244 | 3.109 |
| 2012 | −1.42 | −2.65 | −1.23 | 6.809 | 3.340 | 3.469 |
| **2004** | +0.04 | −0.97 | −1.01 | 8.653 | 5.770 | 2.883 |
| **2008** | −0.77 | −1.78 | −1.01 | 7.746 | 4.548 | 3.198 |
| 2000 | +0.05 | −0.94 | −0.99 | 8.777 | 5.943 | 2.834 |

**2023 is the live warning.** Its late-July state was the *most* reassuring of the modern record
(+0.90σ above its own trailing decade) and it still melted 3.109 — a drop that, applied to today,
settles at **3.678**, YES on T3.8 and 8 hundredths from YES on T3.6. **2008** is the pure form of
Ryan's question: the highest Jul-26 extent of the entire 2007-2025 window (7.746) produced the
second-largest drop ever recorded. **A high late-July extent carries essentially no information
about the size of the drop** — that is what slope ≈ 1 *means*, and it is exactly the information the
lane's slope-0.65 model destroys.

Twin check: **2025 had a Jul-26 extent of 6.788 — one thousandth from 2026's 6.787, same sensor —
and settled at 4.543.** A benign twin exists. So does 2012. n=1 each way.

---

## 4. SETTLEMENT FINE PRINT (`KXARCTICICEMIN`) — clean, with three teeth

From `/series/KXARCTICICEMIN` and the live market objects (authoritative; the contract-terms PDF is
font-subsetted and unparseable, the API rules text is the same language):

- **Settle source:** National Snow & Ice Data Center, `nsidc.org/data/seaice_index/archives`.
  `rules_secondary`: *"the lowest individual daily Extent value reported in the **NSIDC Sea Ice Index
  Version 4 Northern Hemisphere daily CSV** for a date within between December 19, 2025 and October
  01, 2026."* → **the settle number is exactly the file we pulled.** BENTER factor-1 passes cleanly:
  fully public, fully reconstructible, free, daily. This is a genuinely good settle source and the
  lane's Mesh delta ("Arctic/NSIDC is a first-class free settle source") stands.
- **Threshold semantics:** `strike_type = less`, "below X" — **strictly below**, so a daily print of
  exactly 3.600 resolves NO. Values carry 3 decimals; immaterial at these strikes.
- **Not the official minimum.** NSIDC headlines the annual minimum as a **5-day trailing mean**;
  Kalshi settles on the **lowest individual daily value**, which is always ≤ the 5-day figure
  (2007-25 gap 0.007–0.073, mean 0.028). Anyone modelling "the NSIDC announced minimum" is modelling
  a number **~0.03 too high** and will under-price every YES rung. Correct here; a trap for others.
- **TOOTH 1 — one-sided early close.** `can_close_early=true`; *"If this event occurs, the market
  will close the following 10am ET."* The trigger is the crossing, so **only the YES side can pay
  early.** A NO holder loses in August and waits until October to win. There is no early exit for
  the winning side and no optionality to compensate for the lock.
- **TOOTH 2 — settlement is October, not September.** `expected_expiration_time 2026-10-02T14:00Z`,
  `latest_expiration_time 2026-10-07T14:00Z`, `close_time 2026-10-02T03:59Z`, settlement timer 3600s.
  Ryan's "hold to September settlement" is off by 2–3 weeks. The minimum itself lands Sep 7–21 in
  2007-25 (latest ever Sep 23, 2018) — so **the position is dead money for the ~2 weeks after the
  outcome is already known.**
- **TOOTH 3 — revision risk is demonstrated, not hypothetical.** The CSV's `Source Data` column
  shows the record is **not one instrument**: 15,218 rows from NSIDC-0051 (SMMR/SSMIS) through
  2024-12-31, then **572 rows from NSIDC-0803 AMSR2 v2 from 2025-01-01 to today.** NSIDC
  retroactively re-sourced the *entire 2025 year* to a different sensor. The rules name no as-of
  date — "the lowest value reported in the v4.0 CSV" — so a re-processing between the September
  minimum and the Oct-2 settlement, or after it, has no defined treatment.
  **How big is the sensor offset? Measured, and it is small.** At the 2024-12-31/2025-01-01 boundary
  the Dec25-31 → Jan1-7 step is **+0.359** against a 10-year same-window mean of **+0.378 (sd 0.167)**
  = **−0.11σ**. No detectable discontinuity; any offset is bounded well inside the seasonal noise at
  winter extent. **This is the one risk that checks out clean** — flag it, don't kill on it. (Summer
  offset is unmeasurable from this file; there is no overlap period.)

---

## 5. LIVE BOOK, DEPTH, AND THE FISH THAT ISN'T THERE

Live 2026-07-27 (`*_dollars` / `*_fp` only — plain fields read None, note 33 §API quirk):

| rung | yes bid | yes ask | OI | vol | NO buyable (taker) |
|---|---|---|---|---|---|
| T3.6 | 0.19 (5) | 0.24 | 115 | 115 | 5@.81 · 10@.82 · 110@.83 · 310@.84 · 460@.86avg |
| T3.8 | 0.26 (103) | 0.31 | 210 | 210 | 103@.74 · 203@.745 · 403@.752 · 451@.768avg |
| T4.0 | 0.36 (5) | 0.40 | 200 | 200 | 110@.659 · 310@.666 · 546@.754avg |
| T4.2 | 0.45 (5) | 0.50 | 35 | 35 | 105@.560 · 305@.573 |
| T4.3 | 0.58 | 0.59 | 5 | 5 | — |
| T4.4–T4.8 | .66/.75/.86/.92 | .70/.78/.88/.97 | ≤5 | ≤5 | — |

Depth is **real** — ~310 contracts of NO available at ≤84¢ on T3.6, ~$259. The lane's "~$300 of
fish" capacity estimate is right. **It is the word "fish" that is wrong.**

**The tape, all 9 rungs, every trade since listing (2026-07-26 05:12 → 2026-07-27 17:21):**

```
taker-side NO : 580.00 contracts
taker-side YES:   1.43 contracts
```

**Zero tail-fear demand exists.** The lane's mechanism — *"a crowd that fears the ice-collapse
tail"* buying the deep YES wings — is **falsified by the tape**. Every contract of the 115/210/200
open interest the lane cited as evidence of a fish was created by someone doing *our* trade: taking
NO into a maker's resting YES bids. The counterparty is a market maker running a smoothly-fitted
ladder, and it is **repricing away as it is hit** — T3.6 traded 0.22 (Jul-26 12:50) → 0.21 → 0.21 →
0.20/0.21 (Jul-27 13:47); T4.0 0.39 → 0.36. Mesh law: *no named fish = no idea.* The named fish was
named from open interest without reading taker side. It is not there.

**And the maker's ladder is not naive.** Fitting a normal to the *centre* rungs alone gives
μ=4.226, σ=0.356 (SSE 0.0006 across T4.2–T4.6 — a near-perfect fit). Against its own centre, the
maker's wings are marked **+0.176 / +0.169 / +0.117** rich at T3.6/T3.8/T4.0. That is not an error;
that is a maker deliberately pricing a fat left tail on a variable whose left tail is empirically
fat (§2). The maker's 0.215 is ~2× the analog estimate of 0.105 — so a residual edge does exist —
but the "wings break away from the model" observation is *the maker being right about the tail
shape and possibly overdoing it*, not the maker being wrong about the distribution.

---

## 6. CAPITAL LOCK vs STAGE MATH — the tail-book kill, again

Bankroll **$93.09** (note 40). Today 2026-07-27.
**Days to settlement: 67 (expected, Oct-2) to 72 (latest, Oct-7).** Not "September".

Fee: quadratic, `fee_multiplier=1` → 0.07·C·P·(1−P) per order, ceil to the cent.
A 111-contract order at 0.8286 → $1.11 = **1.0¢/contract**.

**T3.6 NO at 0.8286 (110 contracts, the realistic clip), all-in cost 0.8386:**

| P(YES) source | EV/contract | return on capital | **%/day over 67d** |
|---|---|---|---|
| lane, N(4.258, 0.340) — 2.5% | +14.5¢ | +17.5% | **+0.261%** |
| honest t₁₇ predictive — 5.0% | +12.0¢ | +14.5% | **+0.216%** |
| LOO-empirical — 5.3% | +11.7¢ | +14.1% | **+0.211%** |
| **melt-drop analog — 10.5%** | **+6.5¢** | **+7.8%** | **+0.117%** |
| market mid — 21.5% | −4.5¢ | −5.4% | −0.081% |

**Even taking the lane's most optimistic number at face value, this is 0.26%/day.**
Note 15's staged goal at the $93 stage is **5–15%/day** (gas / vol-book / streak / mention all run
there); the *most defensible number in the whole project* is 2%/day at $50k. This trade is
**8× below the steady-state floor and 20–60× below the current-stage rate**, and it consumes the
capital those engines run on.

**Sizing, if it were taken:**
- $93.09 buys **111 contracts** at 0.8386 all-in = **$93.08 = 100.0% of bankroll.**
- On a loss: bankroll → **$0.01.** Not a drawdown — ruin.
- Full-Kelly at p=0.050 says f*=0.71 ($66); at p=0.105, f*=0.39 ($36); quarter-Kelly $16.48 / $9.02.
  **All of these are wrong here** — Kelly assumes a repeatable independent bet, and this is
  n=1-per-year with a 67-day lock and 2× model disagreement on p. A quarter-Kelly $9–16 clip earns
  **$0.70–$1.90 total, over 67 days**, i.e. ~$0.02/day, while tying up 10–18% of the bankroll.
- Sizing implication either way: **$0.**

The tail-book (`verify-tail-book.md`) died on exactly this arithmetic: $1,727 locked for 146 days on
a $93 bankroll. This is the same object at 1/18th the notional and it fails the same test, because
the test is about **days and share-of-bankroll, not dollars.**

---

## 7. WHAT SURVIVES (write these back to the Mesh)

- **NSIDC v4.0 daily CSV is a confirmed first-class settle source** and Kalshi settles on it by name.
  `curl` + browser UA → 200; WebFetch 500s. Reconfirmed today, 15,790 rows.
- **The record is two instruments.** NSIDC-0051 (SMMR/SSMIS) to 2024-12-31, **NSIDC-0803 AMSR2 v2
  from 2025-01-01** — NSIDC re-sourced a whole past year retroactively. Measured boundary step is
  −0.11σ vs the 10-yr seasonal norm (no detectable offset), but *any* long-horizon contract settling
  on a satellite record must check the Source Data column, not just the value column.
- **Kalshi settles Arctic min on the LOWEST DAILY value, NOT NSIDC's headline 5-day-mean minimum.**
  Systematic 0.026 gap (2007-25). Anyone using the announced minimum under-prices every YES rung.
- **Read taker side before naming a fish.** Open interest is *not* evidence of retail demand — here
  580 vs 1.43 contracts says the OI is entirely other people's NO sales into a maker's ladder. Add
  a taker-side check to the idea template; M5 named a fish that the tape disproves in one query.
- **Range restriction manufactures false precision.** A regression slope of 0.65 on a 19-year window
  whose predictor spans 1.5 units, against 1.07 ± 0.06 on the full 42-year record, is attenuation.
  Shrinkage toward the mean is what made 3% look real. **When a fitted slope < 1 is doing the work
  of a tail probability, re-fit on the longest available window before trusting it.**
- **Note-07 correction to apply everywhere:** a residual sd is not a forecast sd. Divide by n−2, add
  `1 + 1/n + (x₀−x̄)²/Sxx`, use t at small n. Here that alone was 0.340 → 0.382 and 2.5% → 5.0%.
- **New graveyard entry:** *annual-cadence natural-science ladders.* Public settle source, honest
  computable model, real book depth — and still untradeable, because n never accrues and the capital
  lock is a full melt season. Do not re-propose the Antarctic sibling, the September-mean sibling, or
  next year's KXARCTICICEMIN on a sub-$1k bankroll.

## 8. THE GATE, IF ANYONE REOPENS IT

Not a gate on the model — a gate on the frame. All must hold simultaneously:
1. bankroll ≥ **$3,000**, so a 3%-of-bankroll clip ($90) is a rounding error rather than the account;
2. the honest tail estimate (analog, not Gaussian) still ≤ 8% at the entry rung;
3. NO available at ≤ **0.80** (not 0.88 — the 88¢ trigger was calibrated to the fake 3%; at the
   honest 10.5% it is EV-negative after fee);
4. and even then it competes at ~0.12%/day against engines running 100× that. It loses.

*Data: `nsidc_daily.csv` (15,790 rows), `res.json`, `ob_T*.json`, scripts `prep.py m1.py m2.py m3.py
m4.py sens.py` in the session scratchpad. Read-only throughout: no orders, no `~/kalshi_data`
writes, no nestor changes.*
