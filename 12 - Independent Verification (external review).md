# 12 - Independent Verification & Reality Check (external review)

> ⚠️ **Historical (2026-07-15 era).** The METHOD here (independent re-derivation, adversarial re-test) remains the standard; the CONCLUSIONS reviewed the OLD slate (lock/weather/gold/streak-at-50). Lock has since decay-died; the current 5-system slate post-dates this review entirely. Its 'realistic 0.5-3%/day' figure applies to that old slate, not the current one — current numbers and the staged goal live in [[18 - LIVE STATE (2026-07-23)]] and note 15.

> Written 2026-06-25 by a separate Claude acting as an **independent verifier** (not one of the strategy-hunting sessions). Goal: re-derive every claimed edge from the raw data WITHOUT trusting the other notes' scripts, pull new data where possible, and simulate the risks that historical backtests can't show. **This note is self-contained — a fresh Claude can read it cold.** Where it contradicts an optimistic claim elsewhere, trust the number here (it was reproduced from scratch).

## TL;DR (read this first)
- **Every edge that the project marks as "verified" reproduced independently. Nothing broke under re-derivation.** That is a genuinely good sign.
- **The realistic return is ~0.5–3%/day sized safely, NOT the ~10%/day some notes claim.** 10%/day requires sizing that blows up in the first market-wide crash.
- **Two famous claims were REFUTED on independent re-test** (see "Corrections"): the "cheap-entry filter triples the edge" and the "regime-gated favorite walk-forward verified." Don't resurrect them.
- **The bottleneck is NOT writing code or finding alpha — it is two empirical numbers** that only live data answers: (1) fill depth at the exact entry moment, (2) the all-assets-at-once crash tail. Everything else is settled.

## How this was verified (the standard)
For each edge: reload the on-disk data, recompute win rate / EV with confidence intervals, and demand a **true out-of-sample** check (different time period, different platform, or walk-forward — NOT a split of the same fit window). Then stress the parts a price-only backtest hides: real entry price, fees, fill depth, and crash correlation.

## Edge-by-edge verdict

### 1. Streak / cross-asset breadth reversal — REAL but THIN, Polymarket-only
- Independently recomputed on the 49,720-market Polymarket BTC 5-min sample: 3-streak reversal **52.2% (z=4.7)**, holds in both time-halves. The famous "4-streak" is weaker (z=2.4). True magnitude ~52%, not the 55–59% small samples showed.
- Cross-asset breadth ≥15: picked the threshold on the early 60% of data, locked it, tested on the held-out late 40% → **55.9% (z=2.3). Did NOT collapse.** Survives overfit discipline.
- **Tradeable economics (real CLOB entry prices, n=334):** reversal side opens ~53¢ (market pre-prices ~60% of the edge). Win 55.4% at real entry → **Polymarket +2.1¢/contract (+3.9%/trade); Kalshi ≈ break-even after fee.** Polymarket-only.

### 2. Gold × BTC-drop — REAL, the cleanest Kalshi-positive edge
- Buy UP at a KXBTC15M open when BTC fell last hour (low tercile) AND gold/PAXG rose (high tercile). Re-pulled both periods independently: **recent 61.3% win / +6.1¢; older (true OOS) 57.8% / +2.2¢.** Both >50%, both +EV after fee, consistent across two different months. Enters ~54¢ (at-50, high fee, high variance). Thin OOS margin (~1.5σ over breakeven) but it holds.

### 3. Lock edge / deep-longshot fade — REAL, best-supported off-50 edge
Rule: late in a 15-min market (~2–4 min left), buy the favorite at **93–97¢** when the coin is **Z≥4** normal-moves clear of the strike (`Z=|spot−strike|/(median_1min_move·√min_left)`); hold to settle.
- **Flip-rate physics reproduced exactly:** 32,880 observations, flip rate falls cleanly monotonically 30%→0.27% as Z rises; **Z≥4 = 0.27% flip.** Real, not noise.
- **Tradeable rule reproduced:** BTC 93–97¢ & Z≥4 → 100% win, n=76, +4.2%/trade. **OOS (separate May pull): 98.7% win, n=303, +3.1%/trade.** Holds out-of-period.
- **Structure = selling deep insurance.** At 95¢ you risk 95¢ to make ~5¢; breakeven win rate ~95%. The edge is the ~3pp gap between realized (~98–99%) and breakeven. Each loss wipes ~30 wins.
- **FILLS (measured 2026-06-25, NEW):** Kalshi's own live order-book endpoint shows **~6,900 contracts resting in the tradeable 3–7¢ cheap band** (= buying a 93–97¢ favorite), plus tens of thousands at the 99¢+ extreme. The "thousands resting / transactable" claim is **confirmed with real data** (was previously only asserted — no script ever pulled the book). Caveat: one early-life snapshot, not the final-2-min entry state.

### 4. Weather forecast-buy — REAL, the most independent edge
- Bias-corrected Open-Meteo daily-max forecast → predicted 2°F Kalshi bucket → buy at 9am ET, hold to settle. **Reproduced exactly: 487 trades, +6.17¢/trade (~+17%), 6 of 8 cities positive** (negative only DEN/SEA = the bad-forecast cities; city filter is a-priori via forecast-MAE, not P&L-fitting).
- **Lookahead ruled out (independent check):** Open-Meteo archived forecast vs actual = 1.36°F MAE, only 4/51 days match exactly. True lookahead would give ~0 MAE / ~100% hits. It's a genuine forecast.
- Uniquely, the *prediction* side is validatable over 2 years of weather archives (crypto markets are only 8 weeks old). Uncorrelated with crypto. Limits: pricing side still ~70 days of Kalshi data; thin markets cap size to hundreds–low-thousands $/market.

## CRASH-TAIL SIMULATION (NEW, 2026-06-25) — the risk backtests can't show
The lock edge's danger is a market-wide crash flipping many simultaneous 95¢ positions. Pulled **300 days of BTC 5-min** (Aug 2025–Jun 2026, incl. a real **−5.36% 15-min crash** on 2025-10-10), reconstructed synthetic 15-min markets, counted lock-favorite flips (`crash_sim.py`, data `btc_5min_long.json`):
- **Max CONSECUTIVE BTC lock-favorite flips, even through the −5.36% crash: 2.** Max in any rolling 60-min window: 2.
- **Why it's not worse:** a sustained move *resets each new window's strike*, so trends do NOT chain-flip lock favorites — only intra-window *whipsaws* flip, and they're localized. This **downgrades** the vault's earlier "58–82% drawdown" fear.
- In-sample 5-asset clustering (`flip_corr.py`): 20 flips total, 19 isolated, **1 two-asset cluster, 0 three+.**
- **The residual tail:** a true market-wide *whipsaw* hitting all 5 correlated cryptos' late-window favorites in ONE window (~5–10 simultaneous flips) has **never appeared** in any data — but isn't impossible. That single unobserved event is what caps safe size.

## SIZING — data-grounded (supersedes "10%/day at 12–15%")
- Do NOT use per-trade Kelly here: it returns ~94% of bankroll (absurd) because it assumes the 0.27% loss is the whole story; the real risk is correlated co-flips it can't see.
- Worst *observed* cluster (2 co-flips) at 5%/trade ≈ a 10% hit — survivable. The wipeout cases need ~10 simultaneous flips (never observed).
- **Recommended: ~3–5%/trade, and treat all 5 assets in the same 15-min window as ONE bet (cap that cluster ~10–15% of bankroll).** This survives all 300 days of history including the −5.36% crash.
- **Realistic return at safe sizing: ~1–3%/day median, with ~1-in-3 losing days and the occasional sharp drawdown.** Excellent, but not 10%/day.

## CORRECTIONS — claims that FAILED independent re-test (do not resurrect)
- **"Cheap-entry filter triples the streak edge to +6.6¢" — FALSE.** Independently, entries ≤52¢ won only 48.8% (EV +0.3¢), BELOW the unfiltered set. Was p-hacked (pooled, ~2.7σ). [[02 - The Confirmed Edge - Streak Reversal]] overstates this.
- **"Regime-gated favorite, walk-forward verified" — NOT a real gate edge.** Reproduced the walk-forward, but the gate's lift over simply buying ALL favorites in the held-out third was **−0.5¢** (it slightly HURT). The whole result was a recent favorable favorite-underpricing regime, not the gate. (This was later also walked back in the project's own notes.)
- **"~10%/day reachable at 12–15% sizing"** (notes 00/09) — true only in a no-crash sample; it sizes into the unhedged crash tail. Reckless. Use single-digit sizing.

## WHAT IS STILL GENUINELY OPEN (the only unresolved items)
1. **Fill depth at the EXACT entry moment** (final 2–4 min, at size). Live snapshot looks deep (~6,900 in band) but isn't the trade-moment state. Closeable via the keyed third-party order-book archives — **Kalshi BackTest API** (free tier, covers BTC/ETH/SOL/DOGE/XRP 15-min with sub-second book snapshots) or **Predexon** (`api.predexon.com/v2/kalshi/orderbooks`, has `bid_depth`/`ask_depth`). Both need an API key (signup with Ryan's account).
2. **The all-5-assets-at-once whipsaw crash** — unobserved, unquantifiable from history. Only forward experience or a deliberate stress assumption settles it. It's a sizing input, not an edge question.
3. **Forward persistence** — all Kalshi pricing data is ~2 months / one regime. No true forward test has run on any edge. This is the ultimate arbiter for all of them.
4. Execution itself (Kalshi auth/order placement) is commodity — not a blocker.

## Scripts & data from this verification (all in `~/kalshi_data/`)
- `scripts/verify_analyze.py` — streak + breadth, CIs, train/test threshold discipline.
- `scripts/verify_econ.py` — breadth reversal real CLOB entry prices + EV (Poly vs Kalshi fee).
- `scripts/verify_gold.py` — gold×BTC-drop, both periods, patient pull.
- `scripts/verify_fav.py` — gated-vs-ungated favorite decomposition (the refutation).
- `scripts/verify_lock.py` — lock edge price×Z flip table + tradeable rule + clustering, on BTC `all_ticks`.
- `scripts/verify_poly_liq.py` — Polymarket per-market volume (median ~$102k).
- `scripts/crash_sim.py` + `btc_5min_long.json` — 300-day crash co-flip simulation.
- `scripts/pull_btc_long.py` — pulls the long BTC 5-min history.
- Live Kalshi order book (free, current markets only): `GET api.elections.kalshi.com/trade-api/v2/markets/{ticker}/orderbook` → `orderbook_fp.{yes,no}_dollars` = [[price, resting_size],…], 0.1¢ ticks.

Related: [[00 - START HERE]] · [[08 - The Lock Edge - Settlement-Lock Favorite]] · [[09 - Lock Edge - Failure Rate & Sizing]] · [[08 - Broad-Kalshi & Cross-Venue]] · [[07 - Overfitting & Validation Discipline]]
