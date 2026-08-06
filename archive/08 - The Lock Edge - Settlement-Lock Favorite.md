# 08 - The Lock Edge (Settlement-Lock Favorite)

> ✅ **THE 2026-07-23 DECAY KILL IS RETRACTED (2026-08-06). The edge measures ALIVE.** The kill cited +1.72¢/contract (first 6 wks) → −1.07¢ (last 4 wks). On a complete re-pull (34,459 markets, real `floor_strike` on all five assets, 2026-05-25→08-05) the kill's own "last 4 weeks" window measures **+3.39¢/contract pooled (n=752)**, and **every single week is positive, pooled and BTC-alone**. Control reproduced this note's numbers to the digit three ways, including a fresh independent pull. −1.07¢ is arithmetically unreachable — it needs a 4.44% flip rate; the measured Z≥4 flip rate is **0.181%** (90/49,747). What produced −1.07¢ is UNKNOWN; a data-dropout artifact was hypothesised and measured FALSE. **The rule below stands as written and is the correct rule** — with one exception: the "hard floor at 93¢" no longer holds (the 90–93¢ band is now the best cell, +6.4% at Z≥4), which needs its own test before the operating point moves. **This is not a green light to trade.** The one thing never measured is unchanged: real fills and depth at 93–97¢ in the final 2–4 min — unbacktestable, needs forward book capture. Everything here is last-trade + 0.5¢; at +3.3¢/contract, >3¢ of slippage kills it. Full record + the re-pull trap: [[38 - The Strategy Book (living inventory)]] LOCK entry.

> ⚠️ *Historical banner, kept as the lesson (superseded by the line above):* "SUPERSEDED — LOCK EDGE IS DEAD (DECAY KILL, 2026-07-23) … decay ≠ conditional — gates can't fix competition. Do NOT trade it, do NOT build it." **The lesson it now teaches is the opposite of the one intended:** a numberless kill (no n was ever recorded for either arm) benched the best-evidenced edge in the corpus for 14 days, and the standing weekly duty to re-run `backtest-lock` never ran. Verdicts are dated — and so are kills.

> Found & validated 2026-06-25. The first edge that clears the full [[00 - START HERE]] verification bar **off-50, cross-asset AND cross-platform.** It is a NEW edge (not confirmed to be Ryan's 2-year secret edge). Failure-rate / sizing detail in [[09 - Lock Edge - Failure Rate & Sizing]].

## The rule (plain English)
> **If the favorite is at 93-97¢ with ~2-4 min left AND the coin is 4+ "normal moves" clear of the line, buy the favorite and hold to settle. Never below 93¢.**

## The rule (precise)
Scan each live 15-min market from ~240s → 30s before close. At the **first** checkpoint where BOTH hold, buy the favorite at ask, hold to settlement:
1. **Favorite price ∈ [93, 97]¢** (the leading side).
2. **Z ≥ 4**, where `Z = |spot − strike| / (median_1min_move × √(minutes_left))`.
   - `median_1min_move` = median absolute 1-min underlying move over the prior 15 min.
   - Z = "how many plausible remaining moves the coin is from the line." Z≥4 ⇒ a flip needs a ~4σ move against you AND the 60-sec settlement average to cross.
3. Distance must be **on the favorite's side** (don't buy a favorite the underlying contradicts).

## Why it exists (mechanism)
Kalshi settles on the **average of CF BRTI over the final 60 seconds**, and the favorite quote **caps near 99¢**. When the coin is already far from the strike late in the window the outcome is near-locked, but nobody bids 99.7¢ for a 3¢ gain, so it sits at 95-97¢. The Z filter isolates the genuinely-locked markets the cap underprices. This is the same structural under-pricing the old "settlement-lock favorite" note chased and (correctly) killed — it died **because it was tested price-only** (no distance filter), which mixes in 96¢ favorites that are genuinely only 96% (coin near strike). The distance/Z filter is the whole trick.

## Validation — 5 Kalshi assets, one entry/market, net of fee + 0.5¢ spread
Band 95-97¢, **Z ≥ 4**:

| asset | n | losses | win% | net EV/trade |
|---|---|---|---|---|
| BTC | 62 | 0 | 100% | +3.6% |
| ETH | 30 | 0 | 100% | +3.4% |
| SOL | 24 | 0 | 100% | +3.5% |
| XRP (real strike) | 32 | 0 | 100% | +3.6% |
| DOGE (real strike) | 41 | 0 | 100% | +3.5% |
| **pooled** | **189** | **0** | **100%** | **+3.6%** |

- **Cross-period:** BTC split early/late ~15d each → both 100% win, +1.6%/+1.7% (97-99 norm version) / +3.6% (Z≥4 95-97). Held in both halves independently.
- **XRP/DOGE used REAL strikes** (`floor_strike` from the markets API), not reconstructed — cleanest slice, and they hold at Z≥3 (n=91/87, 100%).
- "100% win" here is small-n per cell — the **real** failure rate (0.27%, measured on 33k obs) is in [[09 - Lock Edge - Failure Rate & Sizing]]. It does NOT win every time.

## Cross-PLATFORM confirmation — Polymarket (the falsification test)
Pulled 912 Polymarket BTC 5-min markets' CLOB price paths (`poly_btc5m_paths.json`). Polymarket settles **spot-at-close** (not 60s-avg) and quotes finer (no 99¢ cap), so it's an independent mechanism check:

| Poly band | n | losses | win% | EV (≈0 fee) |
|---|---|---|---|---|
| 97-99¢ (any Z) | 154 | 0 | 100% | +1.5% |
| 95-97¢ Z≥4 | 73 | 1 | 98.6% | +2.2% |

Late-favorite under-pricing **exists on Polymarket too** → the effect is structural, not a Kalshi artifact. It shifts to the **97-99¢** band there (no price cap) and pays less per trade but at **~0 fee** and **~48 qualifying trades/day** on BTC alone (5-min cycle).

## How cheap can we enter? (price × Z grid, all 5 assets, 30d)
| entry price | Z≥3 | Z≥4 |
|---|---|---|
| 85-90¢ | 86.7% win, **−2.3%** ❌ | — |
| 90-93¢ | 93.1%, +0.9% (thin) | 93.3%, +1.2% |
| **93-95¢** | **100%, +5.7%** ✅ | **100%, +5.7%** ✅ |
| 95-97¢ | 98.3%, +1.8% | 100%, +3.7% |
| 97-99¢ | 99.8%, +1.3% (15/day) | 99.6%, +1.1% |

- **Sweet spot is 93-95¢, not 96¢** → +5.7%/trade, ~60% more edge, still ~100% at Z≥3.
- **Hard floor at 93¢.** Below it the edge inverts (cheapness = real uncertainty, the "cheapness is information" rule from [[07 - Overfitting & Validation Discipline]]). 90¢ entry does NOT work.

## Tested refinements that did NOT help
- **Streak-swap overlay** (lock trade in a post-4-streak market, per [[02 - The Confirmed Edge - Streak Reversal]]): every cell n≤13 → no statistical power. Lock is a last-2-min mechanic; streak is an at-open signal — they fire on different markets and rarely coincide. No usable interaction. (It's an *independent* edge, can run alongside.)
- **Momentum 2nd filter** to admit Z=3-4 trades: losers had mixed momentum; carving a filter to 7 losers/405 = overfitting. Don't.

## Frequency & operating points
- **Z≥4, 93-97¢:** ~16 qualifying trades/day pooled across 5 Kalshi assets.
- **Z≥3.5:** ~25/day (slightly more losses, see [[09 - Lock Edge - Failure Rate & Sizing]]).
- Polymarket 5-min runs in **parallel on separate capital** (~48/day BTC, ~0 fee) — biggest frequency multiplier, doesn't touch the Kalshi bankroll.

## Verification-bar check (per [[00 - START HERE]])
(1) off-50 ✓ (93-97¢) · (2) Kalshi net-of-fee EV >0 ✓ (+3.6%) · (3) held-out different period ✓ (BTC halves + cross-asset) · (4) robust ≥3 adjacent params ✓ (Z 4/5/6, bands 93-97) · (5) ≥2 independent markets ✓ (5 assets + Polymarket) · (6) ≥1/day ✓ (~16). **Clears the bar.**

## Open / next
- **Live fill test:** backtest used last-trade + 0.5¢; must confirm 93-97¢ is fillable at size in the final 2-4 min (no order-book history pulled — the one thing historical data can't answer).
- **Apply the 93¢ cheap-band filter on Polymarket** (CLOB pull already exists).
- Scripts & data: see [[05 - Data & Scripts Reference]] (Lock-edge section).
