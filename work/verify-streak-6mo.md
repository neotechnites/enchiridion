# VERIFY: 4-streak reversal signal over SIX MONTHS — regime + dollars (2026-07-26)

Extends `verify-streak-execution.md` (execution policies fitted on 2.3 days of Kalshi tape) to a
6-month signal population. Question Ryan asked: **how much more would we expect to earn** under the
PROPOSED policy (rest@40 + taker backstop≤48 at T0+45s) vs the CURRENT policy (taker-only, fire iff
ask≤44, IOC@46). Answer below is an **EXPECTATION from a model**, not reconstructed fills — see §4.

## 1. Data
Coinbase public exchange API, `granularity=900` (15m candles), paginated 300/req, no auth.
**BTC-USD 17,352 candles, ETH-USD 17,352 candles**, 2026-01-22 00:00Z → 2026-07-22 00:00Z (~181 days,
~99.9% of the 17,376 theoretical windows; small gaps). Script: `/tmp/streak_6mo.py` (pull),
`/tmp/streak_analyze.py` (reconstruct+dollars). Raw cache: `/tmp/candles_6mo.json`.

## 2. Streak reconstruction
Each 15m window direction = sign(close − open); **flat (close==open) = no direction, resets the streak**.
4-streak = 4 consecutive same-direction windows; **signal = bet the reversal in the next window**;
**win = next window direction is opposite the streak** (next-window flat counted as a loss; only 2 of 1,951).

**Signals (6 mo, both coins): 1,951** — BTC 1,002 (WR 55.09%), ETH 949 (WR 56.59%).
**Overall reversal win rate = 55.82%** (1,089 / 1,951).

### Monthly win-rate series (the regime check)
| Month | n | wins | WR |
|---|---|---|---|
| 2026-01 (partial) | 97 | 56 | 57.7% |
| 2026-02 | 295 | 173 | 58.6% |
| 2026-03 | 349 | 197 | 56.5% |
| 2026-04 | 306 | 155 | **50.7%** |
| 2026-05 | 351 | 205 | 58.4% |
| 2026-06 | 323 | 183 | 56.7% |
| 2026-07 (partial) | 230 | 120 | 52.2% |

**Verdict: neither 52% nor 54.7% is the "true" number — the 6-mo BRTI-proxy rate is ~55.8%, above both.**
It is **reasonably stable and never dips below 50.7% in any month** (April was the trough; July soft at
52.2%). Range 50.7%–58.6%, mean 55.8%, no month at or below breakeven-for-the-edge (50%). So the edge is
real and persistent; **54.7% is the better single anchor, 52% is a genuine conservative floor**, and the
measured 55.8% is the central estimate. The signal fires often — ~11/day across both coins.

## 3. Dollars — 6-month expected earnings, BOTH policies
**Stake: flat $4/signal ≈ 8 contracts.** Per-signal $ = 8 × E[capture in cents] / 100.
6-mo E[$] = 1,951 signals × 8 contracts × E[capture] / 100.

### Per-signal E[capture] formulas (reconstructed from the execution ledger's construction, prev1 primary set)
- fee(p) = 7·p·(100−p)/10000 cents; EV(w,p) = 100·w − p − fee(p).
- **CURRENT** = 0.40 · EV(w, 44)  → linear form **40·w − 18.29** (fill 40% at price 44).
  Verifies to their published 2.50c@52% / 3.58c@54.7%.
- **PROPOSED** = 0.24·EV(w,40) + 0.193·(100·w − 46.44)  → linear form **43.3·w − 18.97**
  (maker leg fills 24% at 40; taker backstop fills 19.3% of signals at blended cost price+fee = 46.44c).
  Verifies to their published 3.55c@52% / 4.72c@54.7%.
- Fill fractions (0.40 / 0.24 / 0.193) and the taker cost (46.44) are **held fixed** (measured on 2.3-day
  tape, assumed stationary); **only w is re-evaluated** at the 6-mo rate.

### Results
| Win rate | cap current | cap proposed | 6-mo E[$] current | 6-mo E[$] proposed | **Δ (more earned)** |
|---|---|---|---|---|---|
| **measured 55.82%** | 4.04c | 5.20c | **$630** | **$812** | **+$182  (+28.9%)** |
| conservative 52.0% | 2.51c | 3.55c | $392 | $554 | +$162  (+41.4%) |
| recent 54.7% | 3.59c | 4.72c | $560 | $737 | +$176  (+31.4%) |

**HEADLINE: at the measured 55.8% win rate, the PROPOSED policy is expected to earn ~$182 more over
6 months ($630 → $812, +29%). Conservative floor (52%): +$162 (+41%).**

The delta is larger in % terms at lower win rates because the current policy's edge shrinks faster (it
throws away the 44<ask≤48 band the backstop captures); in absolute $ the delta is ~$160–180 across the
whole 52–56% range — robust to which win-rate anchor you believe. Note the *absolute* dollars are modest
($0.6–0.8k / 6 mo) because size is tiny (8 contracts); the win is fractional edge capture, not scale.

## 4. Assumptions — LABELED (all load-bearing)
1. **Money numbers are EXPECTATIONS from a model, not reconstructed fills.** No Kalshi book was replayed
   for these 1,951 historical signals — the book 6 months ago is **unobservable**. We apply fixed
   per-signal capture rates to a measured signal count. Treat $630/$812/$182 as modeled expectations.
2. **Execution parameters (fill probs 0.40/0.24/0.193, ask-path distribution, taker blended cost 46.44,
   the whole prev1 microstructure) are measured on ~2.3 days of recent tape (2026-07-24→26) and ASSUMED
   STATIONARY across all 6 months.** This is the biggest unverified leap — book depth, spread, and the
   dip-then-sweep ask path 6 months ago could differ materially. Not testable with candle data.
3. **Candle direction (close vs open) is a PROXY for Kalshi's BRTI settlement** direction. Kalshi settles
   on BRTI at the window boundary, not Coinbase 15m close; the two can disagree near flat windows. The
   measured 55.8% is therefore a proxy win rate, not the exact Kalshi win rate. (The exec ledger's
   52/54.7% came from Kalshi tape and may reflect BRTI settlement more directly — consistency check: our
   proxy 55.8% sits just above their 54.7%, so no red flag.)
4. **Flat windows** treated as no-direction (reset streak; flat-next = loss). Only 2 flat-next cases, so
   this convention is immaterial. Coinbase 15m flats are rare at BTC/ETH price granularity.
5. **prev1 primary set used** (not the deeper true-4-streak microstructure). The exec ledger showed real
   4-streaks are cheaper/faster to fill, so prev1 fill-probs are **conservative** — the proposed-policy
   gain is if anything understated.
6. **Winrate exogenous to the capture model** — the exec ledger flags that conditional winrate in
   never-dipped / swept-up windows (exactly where the taker@48 fires) may be lower than the marginal
   55.8%. If the conditional edge in those windows is materially <52%, the proposed delta shades down.
   Untested here.
7. Signal count assumes both coins traded independently and every 4-streak is actionable (early-tell
   timing to rest pre-T0 assumed ~80% per the exec ledger; the ~20% late case degrades to taker-only but
   is already inside the "current"-style capture, so it does not inflate the proposed delta).
