# 08 - Cheap-Side Streak Reversal (the off-50 candidate)

> ✅ **Resolution (2026-07-23): this lead CONFIRMED and matured into the live `streak ≤44¢` rule** — post-4-streak reversal side in first 60s if ≤44¢, 2-yr regime-proof 56-57% every slice, now a slate system and Ryan's first live build ([[18 - LIVE STATE (2026-07-23)]]). The 'NOT confirmed' status below is the old snapshot; a textbook example of a conditional lead that found its gate (Kill Taxonomy, note 15).

> Session 2026-06-25. The strongest OFF-50 lead found. **Status: PROMISING but NOT confirmed for real money** — passes every Kalshi-internal test, but fails/can't-be-validated cross-platform. Read [[10 - Stress Test & Verdict]] before risking a dollar. Supersedes the "entry-price filter" caveat in [[02 - The Confirmed Edge - Streak Reversal]].

## The rule
1. Track the last four 15-min outcomes on a crypto market (BTC/ETH/SOL/XRP/DOGE).
2. After **4 in a row**, at the next market's open, buy the **reversal side** (opposite the streak).
3. **ONLY take it when the reversal side opens CHEAP — entry < ~46–48¢.** Skip if it's near 50¢ (fee-heavy, no edge) or already rich.
4. Hold to settlement. This is OFF-50 (avg entry ~38¢), so the fee is trivial vs the edge.

## The numbers (Kalshi, `streak_trades.json`, BTC+ETH+DOGE, Apr 19 – Jun 23, n=674)
| entry band | n | win | net EV/trade |
|---|---|---|---|
| <35¢ | 81 | 35.8% | +5.97¢ |
| 35–42¢ | 78 | 47.4% | +7.42¢ |
| 42–48¢ | 96 | 49.0% | +1.80¢ |
| 48–52¢ | 84 | 60.7% | +9.10¢ (but at-50, fee-heavy, regime-unstable) |
| 52–58¢ | 130 | 64.6% | +8.36¢ (regime-unstable) |
| **58¢+** | 205 | 67.3% | **−0.85¢ (rich = already priced, SKIP)** |

**CHEAP (<48¢): n=255, avg entry 37.9¢, win 44.3%, net EV +4.81¢ = +12.7%/trade.**
- Payoff: win 62¢ / lose 38¢ → **no steamroller** (loss is small), full Kelly ~10.4%, ¼-Kelly ~2.6%/trade.
- vs the old blanket reversal (+2.2¢, +4.3%/trade): ~2.2× in cents, ~3× in return-on-stake.

## Why it's the signal, not generic longshot bias (key discriminator)
Generic cheap sides at the open with NO streak (BTC 30d, `cheap_vs_generic.py`):
| band | generic win vs price | generic EV |
|---|---|---|
| 35–42¢ | 36.4% (priced 39.5) | −4.79¢ |
| 42–48¢ | 40.0% (priced 45.3) | −7.05¢ |
**Generic cheap longshots LOSE on Kalshi.** Post-streak cheap wins **+9–11pp more at the same price** → the streak adds real, specific predictive power. This rules out the favorite/longshot-bias trap that killed earlier off-50 ideas.

## Robustness (Kalshi)
- **Threshold sweep:** <0.40→+4.7¢, <0.44→+5.4¢, <0.46→+5.6¢, <0.48→+4.8¢, <0.50→+5.0¢. Stable.
- **Split-half by period:** early +7.0¢ / late +1.6¢. Both positive but **decays late — yellow flag.**
- **Per-asset (fresh pulls):** SOL +15.6¢ (n=26), XRP +22.7¢ (n=27). BTC/ETH thin in the recent window but confirmed in the cache.
- **At-50/rich bins are regime-unstable** (positive in the 65-day cache, negative in the recent 11-day window) → only the **<48¢ off-50 region is stable across both.** Stick to cheap.

## Fill realism / adverse selection (`fill_realism.py`) — PASSED
- Entry +1¢ spread (taker) → +16¢; window VWAP fill → +10¢; only the literal worst tick in 90s → ~0¢.
- **Fill rate 65%** of streak signals have a real reversal-side trade ≤48¢ in the first 60s (beats the old ~35% guess).
- **Adverse selection exists but doesn't kill it:** filled-on-dip 57.1%/+12.7¢ vs held/rose 62.2%/+20.5¢. Both strongly +.
- Cheap entries are **real liquid trades** (median size 18 vs 20 baseline), 5/6 stayed cheap — NOT stale prints (`cheap_liquidity.py`).

## Breadth multiplier (the edge-increaser, `breadth_stack.py`) — directionally strong, n tiny
When ≥1 OTHER asset is in a same-direction 4-streak at the same time:
- breadth=0 solo: win 52.4%, +7.9¢
- **breadth≥1 confirmed: win 75.0%, +28.8¢** (n=24)
Matches the confirmed Polymarket cross-asset breadth finding ([[02 - The Confirmed Edge - Streak Reversal]]). Untested at scale.

## Mechanism (why it could be real)
Kalshi's 15-min markets open in a thin book. After a streak, retail/simple bots **over-price continuation**, leaving the reversal side cheap. Kalshi produces a cheap reversal entry **~38% of the time**; Polymarket's continuous CLOB does so **~7%** of the time → the **opening skew is Kalshi-specific** (Ryan's "priced by retail + simple bots" thesis). That's why Poly can't validate it — see [[10 - Stress Test & Verdict]].

## Daily-return math (`cheap_final_summary.py`)
~6.5 cheap signals/day across 5 assets, ¼-Kelly → **~2%/day expected** (high variance). NOT 10%/day from this alone. Levers toward 10%: breadth sizing, Polymarket frequency (but cheap fails on Poly), half-Kelly.

Related: [[02 - The Confirmed Edge - Streak Reversal]] · [[09 - Deep-Favorite Lock (DEAD)]] · [[10 - Stress Test & Verdict]] · [[11 - Session 2 Scripts & Data]]
