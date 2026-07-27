# VERIFY-BEZOS (2026-07-27) — adversarial verification of lane-BEZOS-TAPE's four live-money claims

Mandate: break them before they touch parameters. Independent re-derivation from
`~/kalshi_data` + `senate/nestor/data` — none of the lane's intermediate JSON reused.
Scripts: `<scratchpad>/vb_core.py`, `vb_claims.py`, `vb_placebo.py`.

Reproduction check first: my rebuild gives **n=1,238 priced signals** (BTC 229 / ETH 206 /
SOL 264 / XRP 299 / DOGE 240), span 2026-06-25 → 2026-07-22, and reproduces the lane's
45.7% @ L=40 on n=422, the backstop table to the cent, and the clock table to the cent.
**The lane's arithmetic is clean. Everything below is about what the arithmetic means.**

---

## VERDICTS

| # | claim | verdict |
|---|---|---|
| 1 | fills adversely selected −8.7pp | **CONFIRMED (number) / REFUTED (mechanism)** — it is the universal cheapness tax, not a streak property |
| 2 | taker backstop EV-negative at 46¢ | **CONFIRMED in sign, worse than stated / UNDECIDABLE-until-n≈3,350 fills for significance** |
| 3 | maker rung better at 42-44 than 40 | **REFUTED as directional / UNDECIDABLE-until-n≈10,900 signals** |
| 4 | overnight 00-05Z the one losing cell | **REFUTED** — permutation p=0.39, placebo inverts it, out-of-span inverts it |

---

## 0. Three data/model defects found first (they touch all four claims)

**(a) Early-second truncation in the virgin tape — CHECKED, not fatal.**
`pull_alt_virgin.py` uses `MAX_PAGES=3` (3,000 newest-first trades) and `pull_full_paths.py`
uses 45. Any window busier than that loses its EARLIEST seconds — exactly the 0-52s band this
lane lives in. Measured: windows with no print ≤52s = **BTC 17.3%, ETH 16.3%, SOL 0.2%,
XRP 0.1%, DOGE 0.5%**. Inside the virgin span, 89/1,324 signals (6.7%) are dropped this way,
and the dropped ones win **57.3% vs 54.4%** for the kept ones (BTC 63.9% vs 55.0%, n=36).
So the censoring is on high-activity windows, it is small, and it biases the fade *down*, not
up. **Not a break** — but any future work in the first minute must re-pull BTC/ETH with more
pages before trusting fill counts.

**(b) The fill model rests from t=0. Our own tape says the market is not visible until t≈26s.**
The lane's finding 6 measured first-quote at p50 T0+25.9s and live signal offsets of 4-58s,
then modelled fills over [0,45s] anyway. Refitting the rest window to the live median:

| rest starts at | maker40 fills | fill% | win%\|fill | adverse selection |
|---|---|---|---|---|
| t=0 (lane) | 422 | 34.1% | 45.7 | −8.7pp |
| t=10s | 376 | 30.4% | 44.4 | −10.0pp |
| **t=26s (live p50)** | **314** | **25.4%** | **42.7** | **−11.8pp** |

**(c) Queue position and marketability — the big one.**
The convention "a bid at L fills iff the reversal side trades at ≤L" mixes two different
orders. Decompose the 422 L=40 fills:

- **41 (9.7%)** only ever *touched* 40 and never traded through it → behind the queue, not a fill.
- **127 (30.1%)** were already ≤40 **at our first observation** → a limit at 40 there is
  **marketable and crosses as a TAKER**, paying the fee the lane's $0-maker assumption removes.
  At L=44 that share is **40.1%**; among the incremental 40→44 fills that carry claim 3 it is 28.4%.

Stacking the three optimisms (all on the same 1,238 signals):

| model | fills | fill% | win%\|fill | 95% CI | cushion vs 40¢ | EV¢/signal |
|---|---|---|---|---|---|---|
| lane (rest t=0, touch, $0 fee) | 422 | 34.1% | 45.7 | [41.0, 50.5] | **+5.7pp** | +1.955 |
| + queue-safe (traded THROUGH 40) | 381 | 30.8% | 45.9 | [41.0, 51.0] | +5.9pp | +1.826 |
| + drop marketable-at-arrival (true maker only) | 257 | 20.8% | 44.4 | [38.4, 50.5] | +4.4pp | +0.905 |
| **+ rest from t=26s (live discovery p50)** | **106** | **8.6%** | **41.5** | **[32.6, 51.0]** | **+1.5pp** | **+0.129** |

**The 5.7pp cushion the whole lane rests on is +1.5pp [CI straddles breakeven] once the order
is a real resting maker order placed when we can actually see the market.** Queue position
alone costs little (45.7→45.9 win, −10% of fills); *marketability + arrival delay* cost almost
everything.

**(d) Live confirmation of (c) — and the fee premise is falsified.**
`nestor/data/streak_week1.jsonl`, the one live fill: `limit_placed 38`, `ask_at_signal 38`,
`actual_fee_cents 16.5`, `average_fee_paid 0.0165`/contract. Taker fee at 38¢ = 7·0.38·0.62 =
**1.649¢** — it **crossed the spread and paid a full taker fee**. The five misses (limits 42,
31, 44, 44, 44, each set equal to the observed REST ask) filled 0/5.
**Live maker fills to date = 0. The $0-maker-fee exemption that carries every positive number
in this lane, including the TOP DOOR, has never been observed in our own account.**
P(0 fills in 5) = 0.124 under the lane's 34.1%, 0.312 under true-maker-only 20.8%, 0.638 under
the queue+delay model 8.6% — the live record is consistent with the pessimistic model.

**(e) Minor, unquantified:** the backtest knows window i−1's result with certainty at T0; live,
nestor *derives* it from a 62-sample buffer (`streak_derive`, `derived_margin_bp` observed
5.6-39.6 bp, median 10.9). Not lookahead in the information sense, but the backtest does not
model derivation error on thin margins.

---

## 1. Adverse selection −8.7pp — CONFIRMED as a number, REFUTED as a mechanism

Reproduced exactly: maker40 fills **45.7%** [41.0, 50.5] vs all signals **54.4%** = **−8.7pp**.
Robust to queue-safety (45.9%) and gets worse, not better, with realistic delay (−11.8pp at t=26s).
The arithmetic stands.

**The placebo kills the attribution.** Same book, same fill model, same window, but on
**non-streak** windows with a deliberately unvalidated side (fade of the previous *single*
window), **n=10,640**:

| population | signals | ALL win% | maker40 fills | win%\|fill | adverse |
|---|---|---|---|---|---|
| streak fade (lane) | 1,238 | 54.4 | 422 (34.1%) | 45.7 | **−8.7pp** |
| **placebo side** | **10,640** | **52.3** | **3,734 (35.1%)** | **40.7 [39.1, 42.3]** | **−11.6pp** |

A random side is *more* adversely selected than the streak fade. So the lane's mechanism
sentence — "the fade side gets cheap precisely in the windows where the streak is continuing" —
is not a property of the streak; it is the note-07 structural lesson ("cheapness is
information") measured again, and it applies to any resting bid in this book.

**What actually survives, and it is the better finding:** the placebo maker-40 fill wins
**40.7%** against a 40.0% breakeven — a dead coin. The streak fill wins **45.7%**. The
signal-specific premium is **+5.0pp on the fill-conditional population, n=422 vs n=3,734.**
That is the real measurement, and it is a stronger statement than the one the lane made.

## 2. Taker backstop EV-negative at 46¢ — CONFIRMED in sign, UNDECIDABLE for significance

Two modelling favours the lane granted itself, both removed:

- it takes **min price in the (45,52] band** = intra-band lookahead (worth ~+0.8¢/fill);
  realistic is the **first print at or below the ceiling**.
- `fee = 0.07·P·(100−P)/100` is un-rounded; Kalshi rounds the fee **up to the next cent**.

| ceiling | fills | win% | 95% CI | lane EV¢/fill | +ceil-rounded fee | **+ first-hit (honest)** |
|---|---|---|---|---|---|---|
| 44 | 62 | 41.9 | [30.5, 54.3] | −1.68 | −1.98 | **−2.74** |
| 45 | 82 | 45.1 | [34.8, 55.9] | +0.74 | +0.45 | **−0.33** |
| **46 (SHIPPED)** | **103** | **43.7** | **[34.5, 53.3]** | **−1.37** | **−1.66** | **−2.38** |
| 47 | 124 | 42.7 | [34.4, 51.5] | −2.95 | −3.23 | −3.90 |
| 48 | 146 | 44.5 | [36.7, 52.6] | −1.78 | −2.05 | −2.79 |

The claim survives and strengthens: **no ceiling from 44 to 48 is positive** under honest
modelling (45 flips from +0.74 to −0.33). Two corrections to the lane's prescription:

- its fallback "ship at ceiling 44, costs almost nothing (−1.68¢)" is really **−2.74¢/fill**.
- its gate — "re-check at n≥250 backstop fills" — **cannot work**. Detecting 43.7% against a
  46.1% breakeven at 80% power needs **n ≈ 3,350** backstop fills (n ≈ 9,850 against the
  lane's own 45.1% breakeven). At n=250 the CI half-width is ±6.2pp on a 2.4pp effect.

**This claim is not resolvable by more fills at any realistic rate.** It must be decided on
prior + cost: the backstop is the only fee-paying leg, its point estimate is negative under
every model, and dropping it costs +0.115¢/signal of foregone upside. **Turn it off; do not
pretend a gate will settle it.**

## 3. Maker rung better at 42-44 than 40 — REFUTED as directional

Paired bootstrap on the **same** 1,238 signals (the lane compared independent CIs on
overlapping populations, which is the wrong test — these policies share ~80% of their fills):

| policy diff | Δ EV¢/signal | 95% CI | P(Δ ≤ 0) |
|---|---|---|---|
| L=42 − L=40 | +0.530 | [−0.279, +1.336] | 0.104 |
| L=44 − L=40 | +0.546 | [−0.572, +1.700] | **0.169** |
| L=46 − L=40 | +0.221 | [−1.145, +1.609] | 0.372 |

Three independent reasons to refuse the direction:

1. **Sign flips across coins and across time.** Δ(44−40): BTC +2.445, DOGE +1.083, SOL +0.258,
   **XRP −0.147, ETH −0.816**; **H1 +1.173, H2 −0.091**. The lane's "both halves positive at all
   three rungs" is true of the *levels* and false of the *difference*, which is the claim.
2. **It inverts under the realistic rest window.** Resting from t=26s: L=38 **+0.908**,
   L=40 +0.679, L=42 +0.712, L=44 **+0.649**, L=46 +0.588, L=48 +0.136. The recommendation
   reverses to "if anything, lower."
3. **"Win rate rises with L" is a mathematical identity, not evidence.** As L→100 the
   fill-conditional rate converges to the unconditional 54.4% by construction. Observing
   45.7 → 47.9 → 49.0 → 49.8 → 51.3 across L=40..48 is what a *zero-information* fill model
   also produces (placebo: 40.7% at L=40 rising the same way toward its own 52.3%).

Power: sd of the per-signal Δ(44−40) is **20.35¢**; detecting +0.546¢ at 80% power needs
**n ≈ 10,900 signals — 8.8× the current corpus.** This is not a live-tape question at all; it
is decidable only by re-pulling the virgin price tape across a much longer span.

**Verdict: the lane's own framing ("a re-derivation order, not a ship order") is still too
strong. There is no measured basis to move off 40 in either direction.**

## 4. Overnight 00-05Z the one losing cell — REFUTED

Cells reproduced exactly (00-05: 300 signals, 98 fills, 38.8% win, −0.400¢/signal). Four
independent attacks, all of which it fails:

**(a) Multiple comparisons, done properly.** Shuffling the hour labels 5,000× and taking the
**worst of 5 cells** each time: permuted worst-cell median **−0.132**, p5 **−2.290**. Observed
worst cell **−0.400**. **p = 0.39.** A cell this bad is the ordinary consequence of cutting
1,238 signals five ways.

**(b) The block boundary manufactured the cell.** Hour by hour (EV¢/signal, maker40):
`00:+3.00(n60) 01:−1.33(n45) 02:−3.67(n49) 03:−1.40(n43) 04:−3.23(n62) 05:+4.88(n41)`.
Hours 00 and 05 are among the **best** hours in the day; only 01-04 (n=199 signals, 65 fills)
are negative. The "00-05" label is a boundary chosen after seeing the data.

**(c) Placebo inverts the ordering.** Same clock split on the 10,640-signal placebo population:

| block | placebo signals | placebo EV¢/sig | real EV¢/sig |
|---|---|---|---|
| **00-05** | 2,733 | **+0.249 (2nd best)** | **−0.400 (worst)** |
| 06-11 | 2,528 | +1.171 (best) | +3.444 |
| 12-16 | 2,235 | −0.027 | +1.429 |
| 17-21 | 2,281 | −0.246 | +2.654 |
| **22-23** | 863 | **−0.556 (worst)** | **+3.969 (best)** |

There is no structural "overnight books are worse" effect in this venue to hang the claim on —
on 8.6× the data the overnight cell is fine and the lane's *best* cell is the placebo's worst.

**(d) Out-of-span inverts it too.** Unconditional fade win rate on the settlement corpus
outside the virgin tape (n=2,043 signals we have no prices for, so a clean time-OOS slice):
**00-05 = 56.7% [52.3, 60.9] (n=503)** vs **06-23 = 54.7% [52.2, 57.2] (n=1,540)**. Overnight
signals are *better*, not worse, out of period.

The lane's defence — both halves of the 28 days give −0.38 / −0.42 — is precisely the note-07
favourite-underpricing case study: **within-month split-half is not out-of-sample**, both halves
share the regime. Its proposed gate ("does 00-05 stay negative on the next 100 fills?") needs
**n ≈ 1,592 signals in the cell (~520 fills)** for 80% power on a 2.9¢/signal gap with
sd=29.2¢ — **5× what it asks for.**

**Do not clock-gate the streak strategy on this.**

---

## WHAT ONLY LIVE TAPE CAN DECIDE, AND HOW MANY FILLS

Everything above is decidable from disk. These are not:

| question | why simulation cannot answer it | n needed |
|---|---|---|
| **Is a maker fill even achievable?** Does a non-marketable resting bid at 40-44 ever get hit, and at what rate? | queue depth ahead of us is not in any tape we own; the "trades ≤L" proxy has no queue | **~60 non-marketable resting attempts** to separate 20% from 5% at 80% power; **~150** to pin the rate to ±8pp. Live today: **0/5.** |
| **Do we actually pay $0 on maker fills?** | our one live fill paid a full taker fee; the exemption is an assumption, never observed | **1 confirmed passive fill.** This is the single cheapest and most load-bearing live fact outstanding. |
| **Realised fill price vs the modelled price** (partial fills, price improvement, cancel/repost) | `paper_maker_fills` assumes fill at exactly L, whole size | **~30 fills** for a mean slippage estimate to ±0.5¢ |
| **maker_rest vs taker_backstop path attribution** | `EntryPath` has produced **0** records | **≥1** of each to validate the logger; ~250 for any EV read (and see claim 2: 3,350 for a verdict) |
| **Whether WS-gating converts phantom passes into fills** (lane finding 5) | REST/WS divergence is measured, the *fill consequence* is not | **~100 signals** WS-gated vs REST-gated, paired |
| **Fill-conditional win rate at 45.7% vs 40% breakeven** | in principle simulable, but only if the fill model is right — and it is not (§0c) | **599 maker fills** for 80% power. At BTC+ETH only (~3.5 signals/day × the honest 8.6-20.8% fill rate) that is **1-2 years**. |

**Blunt reading of the last row: the streak maker edge is not liveable-testable on its own
P&L.** The only live facts worth buying are the mechanical ones — *does a passive order fill,
and is it free* — and those cost tens of attempts, not hundreds.

## NET EFFECT ON PARAMETERS

- **Nothing in this lane licenses a rung change.** Claim 3 is inside noise and flips sign under
  the realistic rest window and in half the coins. **Leave the maker rung at 40.**
- **Backstop off** is supported — not by claim 2's significance (there is none, and there cannot
  be at achievable n) but by: negative point estimate under every model, only fee-paying leg,
  and −0.115¢/signal cost to drop.
- **No clock gate.** Claim 4 fails permutation, placebo, and out-of-span.
- **The one change this verification actively wants** is the one the lane already named and
  under-weighted: make the entry a genuine, early, WS-gated resting order, and **instrument
  whether a passive fill happens and what fee it pays.** Until a passive fill is observed, every
  positive EV number in `lane-BEZOS-TAPE-jul27.md` — and its TOP DOOR — is conditional on an
  unobserved fee exemption.

Related: [[07 - Overfitting & Validation Discipline]] · [[15 - Operating Manual (spin-up & method)]] ·
`work/lane-BEZOS-TAPE-jul27.md`
