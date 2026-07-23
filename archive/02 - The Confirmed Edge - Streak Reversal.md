# 02 - The Confirmed Edge: Streak Reversal

> ✅ **STILL ALIVE — but the rule below is the OLD unconditional version.** Current rule (2026-07-23): **streak ≤44¢** — after 4 same-direction prints, buy the reversal side in the first 60s ONLY if it costs ≤44¢ (2-yr regime-proof, 56-57% every slice, both coins — see [[18 - LIVE STATE (2026-07-23)]]). Teaching point: the unconditional ~50¢ version tested break-even after Kalshi fees in the Jul 2026 forward test — a CONDITIONAL result, and the price gate rescued it. This edge is living proof of the Kill Taxonomy (note 15). It is also Ryan's chosen FIRST live implementation (the $100 mechanics week).

**This is the real find. It survived out-of-sample testing across 5 months and both halves independently.**

## The rule
1. Track the last four 15-min outcomes (`KXBTC15M`).
2. When **4 in a row** print the same direction (4 ups OR 4 downs), at the **next market's open**, buy the **opposite** side (the reversal) at ~50¢.
3. Hold to settlement.

## The numbers
### Outcome reversal (signal — huge sample, very robust)
Over **6,229 markets / ~66 days** (Apr 18 – Jun 23, 2026):

| last-4 outcomes | n | next UP-rate |
|---|---|---|
| 0/4 up (4 downs) | 350 | **55.4%** (±2.7) |
| 1/4 up | 1628 | 51.6% |
| 2/4 up | 2434 | 47.0% |
| 3/4 up | 1469 | 48.9% |
| 4/4 up (4 ups) | 308 | **44.5%** (±2.8) |

corr(prior-4-up-count, next-up) = **−0.039** (reversal). 

**Split-half replication (the reason to trust it):**
- First half: after 4 downs → 56.4% up; after 4 ups → 45.8% up.
- Second half: after 4 downs → 54.6% up; after 4 ups → 42.6% up.
- Same reversal in both halves independently, spanning different regimes → not a fluke, not regime-driven (regime would predict *continuation*).

### Tradeable EV (entered at real open ask + fees, held to settle), 5 months
| | n | open cost | win | net PnL/trade |
|---|---|---|---|---|
| ALL reversal trades | 330 | 51.9¢ | 55.8% | **+2.22¢ (+4.3%/trade)** |
| after 4 DOWNS (buy UP) | 173 | 51.5¢ | 55.5% | +2.34¢ (+4.5%) |
| after 4 UPS (buy DOWN) | 157 | 52.3¢ | 56.1% | +2.09¢ (+4.0%) |

Both directions positive & symmetric. **Near-50¢ symmetric payoff = NO steamroller** (win ~48¢, lose ~52¢) — the structure that can actually compound.

## Frequency
- 4-streaks occur in **~10.6% of markets** (658 / 6229) ≈ **~10/day** (~5 four-down + ~5 four-up).
- Slightly *less* than the 12.5% pure-coinflip expectation (reversal itself makes long streaks rarer).
- BUT only **~half had a trade at the open** to fill against → realistically **~5 clean entries/day**; **post a limit at ~50¢** to catch the rest.

## Honest caveats
- **55/45 edge → losing streaks happen.** It compounds on average; it is NOT "70 in a row" foolproof. Size for the variance.
- The +2.2¢ per-trade number rides on 330 trades (noisy ±); the *win rate* and *signal* are the solid parts.
- Confirmation scripts: `streak_long.py` (signal, results-only), `streak_ev.py` (tradeable EV with open quotes). See [[05 - Data & Scripts Reference]].

## CROSS-ASSET REPLICATION (2026-06-24) — the big validation & edge-multiplier
The exact 4-streak reversal rule, run on every crypto 15-min market, ~5 months:

| series | 4-streak signals | reversal win |
|---|---|---|
| KXBTC15M | 658 | 55.5% (±1.9) |
| KXETH15M | 605 | **57.5%** (±2.0) |
| KXSOL15M | 717 | 53.0% (±1.9) |
| KXXRP15M | 704 | 53.8% (±1.9) |
| KXDOGE15M | 644 | 54.5% (±2.0) |

**All five > 50%.** Five independent markets confirming = strong evidence the effect is real.
- **~50 signals/day across the five** (vs ~10 BTC-only) → ~5× daily profit at the same edge.
- More **diversified** (different assets/times) → better Kelly, can deploy more total capital. See [[06 - Sizing & EV Math]].
- **Trade priority: BTC, ETH, DOGE (strongest 54.5–57.5%);** SOL/XRP marginal (53–54%, may not clear fees) — last or skip.
- Script: `~/kalshi_data/scripts/streak_multiasset.py`.

## ⚠️ BIG-DATA REALITY CHECK — Polymarket 50k sample (2026-06-25) — READ THIS
Polymarket runs **5-minute** crypto up/down markets (series `btc-up-or-down-5m`, id 10684) back to **Dec 18 2025**. Pulled the **full 49,720 resolved BTC markets over 189 days** (`poly_full.py` → `poly_btc5m_full.json`):

| streak | n | reversal win |
|---|---|---|
| 3 | 11,914 | 52.2% (±0.5) |
| 4 | 5,700 | **51.6% (±0.7)** |
| 5 | 2,760 | 51.7% (±1.0) |
| 6 | 1,334 | 52.6% (±1.4) |

**The reversal is REAL (confirmed on 50k markets, ~2–3σ, every depth) — but the true magnitude is ~52%, NOT the 55–59% small samples showed.** Kalshi's 55.5% (n=658) and the Polymarket 7-day 59.5% were upward flukes. The 50k sample is the reliable estimate.
- **Profitability implication:** a ~52% edge ≈ +2¢/contract at 50¢. **On Kalshi, fees (~1.7¢) + spread (1¢) eat it → likely break-even/negative.** The earlier $100→$1,000 sim used the inflated 55% + in-sample filter → **too optimistic.**
- **Best/only viable home: Polymarket** (near-zero fees) — IF entry price cooperates. Needs the priced, filtered sim on Polymarket data (CLOB price history) to confirm profitability. 15-min may be mildly stronger than 5-min (different horizon) but unconfirmed.
- Cross-validation tally (direction): Kalshi BTC/ETH/SOL/XRP/DOGE 15m + Polymarket BTC 5m — **7 slices, all >50%.** Effect real; magnitude thin.

## CORRECTION: 5m vs 15m horizon + CROSS-ASSET BREADTH (2026-06-25) — major
**5m/15m error fixed:** a Kalshi 4-streak = 60 min; on 5-min that's a **12-market window**, not a 4-streak. Using *exact* 12-in-a-row is too rare (n~20). Correct signal = **directional pressure over the 60-min window** (up-count of last 12). At the window extremes (≤2 or ≥10 of 12 up), Polymarket 5m BTC reverses **~54–55.7%** — matching Kalshi, not the misleading 52% from the 20-min "4-streak." The earlier "edge shrank to 52%" was MY wrong horizon, not real shrinkage.

**Same-asset stacking (multi-window logistic) = no improvement** (OOS ~52%, all window weights ~0). The market's own longer history adds nothing past the imbalance.

**CROSS-ASSET BREADTH = real, additive edge.** On 29,625 BTC markets aligned with ETH+SOL+XRP+DOGE 5m, conditioning on total 5-asset 60-min imbalance:
| complex \|imbalance\| | n | BTC reversal win |
|---|---|---|
| 0–5 | 6679 | 49.9% |
| 5–10 | 8610 | 51.1% |
| 10–15 | 3485 | 51.3% |
| **15–20** | **617** | **57.4% (±2.0, ~3.7σ)** |
- **Rule:** bet BTC reversal when BTC is extended AND the whole complex is extended the SAME way (breadth ≥15). ~+6pp over single-asset; ~6 signals/day; +7¢ gross at 50¢ → clears costs even on Kalshi.
- Breadth ADDED edge where same-asset stacking didn't — broad over-extension mean-reverts harder than idiosyncratic.
- TODO: split-half OOS confirm; add entry-price/EV; test breadth on Kalshi 15m too; data files `poly_{btc,eth,sol,xrp,doge}5m*.json`, scripts `poly_breadth.py`,`poly_window.py`.

## Filters tested to SHARPEN per-trade edge
- **Volatility:** ❌ died on full 658 set (low-vol Q1 = 51.8%, no pattern). Was small-sample noise.
- **Streak depth:** ❌ non-monotonic — 4 is a sweet spot (57.5%) but 5/6 fall to ~52%. Stick with 4, don't go deeper. (`streak_depth.py`)
- **Entry-price filter — CONFIRMED & it's the key refinement (2026-06-24, hypothesis was inverted).** On full BTC+ETH+DOGE set (693 trades, `streak_entryfilter.py`):
  - ALL: win 55.3%, cost 51¢, EV +2.63¢
  - open leans reversal (reversal side >51¢): win 62.1% but cost 61¢ → **EV −0.91¢ → SKIP (already priced in)**
  - **open leans continuation (reversal side ≤51¢, avg 39¢): win 47.5%, EV +6.62¢ (~+17%/trade) → THE EDGE**
  - Mechanism: the open *underprices* the reversal; you only get paid buying it **cheap**. Filtering to the cheap half ~**triples** per-trade edge (+6.6¢ vs +2.6¢). ~half the signals → ~20–25 trades/day across assets.
  - Caveat: cheap cell n=326, ~2.7σ, pooled 3 assets — **confirm split-half & per-asset before sizing up** (vol/depth filters looked good and died).
- Time-of-day, ETH-confirm, streak-magnitude: no usable signal.

This is the anchor signal for [[04 - Combined Model Plan]].
