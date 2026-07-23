# Nestor — Implementation Overview

> 🛑 **STOP — THIS BUILD PLAN IS SUPERSEDED (2026-07-23, Ryan's direct order). Read [[18 - LIVE STATE (2026-07-23)]] + note 15 (Kill Taxonomy + Live-testing doctrine) before writing ANY code.** This folder was specced when lock + weather led the board; both verdicts have since changed: **Lock sleeve = DEAD (decay kill: weekly kill-scan measured +1.72¢→−1.07¢/contract; the forward test below was honest but the edge was subsequently eaten — do not build).** **Weather sleeve = UNVERDICTED watchlist** (backtest +8.2¢, forward capture via ens_forward_capture.py only started; 3-4 wks to verdict — do not build yet). **The 'Streak = break-even, not trading' line below is WRONG under the current rule:** streak **≤44¢** (price-gated) is 2-yr regime-proof and is **BUILD #1** — one week at ~$100 live = the mechanics degree (order lifecycle, fees, fills), then every later strategy needs only a 2-3-day fill-probe. Lesson encoded in note 15: verdicts are DATED — always check the latest kill-scan before implementing anything. [[02 - Setup - Kalshi API & VPS]] remains fully valid — use it for the streak build. **The full build order lives in [[03 - REDIRECT - Build Streak (2026-07-23)]] — follow that file.**

> Start here. This folder specs the live trading system. It is grounded in the **forward test run 2026-07-15** (~20 days of true out-of-sample data, Jun 25 → Jul 15) that confirmed which vault edges actually held. See [[00 - START HERE]] for the research; this folder is the build.

## What we are building
**Nestor Core** — a live Kalshi trading bot running two uncorrelated edges off one bankroll, sized by the vault's safe-sizing doctrine ([[09 - Lock Edge - Failure Rate & Sizing]], [[12 - Independent Verification (external review)]]).

| Sleeve | Edge | Forward verdict | Role |
|---|---|---|---|
| **1 — Lock (primary)** | Buy BTC favorite 93–97¢ when Z≥4 clear of strike, ~2–4 min left, hold to settle | ✅ HELD — 138 trades, 99.3% win, +3.25%/trade, flip rate 0.06% | The dependable engine (~1.1%/day, ~5% DD at 5% sizing) |
| **2 — Weather (satellite)** | Bias-corrected Open-Meteo max → 2°F bucket, buy 9am ET, hold to settle | ✅ HELD (magnitude regime-dependent) | Uncorrelated kicker; capacity-limited, flat small size |

**Explicitly NOT trading:** Gold×BTC-drop (decayed to noise, at-50, high variance) and Streak reversal (break-even after Kalshi fees). Revisit only if forward evidence changes.

## The forward-test result this rests on ($1000, 20 days)
- Lock sleeve @5%/trade compounded: **$1000 → $1,250** (+25%), max drawdown 5%. Dependable.
- Weather sleeve: robust ~+$300 flat-sized (the raw +$8k was a one-off NY/BOS heat wave — do NOT design around it).
- Combined realistic: **~$1,250–1,550**, of which the lock +$250 is the trustworthy core.

## MUST-RESOLVE before real capital (the vault's own open questions)
These are the only things historical backtests could not answer. The build has to close them:
1. **Order-book fill depth at the exact entry moment.** Backtest used last-trade + 0.5¢. Need to confirm 93–97¢ is fillable at size in the final 2–4 min. → live order-book polling + a paper-fill log (Kalshi `GET /markets/{ticker}/orderbook`; or keyed BackTest API / Predexon per [[12 - Independent Verification (external review)]]).
2. **The all-assets-at-once crash tail.** Never observed in-sample; it's the thing that caps size. → hard cluster cap + a kill-switch, not a hope.
3. **Forward persistence.** All data is one ~2-month regime. → run in PAPER first, log everything, only size up once live P&L confirms.
4. **Execution plumbing** (Kalshi auth + order placement) — commodity, but must be built and tested.

## Sizing doctrine (locked from research)
- 5% of bankroll per lock trade; treat all correlated positions in one 15-min window as ONE bet; cap that cluster at 10–15%.
- Weather: flat small dollars (markets cap at hundreds–low-thousands), NOT % of a compounding bankroll.
- NOT per-trade Kelly (it returns ~95%, absurd). Single-digit % per trade. The crash tail governs, not the odds.
- Kill-switch: halt on a cluster flip / drawdown breach.

## Decisions (locked 2026-07-21)
- **Build order: WEATHER FIRST**, then add the Lock sleeve. (Weather is daily, no last-2-min fill timing, so it's the simpler thing to get live.)
- **Hosting: cloud VPS**, always-on.
- **Kalshi API: not set up yet** → the setup doc specs the auth + key-generation flow.
- **Go-live: LIVE at tiny size** (not paper). Acceptable for weather because its daily markets are deep enough that fills aren't the risk the last-2-min lock entry is. Tiny size closes the forward-persistence + real-fill gaps with real money on the line, cheaply.

## Spec docs
- `01 - Weather Sleeve Spec.md` — ✅ FIRST BUILD. Forecast pull, bias correction, bucket mapping, city/precip filters, entry timing, sizing, go-live checklist.
- `02 - Setup - Kalshi API & VPS.md` — auth/key generation, VPS provisioning, stack, scheduling, secrets.
- `03 - Lock Sleeve Spec.md` — (later) exact signal, Z computation, order-book fill checker, entry/exit.
- `04 - Risk & Sizing Spec.md` — bankroll math, cluster caps, kill-switch, position limits.
- `05 - Architecture.md` — shared components once both sleeves exist.

Related: [[00 - START HERE]] · [[08 - The Lock Edge - Settlement-Lock Favorite]] · [[08 - Broad-Kalshi & Cross-Venue]] (weather) · [[09 - Lock Edge - Failure Rate & Sizing]] · [[12 - Independent Verification (external review)]]
