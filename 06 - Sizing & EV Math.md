# 06 - Sizing & EV Math

> ⚠️ **The streak numbers below are the OLD unconditional (~52¢ entry) version.** Current rule (2026-07-23): **streak ≤44¢** price-gated, 2-yr regime-proof — see [[18 - LIVE STATE (2026-07-23)]]. The sizing/EV MATH here (Kelly logic, cluster correlation, fee mechanics) remains valid; re-derive the numbers with the ≤44¢ rule's stats before using.

## The streak-reversal bet (the confirmed edge)
- Entry ~52¢, win ~56%, **net EV +2.2¢/contract = +4.3% per trade** on staked capital.
- **Near-symmetric payoff** (win ~+48¢, lose ~−52¢) → no fat tail → healthy compounding (unlike favorite bets).
- These are sequential 15-min bets, so one bankroll recycles all day.

## Kelly
- b (win/risk) = 48/52 = 0.923; w = 0.558.
- Full Kelly f* = (w(b+1)−1)/b = **~7.9% of bankroll per trade.**
- **Use ¼ Kelly (~2%/trade)** for sane drawdowns.

## Realistic daily return
| sizing | 5 trades/day | 10 trades/day |
|---|---|---|
| ¼ Kelly (~2%/trade) | ~+0.43%/day | ~+0.86%/day |
| ½ Kelly (~4%/trade) | ~+0.9%/day | ~+1.7%/day (bigger drawdowns) |

Dollar terms (¼ Kelly): on $10k ≈ +$43–86/day; on $100k ≈ +$430–860/day.

## On the 10%/day target — honest
- **Not reachable from a single 56/45 edge.** Even full Kelly compounds at only ~+0.2–0.5%/trade; 10%/day would need ~ruinous leverage and blows up on the loss streaks (which *will* happen at 56%).
- BUT ~0.5–1%/day is **elite**: +0.7%/day compounds to **~5–6× per year**.
- **The path to a bigger daily number is MORE (uncorrelated-ish) signals, not bigger bets** — i.e., the [[04 - Combined Model Plan]] stacking the streak with regime-conditional features to get more +EV trades/day, and adding other assets (ETH/BCH/SOL 15-min) for less-correlated exposure.

## Multi-asset scaling (the real edge-increaser, 2026-06-24)
- Same streak rule on **BTC+ETH+SOL+XRP+DOGE 15-min** = **~50 signals/day** (vs ~10 BTC-only), each ~53–57.5% win. See [[02 - The Confirmed Edge - Streak Reversal]].
- ~5× the trades at the same per-trade edge → ~5× daily profit, AND more diversified (different assets) so effective-independent-bets > 1 → **safely deploy more total capital** (the correlation that capped BTC-only sizing is partly relieved).
- Rough: ¼-Kelly per trade, ~50 trades/day across assets → plausibly **~2–4%/day** (vs 0.5–1% BTC-only), still with real variance. Closer to Ryan's target, though 10%/day still needs aggressive sizing.
- Per-trade edge couldn't be lifted (vol/depth filters died); the entry-price filter (pending confirmation) is the only remaining per-trade sharpener.

## $100 COMPOUNDING SIM + KELLY (2026-06-24) — data is only ~2 months (Apr19–Jun23), IN-SAMPLE
674 streak trades BTC+ETH+DOGE, win 57.3%, entered at open quote, hold to settle. **$100 start:**

Flat fractional sizing: 2%→$389, 5%→$1,584, 10%→$3,265, **25%→RUINED** (blew up at trade 290).

Per-trade Kelly (cap 50%/trade):
| sizing | final | max drawdown |
|---|---|---|
| FULL Kelly | RUINED | 100% |
| HALF Kelly | $408 | 92% |
| **QUARTER Kelly** | **$1,055** | 62% (profit-max) |
| 1/8 Kelly | $509 | 37% |

With realistic edge haircut (assume 53% win): QUARTER→$613 (56%DD), 1/8→$334 (33%DD).

**Key lessons:**
- **Full Kelly / 25% flat RUINS even with a real edge** (variance drag). Bankroll mgmt is mandatory.
- **Profit peaks ~quarter-Kelly**; over-sizing *lowers* growth (HALF made less than QUARTER).
- **Drawdowns are intrinsic and brutal (35–62%)** — you're leveraging cheap near-coinflip contracts. Can't size them away without killing returns.
- All IN-SAMPLE + frictionless (assumes fill at open quote on every signal; only ~35% of signals had a fillable open trade). Live will be lower. **Forward paper-test before real money.**
- Script: `~/kalshi_data/scripts/sim_kelly.py`, `sim100.py`; cached trades `~/kalshi_data/streak_trades.json`.

## DATA WINDOW CORRECTION
KXBTC15M (and ETH/SOL/XRP/DOGE 15M) only exist on the API from **~April 18, 2026** (~2 months). Probed back to 24 months: **nothing before ~April 2026.** Earlier notes saying "5 months" were wrong — all analysis is on ~2 months. A 2-year backtest is impossible (data doesn't exist). Polymarket has short-term (5-min) crypto markets with near-zero fees — worth testing the same edge there (not yet pulled; needs the right gamma-api/clob query).

## Risk notes
- It's an **average with variance** — individual days go negative; 4–6 trade losing streaks are normal at 56%.
- Kalshi position limit ~$25k/market — not binding at sane sizes.
- The +2.2¢ point estimate has error bars (330-trade subsample); the *win rate* and *signal* are the robust parts. Validate live small before scaling.
