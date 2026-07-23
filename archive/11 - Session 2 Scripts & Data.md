# 11 - Session 2 Scripts & Data (2026-06-25)

> Everything added in the cheap-reversal / stress-test session. All in `~/kalshi_data/`. Extends [[05 - Data & Scripts Reference]].

## New data files
| File | What |
|---|---|
| `KXXRP15M_paths.json` | 1,045 XRP 15m markets w/ tick paths (Jun13–23). Pulled via `pull_asset.py KXXRP15M 11`. |
| `streak_trades.json` | (existing) 674 cached reversal trades `[ts, entry_price, won]`, BTC+ETH+DOGE, Apr19–Jun23 — the cheap-reversal goldmine. |
| `poly_4streak_trades.json` | 979 Poly 5m 4-streak reversal entries `[up, entry, won, ts]` (negative control — wrong horizon). |
| `poly_window_trades.json` | 1,169 Poly 5m **60-min-window** reversal entries (correct horizon analog). cheap<48 = 39% win, −2.2¢. |
| `poly_15m_raw.json` | 799 Poly **15-min** markets `[open_ts, result, yes_open]` (same horizon as Kalshi; only ~6d dense, only 3 cheap entries). |

## New scripts (`~/kalshi_data/scripts/`)
**Cheap-reversal (the edge → [[08 - Cheap-Side Reversal (off-50 candidate)]]):**
- `streak_cheap.py` — cheap-entry filter cross-asset + split-half (BTC/ETH/SOL).
- `cheap674.py` — cheap filter on the 674 cache (main numbers, threshold sweep, split-half).
- `streak_cheap_mono.py` — entry-price bin monotonicity (cheap region robust, near-50 negative).
- `cheap_vs_generic.py` — **the discriminator**: generic cheap sides lose; post-streak cheap wins +9–11pp more.
- `breadth_stack.py` — breadth multiplier (≥1 co-streaking asset → 75% win). Uses XRP too.
- `cheap_final_summary.py` — Kelly sizing + daily-return math.

**Fill realism → [[10 - Stress Test & Verdict]]:**
- `fill_realism.py` — entry models (spread/VWAP/worst-tick), fill rate, adverse selection. PASSED.
- `cheap_liquidity.py` — cheap entries are real liquid trades, not stale prints.

**Deep-favorite lock (DEAD → [[09 - Deep-Favorite Lock (DEAD)]]):**
- `deepfav_calib.py` — deep-favorite calibration by price × time-to-close.
- `lock_edge.py`, `lock_edge2.py` — z = margin/(σ√t) lock signal, price×margin grid.
- `lock_rule.py` — one-obs-per-market rule + placebo + robustness.
- `lock_discriminate.py` — win-vs-z monotonicity (NON-monotone = noise) + regime split.
- `your_proposal.py` — Ryan's exact "95–97¢, 2min, 4+ moves" wording (only 11 cases exist; non-monotone).

**Polymarket stress test:**
- `poly_cheap_pull.py` — pull Poly 5m 4-streak reversal open prices.
- `poly_window_pull.py` — pull Poly 5m 60-min-window reversal open prices (correct horizon).
- `poly_15m_pull.py` — pull Poly 15m outcomes + open prices (same horizon as Kalshi). Slug `btc-updown-15m-<open_ts>`.
- `poly_cheap_analyze.py`, `poly15_analyze.py` — analysis of the above.

## Key API notes learned
- **Polymarket HAS 15-min crypto markets**: gamma slug `btc-updown-15m-<open_ts>` → market → `clobTokenIds[0]` (UP token) → clob `prices-history?market=<token>&fidelity=1&startTs=&endTs=`. Result inferable from final price (→1 up / →0 down). But 15m markets are **sparse** (only ~6 dense days found) and rarely open cheap.
- Poly 5m series id 10684; 15m via slug. `pull_asset.py <series> <days>` builds Kalshi `_paths.json` for any series.
- Kalshi 15m data only exists from ~Apr 18, 2026 (one ~2-month regime) — limits cross-period OOS.

Related: [[05 - Data & Scripts Reference]] · [[08 - Cheap-Side Reversal (off-50 candidate)]] · [[09 - Deep-Favorite Lock (DEAD)]] · [[10 - Stress Test & Verdict]]
