# 03 — REDIRECT: Build Streak ≤44¢ (2026-07-23)

> **This file is the current build order for `~/Documents/senate/nestor/`. It supersedes the README's sleeve plan, [[00 - Implementation Overview]], and any docs/tickets that conflict. Written 2026-07-23 by the research session after reading your actual code (crates/engine, crates/lock, crates/weather, nestor_bin). Research grounding: [[18 - LIVE STATE (2026-07-23)]].**

## Why the plan changed (verdicts are dated — this is the lesson)
- **Lock edge: DEAD by DECAY.** It was real when you built it (the 2026-07-15 forward test was honest). The weekly by-week kill-scan then measured it decaying: **+1.72¢/contract (first 6 wks) → −1.07¢/contract (last 4 wks)**. Competition ate it. Do not run it. Keep the crate; it's benched with re-entry criteria in the vault.
- **Weather: UNVERDICTED.** The +8.2¢ figure is backtest-only; live forward capture (a separate research machine, `ens_forward_capture.py`) started Jul 2026 and needs ~3-4 more weeks. Do not finish calibration/bias work; do not run it. If it verdicts TRADE, [[01 - Weather Sleeve Spec]] resumes as written.
- **Streak: PROMOTED — this is BUILD #1.** The old "streak = break-even after fees" result was the unconditional ~50¢-entry version. The current rule is **price-gated at ≤44¢** and is 2-yr regime-proof: **56-57% win in every yearly/half slice, on both BTC and ETH**, fees included (vault notes 13/18). It is also the ideal first live build: highest fill frequency, simplest order shape, one taker order, hold to settle.
- **Purpose of going live: MECHANICS, not efficacy.** One week at ~$100 = the mechanics degree (order lifecycle, real fills, fees, settlement). Efficacy is already proven from data. Do not design anything that "tests whether the strategy works" — design everything to *measure execution quality*.

## What to do with the existing code
- **`crates/engine` — KEEP 100%, unchanged in role.** Kalshi client, Strategy trait, Engine::execute, RiskManager, reconcile, state store + single-writer lock, selftest-order: all exactly what streak needs.
- **`crates/lock` — PARK.** Remove it from the `run` runtime (it is currently the foreground always-on task in `run_all`). Keep the crate compiling (it costs nothing and `backtest-lock` stays useful as a re-entry check), but nothing schedules it.
- **`crates/weather` — PARK.** Remove/disable the 9am ET spawn in `run_all`. Keep the crate and `probe-weather`/`calibrate` commands for when the forward verdict lands.
- **`nestor_bin` — rewire `run`:** settlement sweep (60s) stays; **streak becomes the always-on foreground scanner** (the slot lock occupies today) — but NOT at lock's 15s cadence. **Poll at 1-2s.** The entry window is only 60s; 15s polling gives ~4 looks at the ask, 1s gives 60. Streak is 2 requests/pass (one per series) = ~2 req/s, well inside Kalshi's public rate limits (research self-throttles at ~8 req/s; 429s are real above that). Outside entry windows a lazy cadence (10-15s) is fine — the fast poll matters in the 60s after each 15-min boundary.
- Do not touch anything in `~/kalshi_data/` — that's the research infrastructure, separate system.

## The strategy: `crates/streak` (implements `Strategy`, like the others)

**Rule in plain English:** In Kalshi's 15-minute crypto up/down markets (**KXBTC15M** and **KXETH15M**), when the last four settled windows all printed the same direction (4 ups or 4 downs), buy the **opposite** side of the new window — but **only during the new market's first 60 seconds**, and **only if that side's ask is ≤ 44¢**. Taker only. Hold to settlement. No exits, no averaging, one order per market.

**Signal detection (do it from Kalshi settled results — NOT candles):**
1. Per series, fetch settled markets (`probe_series(series, "settled", ~8)`), sort by `close_time` desc, take 4.
2. All 4 must have non-empty `result` and be **consecutive 15-min windows** (close_times exactly 15 min apart). Any gap/missing → no signal.
3. All 4 results equal ("yes"×4 → streak UP → buy **NO** on the current market; "no"×4 → buy **YES**).
4. Current market: the open market in the series whose window just began. **Entry-window check: time-to-close ≥ 14 minutes** (equivalently: within 60s of open, and the newest settled market's close == current market's open). Past the window → skip, wait for the next streak.
5. Price gate: reversal side's ask (from `/markets`, `yes_ask_dollars`/`no_ask_dollars`) **≤ 44¢**. Ask above 44 → skip entirely (never bid below the ask waiting — taker-only doctrine, resting orders are informed-flow magnets).
6. Place `limit buy` at the current ask via `Engine::execute` (the deterministic `client_order_id = strategy-ticker` dedupe already guarantees one order per market). If unfilled because the book moved, do not chase and do not re-place — a missed fill is DATA (log it).

**Why candles are banned for detection:** the research burned itself once on 1-min candle lookahead (candle stamped `ts` is final at `ts+60`) — a fake 71% signal. Settled-market `result` fields have no such trap.

**Risk wiring (use what exists):**
- `SizingHint::Flat`. Week-1 config: `NESTOR_BANKROLL=100`, `flat_usd = 4.0`, `daily_budget_usd = 60.0`, keep the drawdown/daily-loss halts as configured.
- **Cluster key = the window close timestamp, shared across coins**: `cluster = format!("streak-{}", close_unix)`. BTC and ETH move together; simultaneous entries are ONE bet (vault correlated-tails doctrine).

## EXECUTION TRUTH — fix these BEFORE anything trades live (bugs confirmed by code-read 2026-07-23)

**Bug 1 — accepted ≠ filled (`Engine::execute`, crates/engine/src/strategy.rs).** Live mode calls `risk.on_fill()` immediately after Kalshi ACCEPTS the order, assuming a full fill at the limit price. No fill is ever verified. Consequences: a moved ask leaves an unfilled/partial limit resting while nestor believes it holds the position — state, P&L, cluster caps, and the kill-switch are all fed fiction, AND the stranded resting order violates the taker-only doctrine (resting orders are informed-flow magnets). Required fix:
- After placement, poll the signed fills endpoint (`GET /portfolio/fills`, filter by order/client_order_id) until the order is fully filled OR the entry window closes.
- Record actual `fill_price`, `filled_count`, `ts_fill` from the API — never assume the limit price.
- Partial fill → `risk.on_fill` with the FILLED count only.
- Not fully filled by window close (or a few seconds, whichever comes first) → **cancel the remainder** (`DELETE /portfolio/orders/{order_id}`) and record the miss. Never leave a resting order alive.
- Paper mode keeps simulating fills but every simulated record carries `simulated: true`.

**Bug 2 — no fee accounting.** `Order::stake()` is `count × price` with no fee, so paper P&L and reconcile overstate results. Kalshi taker fee = **ceil-to-next-cent of `0.07 × count × P × (1−P)` per ORDER** (P in dollars; at 44¢ ≈ 1.73¢/contract). Add it to fill accounting in both paper and live/reconcile paths. The week's P&L numbers are worthless if fees aren't in.

**Latency timestamps.** Record `ts_signal`, `ts_submit`, `ts_ack`, `ts_fill` (ms) on every order — submit→ack and ack→fill latency are week-1 deliverables.

## DATA CAPTURE — nestor keeps everything it generates (Ryan-ordered 2026-07-23)
Nestor's data job = **participation record + decision context** (the research machines own the observational firehose separately — do not duplicate them):
1. **Observation log:** every poll the scanner makes appends one compact JSONL line (`ts_ms, ticker, yes_ask, no_ask` in deci-cents) to a daily file, e.g. `data/obs/YYYY-MM-DD.jsonl`. ~10-20MB/day raw. Nothing nestor sees is discarded.
2. **Decision snapshot:** at every signal (traded or skipped or risk-rejected), fetch and store the order book (`GET /markets/{ticker}/orderbook`) alongside the signal record — one extra request at decision moments only.
3. **Participation record:** the week-1 JSONL below, for every signal forever (not just week 1).
4. **Nightly compression job:** gzip/zstd yesterday's logs (10-20× shrink). Keep everything; delete nothing.

## Week-1 mechanics logging (the actual deliverable)
Every signal — filled or not — appends a JSONL record (e.g. `data/streak_week1.jsonl`) with:
`ts_signal, ticker, series, streak_dir, side_bought, ask_at_signal (deci-cent), limit_placed, ts_submit, ts_ack, filled (bool), partial (bool), ts_fill, fill_price, filled_count, canceled_count, fee_cents, ts_settle, result, pnl_cents, simulated (bool), reject_reason (risk rejections too)`
End-of-week report answers, with numbers: **(1)** fill realization (fills ÷ signals — research assumed 60%), **(2)** slippage (fill price vs ask-at-signal), **(3)** signal frequency/day per coin vs backtest expectation, **(4)** fee per trade actual vs formula, **(5)** settlement timing + reconcile correctness, **(6)** win% (context only — one week is noise; do NOT judge the edge on it).

## Rollout order
1. Build `crates/streak` + rewire `run`. **Paper mode 1-2 days**: confirm signal detection fires at plausible frequency, entry-window and consecutiveness logic correct, no dupes.
2. Ryan creates Kalshi API keys (Settings → API Keys) → `selftest-order` one $1 round-trip (already specced in your tickets).
3. **Live, $100, 7 days.** Then the mechanics report, then research decides build #2 (unified vol book is next in line).

*Standing rule going forward: before implementing ANY strategy, check [[18 - LIVE STATE (2026-07-23)]] (or its successor) for the latest verdict + kill-scan — research verdicts change weekly by design.*
