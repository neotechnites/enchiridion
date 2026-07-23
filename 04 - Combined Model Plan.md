# 04 - Combined Model Plan (the "kill" engine)

> ⚠️ **Historical (session 1/2 era).** The slate, goal, and verdicts have changed materially since this was written — [[18 - LIVE STATE (2026-07-23)]] wins every conflict (notably: lock edge = DEAD by decay; goal = staged $1k→$10k→$50k→$1k/day, not flat %/day; kill verdicts governed by the Kill Taxonomy in note 15).

## Ryan's thesis (correct, and the basis for this)
The 15-min crypto markets are priced by MMs + retail + a few small bots — **no BlackRock/Citadel quant desk.** Tight spreads stop arbitrage but do **not** make the price reflect: (1) current BTC, (2) this market's recent history/streaks, (3) what correlated cryptos are doing, (4) other easy-to-intake info. **Every one of those sharpens a model the market isn't fully using.** Combine them all → beat the naive price.

**The sharpest insight:** the "regime-conditional" strategies (⚠️ in [[03 - All Strategies Tested]]) didn't *fail* — they only work in certain regimes (e.g., YES-overpricing in down markets). **Feed the model a regime signal (trailing BTC trend + vol) and let it learn to weight those features conditionally.** Interaction terms (streak×vol, trend×streak) capture this. That converts the "partial" edges into conditional contributors stacked on top of the streak.

## Architecture
- **Decision point: the market OPEN** (strike = spot, price ≈ 50¢). Every feature is strictly *prior* info → zero lookahead, and the quote carries little info so the model's edge over the price = its predictive power.
- **Model:** regularized logistic regression → P(up). **Bet when P diverges from the open price past fees** (tune the threshold for trade-frequency vs per-trade-edge).
- **Features (all pre-open):**
  - Streak / run-length (the clean anchor) — `prevup` count of last 4
  - BTC trailing returns 15m / 1h / 4h + realized vol (price + regime)
  - ETH trailing returns 15m / 1h (cross-crypto) — extend to BCH/SOL
  - Time-of-day (sin/cos), day-of-week
  - Recent taker-flow imbalance (retail FOMO) — from trade `taker_outcome_side`
  - **Interactions:** streak×vol, streak×btc_r60, btc_r60×vol
- **Guardrails:**
  - **Lookahead:** open-time decision solves the offset-peek problem; keep BTC features strictly before open.
  - **Overfitting / regime-fooling:** train on **early months**, test on **late months** (out-of-regime split), L2 regularization, keep feature count modest vs ~6,000 markets.

## Status / how to resume
- **Data assembly job** (`assemble.py`) was running to build **`~/kalshi_data/model_features.csv`** (5 months: open quote + streak + BTC/ETH trailing + vol + time). **First check if that file exists & is complete** (cols: `ts,res,openq,prevup,btc_r15,btc_r60,btc_r240,vol,eth_r15,eth_r60,hour,dow`). If not, re-run `assemble.py`.
- **Fit script ready:** `~/kalshi_data/scripts/fit_model.py` — loads `model_features.csv`, builds features + interactions, standardizes, trains logistic (pure-python GD, no numpy on this machine), splits 60% early / 40% late, prints weights + OOS trading results at several thresholds. **Run it:** `python3 ~/kalshi_data/scripts/fit_model.py`.
- A **30-day proof-of-concept** already ran (`model.py`): OOS +0.96¢/trade, 61% win, 240 trades; weights — quote +1.75, offset +0.79, streak −0.25 (offset weight suspected lookahead-inflated → that's why the 5-month open-time rebuild).

## RESULT of first 5-month open-time fit (2026-06-24) — IMPORTANT
- `model_features.csv` built: **2,320 rows** (markets with an open quote, 5 months).
- OOS (train early 1,392 / test late 928): **weak & inconsistent.** Win ~48–49%, PnL flips sign by threshold (+0.28¢ @0.5¢ thr, −0.35¢ @1¢, +0.10¢ @2¢, +0.63¢ @3¢). Top weights: openq +0.40, eth_r15 −0.14, streak −0.14, btc_r240 −0.12, btc_r60 +0.10.
- **Diagnosis:** the linear logistic trades 500–700 markets and **dilutes** the streak edge, which lives almost entirely at the **4-in-a-row extremes** (~10% of markets). Generalizing it linearly across all markets washes it out.
- **Pivot:** treat the **streak as a discrete rule (fire only on 4-streaks)** — that's where the edge is (see [[02 - The Confirmed Edge - Streak Reversal]]). Use the model/other features to *filter or size* streak trades (e.g., "skip the streak trade when vol/regime is unfavorable"), or build a **nonlinear** model (trees / interactions on the extreme subset), NOT a global linear blend.

## Next steps after first fit
1. Read OOS weights — which signals carry independent info beyond price.
2. Add cross-crypto (BCH/SOL), taker-flow imbalance features.
3. Tune the bet threshold for the frequency/edge operating point Ryan wants.
4. Walk-forward validation (rolling retrain) before any real capital.
5. Combine the model's signal with the streak edge (streak is a feature; model generalizes it). Frequency target ~10+/day.
