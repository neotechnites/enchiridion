# 01 - Goal & Market Mechanics

> ⚠️ **Goal statement below is superseded (2026-07-23).** The definitive goal is STAGED (Ryan verbatim, note 15): $1k → $10k (capacity edges 5-15%/day at small size) → $50k → steady $1k/day (2%/day) as income. The '10%/day needs leverage that ruins you' line was written when one edge existed; the current 5-system slate ([[18 - LIVE STATE (2026-07-23)]]) reaches the staged targets without ruinous sizing. Market-mechanics content below is still accurate.

## The market: `KXBTC15M`
- Kalshi series, title "Bitcoin price up down", frequency `fifteen_min`. A new market opens every 15 min (:00/:15/:30/:45).
- **Binary up/down:** strike = BTC price at the market's open. Resolves **YES** if BTC is at/above the strike at close (15 min later). So at open it's ~50/50 and the contract opens ~50¢.
- ETH analog exists: **`KXETH15M`** ("ETH 15M price up down"). Also `KXBTCD`/`KXETHD` (hourly above/below), `KXBTC`/`KXETH` (hourly range, mutually-exclusive buckets).
- Settlement source: **CF Benchmarks BRTI**, and crucially **the settlement value is the average of BRTI over the final 60 seconds** before close (not the spot at close). This averaging "locks" the outcome partway through the last minute — a potential edge area (untested cleanly; see [[03 - All Strategies Tested]] #settlement).

## Fees (decisive — they kill most strategies)
- **Quadratic / parabolic:** taker fee = `ceil(0.07 × P × (1−P) × 100)/100` per contract, **maxes at 50¢ (~1.75¢/contract), → ~0 near 1¢/99¢.**
- Maker fee = **25% of taker** (`1.75% × P × (1−P)`), and rounds toward 0 on small orders.
- Implication: round-trip taker scalp needs the price to move **~4–5¢ at mid-book just to break even**; near the extremes (90¢+) fees are tiny. **Trade near the edges = nearly free; trade at 50¢ = expensive.**
- Bid-ask **spread = 1.0¢** across the whole mid-range (10–90¢), ~0.1¢ at the extremes (measured).

## Market microstructure facts (measured)
- BTC at 1-min scale is ~a **martingale** (1-min autocorr ≈ 0, slight mean-reversion at 2–5 min). The 15-min *contract* whips around because near 50¢ it's **leveraged** to tiny BTC moves + bid-ask bounce — that craziness is amplified noise, NOT hidden BTC predictability.
- Contract tick-to-tick autocorrelation ≈ **−0.26** but that's **bid-ask bounce**, not tradeable reversion (fade-the-move loses even at zero cost).
- BTC/ETH 1-min return correlation ≈ **+0.89** (contemporaneous) but **no lead-lag** (neither moves first at 1-min scale) and the BTC/ETH spread is a **random walk (half-life ~51 hours)** → not cointegrated, no pairs convergence trade.
- Maker (passive) PnL holding to settlement ≈ **+0.25¢/contract gross**, **−0.28¢ net of full taker fee** but **+~0.12¢ net at the 25% maker fee** → MM is marginally viable but owned by faster players.

## API quick reference (`https://api.elections.kalshi.com/trade-api/v2`)
- Markets: `/markets?series_ticker=KXBTC15M&limit=1000&min_close_ts=&max_close_ts=&cursor=` (paginate via `cursor`). Fields: `ticker, result(yes/no), open_time, close_time, floor_strike, yes_bid_dollars, yes_ask_dollars, volume_24h_fp, ...`
- Trades: `/markets/trades?ticker=...&limit=1000&max_ts=<unixsec>&cursor=` — **`max_ts` trick:** `limit=1&max_ts=T` returns the last trade at/before T → the quote at any moment.
- Candlesticks: `/series/KXBTC15M/markets/<ticker>/candlesticks?start_ts=&end_ts=&period_interval=1` — has yes_bid/yes_ask, but **only retained ~1–2 days**.
- **Retention:** trades retained back to ~**April 18** (≈2 months); candlesticks ~1–2 days; settlement/markets data full.
- Python `urllib` SSL is broken on this machine → **shell out to `curl`** (`-H "User-Agent: r"`). Coinbase for underlying price: `https://api.exchange.coinbase.com/products/BTC-USD/candles?granularity=60&start=&end=` (max 300 candles/call; candle = `[time,low,high,open,close,vol]`, close=index 4, time=bucket start).

## The goal, stated precisely
- **Taker** strategy on KXBTC15M (Ryan is firm on taker, not MM, though limit-order entry is allowed/encouraged for the streak edge).
- Target ~**10%/day**. Reality from current edge: ~0.5–1.5%/day sane (see [[06 - Sizing & EV Math]]). 10%/day needs leverage that ruins you; the path to higher is **stacking more signals** (the combined model), not bigger bets.
