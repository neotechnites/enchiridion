# 14 - Data & Infrastructure (Session 3, 2026-07-22)

> Everything on disk after the overnight sprint. The plumbing is PAID FOR — every new idea rides on it for free. All in `~/kalshi_data/` unless noted. Machine quirks at the bottom (read before running anything).

## Per-second contract paths (the core asset — full-window data NEVER existed before this session)
| File | What | Era |
|---|---|---|
| `KXBTC15M_fullpaths.jsonl` | BTC full-window per-second bars | Jul 2–21 |
| `KXBTC15M_virgin.jsonl` | BTC merged virgin set (fullpaths + forward_lock late-window) | Jun 25–Jul 21 |
| `KX{ETH,SOL,XRP,DOGE}15M_virgin.jsonl` | alt late-window (≈3 pages/market) per-second bars | Jun 25–Jul 21 |
| `kx{eth,sol,xrp,doge}_ticks.json` | alt FULL-window ticks (legacy 4-tuple) | May 5–Jun 24 |
| `all_ticks.jsonl`, `older_btc_ticks*.json` | BTC late-window (~last 4 min only!) | May 25–Jun 24 / May 1–24 |

Format (new files): `{"ticker","open","close","K","result","nticks","secs":[[rel_s, px_cents, yes_taker_qty, no_taker_qty],...]}`. **The old BTC files only cover the final ~4 min — that limitation shaped every pre-session-3 result.** Kalshi trades retention ≈ rolling 10 weeks; to keep full-window history, re-pull forward regularly (`scripts/pull_full_paths.py`, v2 rate-limit-safe, ~8 req/s, newest-first, resumable).

## Derived analysis tables (rebuild with `build_events.py <tag> <paths> <underlying> [--legacy-kx|--legacy-allticks|--legacy-paths2] [--minspan=N]`)
- `{asset}_{fit|virgin}_cube.jsonl` — one row per market per minute-mark 1–14: price, fresh-print entry `pe` + `age` (entry realism), signed lag-safe Z, flow imbalance, crossings, forward prices. THE workhorse.
- `{asset}_{fit|virgin}_touch.jsonl` — every collapse-to-50 event + context + forward path + first-passage races (fp5/fp8/fp10 — never yet analyzed).
- Fit tags: `eth_fit sol_fit xrp_fit_kx doge_fit_kx btc_fit_late` (+ `xrp_fit/doge_fit` = paths2 Jun 13–23 only).

## Underlying & externals (all Apr 15 → Jul 21, `ts,close` CSVs)
`{btc,eth,sol,xrp,doge,paxg}_1min_full2.csv` (Coinbase) · `okx_btcusdt_1min.csv` (offshore) · `bitstamp_btcusd_1min.csv` · `upbit_krwbtc_1min.csv`+`upbit_krwusdt_1min.csv` (kimchi) · `dvol_btc_1min.json` (Deribit IV) · `okx_funding_{btc,eth}.json` (8h) · `mempool_fees.json`. Re-pull: `pull_underlying_full.py`, `pull_externals.py`.
**⚠️ LOOKAHEAD RULE: a 1-min candle stamped ts closes at ts+60 — always lag 60s.** This bug manufactured a fake 71%-win signal before the placebo caught it.

## Polymarket
`poly_{btc,eth,sol,xrp,doge}5m_full2.json` — outcomes [ts,up] Dec 2025→Jul 22 (btc 57.5k). `poly_btc5m_paths.json` — 912 CLOB 1s paths. `xo_okx1s.json` — 1s OKX spot around 912 Poly closes.

## Hourly ladders & other markets
- `KXBTCD_meta.json` / `KXETHD_meta.json` — all settled hourly markets w/ strikes & expiration values. `KXBTCD_cross.jsonl`/`KXETHD_cross.jsonl` — near-money trades, final-15-min windows. `hourly/` — obs tables (28k BTC / 20k ETH rows: price@T−55/30/15min + 20 lag-safe external features + outcome). Rebuild: `hx_run_all.py` → `hx_build_obs.py KXBTCD btc` → analyze `hx_analyze.py KXBTCD`.
- `settled_KXAAAGASD.json` + `trades_gas.jsonl` + `aaa_gas_series.json` (== expiration values 1148/1148) — the gas edge dataset.
- `settled_KXTEMP{NYC,CHI,AUS,LAX,DC}H.json` + `trades_temp.jsonl` — intraday temp lane.
- `KX*15M_mkts_full.json` — market lists w/ strikes, May 15→Jul 21, all 5 series.
- Ticker quirks: `KXBTCD-DDHH` = EDT hour, close = HH+4 UTC. Sports tickers are ET too.

## 🔴 LIVE PROCESSES — now SESSION-PROOF via launchd (2026-07-22)
**`com.nestor.machines` LaunchAgent** (`~/Library/LaunchAgents/com.nestor.machines.plist`) runs `scripts/nestor_supervisor.sh` every 5 min, resurrecting any dead machine (AbandonProcessGroup=true — required, else launchd reaps children). Survives session ends, reboots, account switches. Manage: `launchctl unload/load ~/Library/LaunchAgents/com.nestor.machines.plist`; health: `ps aux | grep -E "capture_kbt|deribit_gate_hourly|dutchbook_watch|ens_forward"` + `~/kalshi_data/supervisor.log`.
The four machines (each appends to its own JSONL, all resumable):
1. **`capture_kbt.py`** — 100ms L2 book capture (KalshiBackTest free tier, newest-50) → `kbt_books_*.jsonl`. The microstructure archive.
2. **`dutchbook_watch.py`** — PAPER dutch-book watcher, BTC/ETH/SOL, 4s poll in final 15 min of each hour, threshold-ordering guard, real touchable asks+sizes → `dutchbook_paper.jsonl`. First resolved fill WON (+0.58¢ as designed). THE capture-rate measurement.
3. **`deribit_gate_hourly.py`** — hourly Deribit-density vs Kalshi rung scan every 30 min + outcome resolution → `deribit_gate_hourly.jsonl`. Forward-validates the wrong-density gate.
4. **`ens_forward_capture.py`** — daily 13:00Z GEFS+ECMWF ensemble members + 9am Kalshi bucket prices, 6 cities → `ens_forward.jsonl`. NOTE: switch to 14:00Z in EST months.

## Third-party API keys (both Ryan's accounts, free tier)
- **KalshiBackTest** (`pdm_…` key, embedded in capture_kbt.py): free tier = newest 50 markets (~12h) of 100ms full-L2 books + coin price. Pro $19.90/mo = 31 days. Auth: `Authorization: Bearer`, base `api.kalshibacktest.com/v1/{coin}/15m/...`.
- **Predexon** (`pk_…` key, in scripts that use it): trades/orderbook history endpoints free but PATCHY (10/30 markets sampled; multi-day book holes). The complete product is paid Parquet orderbook deltas since Mar 5 at $80/GiB (~$5/day-slice filtered; $50 free credits if a card is added — never done).
- Everything else used is keyless (Kalshi official, Coinbase, OKX, Upbit, Bitstamp, Deribit, mempool.space, Open-Meteo, IEM, gamma/clob Polymarket).

## Harness scripts (the funnel machinery)
`batch_kills.py` / `batch_kills2.py` — multi-idea gate evaluation over lock candidates / sequence effects (extend these, don't write bespoke studies). `verify_composite.py fit|virgin` — frozen-rule one-shot verification. `verify_z50.py`, `flow_fade_test.py`, `sweep_touch.py` — control batteries. `gbt_at50.py`/`gbt_dissect.py` — GBT walk-forward + attribution (needs `.venv`). `lead_lag.py` — cross-asset 1s lead-lag (written, never run). `analyze_cross.py` — dutch-book detector. `bt_gas.py`, `ev_wing.py`, `xo_seam2.py`, `opening_secs/` — per-strategy backtests.

## Machine quirks (will bite you)
1. System `python3` = stdlib ONLY. numpy/sklearn live in **`~/kalshi_data/.venv/bin/python`**.
2. `urllib` SSL is broken → ALWAYS `curl -s -H "User-Agent: r"` via subprocess.
3. Kalshi rate limits poison naive pulls: a page is valid only if the parsed JSON has the expected key; throttle ~8 req/s; never mark failures as done (v1 of the puller wrote 6,339 empty markets before this was caught).
4. Disk is nearly FULL (~1-3GB free of 460GB — it's Ryan's other stuff, `~/Library/Caches` is 45GB). Store per-second bars, never raw ticks.
5. Background jobs get killed by sleep → wrap in `caffeinate -is`.
6. `~/.claude/agents/researcher.md` = cost-disciplined sub-agent type (Opus, high effort) — registers on session start.

Related: [[13 - Session 3 Findings (2026-07-22)]] · [[15 - Operating Manual (spin-up & method)]] · [[05 - Data & Scripts Reference]]
