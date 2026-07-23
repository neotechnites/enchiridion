# 09 - Lock Edge: Failure Rate & Sizing

> ⚠️ **SUPERSEDED — LOCK EDGE IS DEAD (DECAY KILL, 2026-07-23).** Everything below was true when written: the edge was real and the testing was honest. It then DECAYED under competition — the weekly by-week kill-scan measured +1.72¢/contract (first 6 wks) → −1.07¢/contract (last 4 wks). Per the Kill Taxonomy ([[15 - Operating Manual (spin-up & method)]]): decay ≠ conditional — gates can't fix competition. Verdict: BENCHED on the re-entry watchlist. Do NOT trade it, do NOT build it. Verdicts are dated — a builder reading only this note nearly implemented a corpse. Current state: [[18 - LIVE STATE (2026-07-23)]].

> The honest "how often does it actually fail?" answer for [[08 - The Lock Edge - Settlement-Lock Favorite]]. Measured on a BIG sample, not on the in-sample wins. Ryan's challenge ("0% drawdown? really?") was correct — this note replaces the small-sample "100% win" with the true rate.

## The method fix
You cannot estimate a failure rate from a pile of winning trades (189 wins, 0 losses tells you almost nothing). The failure rate is a property of the **physics** — does the underlying cross back through the strike before close — so measure it on **every market at every late checkpoint regardless of contract price.** That's ~33k observations across the 5 assets, vs the 189 that also happened to be priced 93-97¢.

## TRUE flip rate by lock strength (32,880 obs, late checkpoints sb≤120s)
`flip = leading side at checkpoint ≠ settled winner`. Pooled BTC+ETH+SOL+XRP+DOGE:

| Z (lock strength) | n | flip rate |
|---|---|---|
| 0-1 | 4,445 | 30.5% |
| 1-2 | 4,438 | 11.0% |
| 2-3 | 4,231 | 3.5% |
| 3-4 | 3,599 | 1.06% |
| **4-5** | 3,146 | **0.60%** |
| 5-6 | 2,626 | 0.38% |
| 6-8 | 3,864 | 0.26% |
| 8-12 | 4,024 | 0.12% |
| 12+ | 2,507 | 0.00% |

- **Cumulative Z≥4: 0.27% flip rate (44 / 16,167), Wilson 95% upper bound 0.37%.** ≈ **1 loss in 370 trades.** Not zero.
- **Perfectly monotonic** over 33k obs → clean signal, NOT noise. (Directly refutes a competing analysis that called lock-strength "non-monotone noise" — that test used Z≥2 / 96-99¢, the weak version.)

## EV with the true failure rate baked in
Break-even flip rate = `(1−entry)`-ish: at 95¢ ≈ 4.5%, at 93¢ ≈ 6.5%. Measured 0.27% ≪ both:
| entry | EV @ measured 0.27% | EV @ pessimistic 0.37% |
|---|---|---|
| 95¢ | +4.2% | +4.1% |
| 93¢ | +6.2% | +6.1% |

Edge is real and survives even the pessimistic statistical bound. Script: `flip_rate.py`.

## Correlation — the thing that actually sets safe size
A 0.27% per-trade rate is harmless **if flips are independent**. The danger is a flash crash flipping MANY simultaneous positions (all 5 cryptos move together). Measured the time-clustering of all Z≥4 flips (`flip_corr.py`):

- **20 Z≥4 flips total. 19 were isolated single-asset. 1 hit two assets (BTC+SOL, 06-18). 0 hit three+.**
- So in this 45-day sample the "everything crashes together" event **did not occur**; flips are nearly independent.
- **CAVEAT (the one real residual risk):** this window had no market-wide liquidation cascade. A true crash WOULD flip several assets at once and is NOT in the sample. That tail is unmeasurable here and is a **sizing** problem, not an edge problem.

## Monte Carlo drawdown (measured 0.27% flip, 16 trades/day, 252 days, 3000 paths)
`sim_mc.py`. (Ignore the compounding "final multiple" — fantasy; you'd hit liquidity/position limits. Read drawdown & worst-day.)

| sizing | median %/day | median maxDD | 95th-pct maxDD | worst day (5%) |
|---|---|---|---|---|
| 10% | +8.2% | 10% | 19% | −13% |
| 20% | +17% | 20% | 36% | −26% |
| 30% | +26% | 30% | 51% | −39% |

- Stress at flip=0.5% / 1.0%: still strongly +; drawdowns scale up modestly.
- **Correlated-crash stress** (1.5% of days, half that day's positions flip together — a scenario NOT seen in-sample): 10% sizing → 58-82% drawdown; 30% → near-wipeout. This is what aggressive sizing risks in a real crash.

## Sizing conclusion
- The binding constraint is **correlation during a crash**, not the per-trade rate.
- **Treat overlapping-window positions across the 5 assets as ~one bet.** Cap per-cluster exposure ~**10-15%** of bankroll.
- At ~12-15% sizing: **~10%/day median in normal conditions** with ~15-25% routine drawdowns, and a real crash day costs a painful-but-recoverable hit instead of the account.
- Do NOT size by per-trade Kelly (≈95% — absurd, because the edge dwarfs the tiny payout); the catastrophe + model risk on the crash tail govern.

## Status
- Failure rate: **measured, real, tiny (0.27% @ Z≥4), monotonic.**
- Per-trade EV: **solidly + even at pessimistic bound.**
- Drawdown: **routine 10-36% at 10-20% sizing; crash tail unmeasured → cap cluster exposure.**
- Unresolved: live fill depth in the last 2-4 min (no order-book data). Forward paper-test before real size.

Related: [[08 - The Lock Edge - Settlement-Lock Favorite]], [[06 - Sizing & EV Math]], [[07 - Overfitting & Validation Discipline]].
