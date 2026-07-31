# 01 — Weather Sleeve Spec (FIRST BUILD)

> ⏸️ **ON HOLD (2026-07-23; still parked 2026-07-31).** Edge unverdicted: forward capture (`ens_forward_capture.py`, daily 13:00Z) matures ~mid-Aug — tracked in [[38 - The Strategy Book (living inventory)]]. If it verdicts TRADE, this spec becomes the build plan as written. Until then: no code. (Streak, the former build #1, was built and went live 2026-07-25.)

> The first live component of Nestor Core. Forward-confirmed 2026-07-15 (HELD: 6/6 tradeable cities positive, precip + MAE→EV filters persisted). Grounded in [[08 - Broad-Kalshi & Cross-Venue]] (weather sections) and the forward test. Go-live: **live at tiny size**, VPS, daily.

## The strategy (exact, as forward-tested)
Once per day, at **09:00 ET (13:00 UTC)**, for each tradeable city:
1. Pull the **morning-of forecast** daily-max from Open-Meteo (as-issued run, no lookahead).
2. **Bias-correct** it per city (subtract that city's trailing mean forecast error).
3. Map corrected temp → the **2°F Kalshi bucket** it falls in.
4. **Filters:** skip if the city's forecast-MAE ≥ ~2°F (untradeable), and skip if **forecast precipitation > 0** (wet days kill the edge: dry +16.7¢ vs wet +1.2¢ forward).
5. **Buy** that bucket's YES at the current Kalshi ask, tiny size, **hold to settlement** (NWS daily climate report, ~next morning).

No intraday nowcasting, no fading the market — the forward test showed both add nothing. The edge is purely the **bias-corrected early-entry forecast** before the market converges (by ~1pm ET the market is calibrated and the edge is gone).

## Cities (tradeable-6 + negative controls)
| City | Kalshi series (VERIFY LIVE) | lat, lon | IEM station | 2yr MAE | Trade? |
|---|---|---|---|---|---|
| Miami | KXHIGHMIA | 25.79, -80.29 | KMIA | 1.03 | ✅ |
| Atlanta | KXHIGHTATL* | 33.63, -84.44 | KATL | 1.32 | ✅ |
| New York | KXHIGHNY | 40.78, -73.97 | KNYC | 1.54 | ✅ |
| Boston | KXHIGHTBOS* | 42.36, -71.01 | KBOS | 1.65 | ✅ |
| Phoenix | KXHIGHTPHX* | 33.43, -112.00 | KPHX | 1.71 | ✅ |
| Chicago | KXHIGHCHI | 41.79, -87.75 | KMDW | 1.85 | ✅ |
| Denver | KXHIGHDEN | 39.85, -104.66 | KDEN | 2.65 | ❌ control |
| Seattle | KXHIGHTSEA* | 47.44, -122.31 | KSEA | 2.51 | ❌ control |

\* The forward-test agent saw a mix of `KXHIGH<city>` and `KXHIGHT<city>` tickers — **must confirm each exact series ticker and its settlement station via `/events` / `/markets` at build time** before trading. Getting the station wrong = settling against the wrong truth.

## Data feeds
- **Forecast (live, morning-of):** `https://api.open-meteo.com/v1/forecast?latitude=<lat>&longitude=<lon>&daily=temperature_2m_max,precipitation_sum&forecast_days=1&timezone=America/New_York&temperature_unit=fahrenheit`. Free, no key. (Backtest used the `historical-forecast-api`; live uses the plain `forecast` endpoint — confirm it serves the 00Z morning run at 9am ET, not a later-updated run.)
- **Bias calibration + settlement truth:** IEM `https://mesonet.agron.iastate.edu/api/1/daily.json?station=<STN>&network=<NET>&...` → official daily max = Kalshi result. Recompute each city's bias from a trailing window (e.g. prior 60–90 days) on a schedule; store per-city bias in config.
- **Kalshi market + entry price:** list buckets via `/markets?series_ticker=<S>&status=open` → each market = one 2°F bucket with `floor_strike`/`cap_strike`, `yes_ask_dollars`. Fee `0.07·P·(1−P)`.

## Bias correction (the actual edge)
Raw Open-Meteo runs ~+1.5°F hot. Per city: `corrected = raw_forecast − bias_c`, where `bias_c` = mean(forecast − IEM_actual) over the trailing calibration window. Store as `{city: bias}`; refresh weekly. This single step is most of the edge (NY MAE 2.07→1.52 corrected).

## Sizing (tiny live to start)
- Flat dollar per trade, NOT % of bankroll (markets cap at hundreds–low-thousands). 
- **Start: $10 stake per city-trade.** ~5–6 dry tradeable-city trades/day → ~$50–60/day at risk. Scale up only after live P&L confirms fills + edge.
- Hard caps: max 1 bucket per city per day; max $X total/day (config); never exceed a market's visible depth.

## Daily flow (the cron job)
```
09:00 ET:
  refresh per-city bias if stale
  for city in tradeable-6:
    fc, precip = openmeteo(city)
    if precip > 0: skip (log "wet skip")
    t = fc - bias[city]
    bucket = floor/cap bucket containing t
    mkt = find open Kalshi market for (series, bucket) today
    if mkt and 2c < yes_ask < 98c:
      buy YES, stake=$10, log(city, fc, t, bucket, ask, mkt)
next morning:
  fetch IEM actual + Kalshi settlement; reconcile; log realized P&L
```

## Logging (mandatory — this is how we confirm forward persistence)
Every trade: date, city, raw_fc, bias, corrected_t, predicted_bucket, entry_ask, precip, dry-flag, filled_qty, then actual_temp, settled_bucket, win/loss, net_$. Weekly: per-city hit%, EV, forecast-MAE vs the 2yr ranking, dry-vs-wet. This log IS the live forward test.

## Go-live checklist
- [ ] Kalshi API keys generated + auth working (see [[02 - Setup - Kalshi API & VPS]]).
- [ ] Confirm each city's exact series ticker + settlement station.
- [ ] Confirm live Open-Meteo `forecast` endpoint = morning 00Z run at 9am ET.
- [ ] Per-city bias computed from IEM trailing window.
- [ ] Order placement tested with a single $1 trade (round-trip: place → confirm → settle → reconcile).
- [ ] Cron scheduled 09:00 ET on the VPS with retry/alerting.
- [ ] Kill-switch: pause on auth failure, unexpected fill price, or daily-loss breach.

## Known caveats (don't design them away)
- The forward window's big weather profit was a **NY/BOS heat wave** (+102%/trade with them, +5.7% without). Expect the robust ~+5¢/trade, treat windfalls as luck.
- Capacity is real — this sleeve makes hundreds, not thousands. It's the satellite, not the engine.
- 20 days / one season of pricing data. The live log is the real test.

Related: [[02 - Setup - Kalshi API & VPS]] · [[08 - Broad-Kalshi & Cross-Venue]]
