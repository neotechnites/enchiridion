# 05 - Data & Scripts Reference

## Data files — `~/kalshi_data/` (persistent, in Ryan's home)
| File | What |
|---|---|
| `KXBTC15M_last30d_raw.json` | All 2,833 markets last 30d (full market objects: result, strike, times, prices) |
| `KXBTC15M_last30d.csv` | Tidy CSV of the above (settlement-level) |
| `KXBTC15M_trade_prices.csv` | Per-market YES price at checkpoints (yes_1/3/5/7/10/12 min) + result — coarse, all 2833 |
| `all_ticks.jsonl` | **Full tick-by-tick history, all 2,833 markets last 30d** — `{ticker,open,close,K,result,ticks:[[ts,price_cents,count,taker_is_yes],...]}` (199 MB) |
| `paths_sample.json` | 314-market sample of intraday paths (older, has K & close) |
| `btc_1min.csv` | BTC 1-min closes, recent ~30d (key=bucket-end unixsec, val=close) |
| `btc_1min_old.csv` | BTC 1-min closes, ~Apr 15 – May 26 |
| `eth_1min.csv` | ETH 1-min closes, recent ~30d |
| `eth_1min_full.csv` | ETH 1-min, full Apr–Jun (created by `assemble.py`) |
| `model_features.csv` | **5-month feature matrix for the combined model** (created by `assemble.py`) — check it exists |
| `KXBTC15M_fairvalue.csv`, `KXBTC15M_intraday_features.csv` | older intermediate analyses |

## Scripts — `~/kalshi_data/scripts/` (copied out of session scratchpad)
Run with `python3 ~/kalshi_data/scripts/<name>.py`. **Note:** machine's Python `urllib` SSL is broken — all scripts shell out to `curl`.

Key ones:
- `assemble.py` — builds `model_features.csv` (5mo markets + open quotes + BTC/ETH trailing + streak + vol). **Re-run if model_features.csv missing/incomplete.**
- `fit_model.py` — **the combined model.** Logistic regression, early/late OOS split, prints weights + OOS trading EV. Pure-python (no numpy).
- `streak_long.py` — streak→outcome signal over 5mo (results-only, very robust).
- `streak_ev.py` — tradeable EV of streak reversal at the open (pulls open quotes).
- `fullpull.py` — pulls full tick history → `all_ticks.jsonl` (resumable, ~15-20 min).
- `regime.py` — YES-overpricing by up-day vs down-day (regime control).
- `nearstrike_long.py`, `favside.py`, `comprehensive.py` — the offset/favorite/multi-feature mines.
- `mine.py` — calibration residual by side/hour/price/trend.
- `mm.py` — maker PnL to settlement (the +0.25¢ gross / 25%-fee analysis).
- Dead-end backtests (kept for reference): `scalp.py`, `tpharvest.py`, `minutebox.py`, `revert.py`, `dyn.py`, `pairs.py`, `ethodds.py`, `arb.py`.

## Added 2026-06-25 (Polymarket + external data)
- `poly_btc5m_full.json` — **49,720** Polymarket BTC 5-min resolved outcomes, Dec 2025–Jun 2026 (`[ts,up]`). Also `poly_eth5m.json`, `poly_sol5m.json`, `poly_xrp5m.json`, `poly_doge5m.json` (~30–48k each). The big OOS lab.
- `btc_1min_old.csv` — BTC 1-min ~Apr15–May26 (older period). `eth_1min_full.csv` — ETH 1-min full range.
- **External data sources (US-accessible, free):** Coinbase `PAXG-USD` candles = **gold 24/7** (geo OK); OKX `funding-rate-history?instId=BTC-USD-SWAP` = funding (Binance/Bybit geo-blocked). Polymarket: gamma-api `/events?slug=btc-updown-5m-<ts>` → market → `clobTokenIds`; clob `prices-history?market=<token>` for price paths. Series ids: btc-5m=10684, eth=10683, sol=10686, xrp=10685, doge=11325.
- Scripts: ~91 .py in `~/kalshi_data/scripts/`. Key new ones: `interaction_oos.py` (dual-period conjunction scan → found gold×BTC-drop), `gold_strat.py` (the gold edge backtest), `gold_offside.py` (off-50 dip = win 0%, the "cheapness is info" lesson), `poly_offside.py`/`strategy_backtest.py` (off-50 reversal cross-platform), `wide_scan.py`, `threeway.py`, `funding_okx.py`, `settlement_lock.py`.

## Useful constants
- "now" used in pulls = `2026-06-24`. 30-day window = 2026-05-25 → 06-24. Trades retained back to ~2026-04-18.
- KXBTC15M ≈ 96 markets/day.
- Fee: `0.07 × P × (1−P)` taker (P in dollars), maker = 25% of that. Spread = 1¢ mid-range.

## Added 2026-06-25 (Lock Edge — see [[08 - The Lock Edge - Settlement-Lock Favorite]] / [[09 - Lock Edge - Failure Rate & Sizing]])
- **New data:** `sol_1min.csv`, `xrp_1min.csv`, `doge_1min.csv` (Coinbase 1-min underlying, ~10d window); `KXXRP15M_paths2.json`, `KXDOGE15M_paths2.json` (Kalshi markets + YES price paths + **real `floor_strike`**); `poly_btc5m_paths.json` (912 Polymarket BTC 5-min CLOB price paths + outcome).
- **Pull scripts:** `pull_underlying.py` (SOL/XRP/DOGE Coinbase 1-min), `pull_xrp_doge.py` (Kalshi XRP/DOGE markets+trades→paths), `pull_poly_clob.py` (Polymarket gamma→clobTokenIds→prices-history).
- **Analysis scripts:** `lock_test.py`/`lock_validate.py` (first BTC lock tests, by norm-distance), `lock_strategy.py` (one-entry/market backtest), `lock_strict.py` (strict-norm sweep + loss autopsy), `lock_timeaware.py` (Z = dist/(median×√min-left)), `lock_xasset.py` (BTC+ETH+SOL), `lock_xrpdoge.py` (XRP/DOGE real-strike), `poly_lock.py` (Polymarket lock test), `price_z_grid.py` (price×Z grid + streak overlay), `master_sim.py` (5-asset pooled compounding), **`flip_rate.py` (TRUE failure rate by Z, 33k obs)**, `flip_corr.py` (flip time-clustering / correlation), `sim_mc.py` (Monte Carlo drawdown w/ measured flip rate).
- Key constant: `Z = |spot−strike| / (median_1min_move × √(minutes_left))`; trade band 93-97¢, Z≥4; measured flip rate 0.27% @ Z≥4.

## To rebuild from scratch (if data lost)
1. `pull_kalshi.py` → markets/results. 2. `trades_bt.py` → checkpoint prices. 3. `fullpull.py` → all_ticks. 4. Coinbase pulls inside `fairvalue.py`/`assemble.py` → BTC/ETH 1-min. Then `assemble.py` → `fit_model.py`.
