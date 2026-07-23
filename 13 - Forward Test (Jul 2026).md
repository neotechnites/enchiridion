# 13 — Forward Test (2026-07-15)

> ⚠️ **NO LONGER THE MOST RECENT GROUND TRUTH (2026-07-23).** This test was honest and its verdicts were correct ON ITS DATES. Two have since changed: **Lock** decay-died in the weeks after (kill-scan: +1.72¢→−1.07¢ by week — decay kill, benched, do not build). **Streak 'break-even'** was the unconditional ~50¢ version; the **≤44¢ price gate rescued it** (2-yr regime-proof, 56-57% every slice) — a textbook conditional rescue per the Kill Taxonomy (note 15). Current ground truth: [[18 - LIVE STATE (2026-07-23)]].

> The forward paper-test the vault kept calling "the only remaining arbiter" — finally run. The notes were written 2026-06-25; this test used **~20 days of pure out-of-sample data (Jun 25 → Jul 15) that no edge had ever seen.** Four edges re-pulled fresh (cached files stop ~Jun 24) and re-tested with the exact rules from the notes + reference scripts. Where this contradicts an optimistic earlier claim, trust this — it's genuine OOS.

## TL;DR
Nothing collapsed. Three of four edges held; one decayed but stayed positive. The **lock edge held essentially perfectly** and is the standout.

| Edge | Verdict | Forward result | Vault claim |
|---|---|---|---|
| **Lock / deep-longshot fade** (BTC, off-50) | ✅ HELD | n=138, 99.3% win, +3.25%/trade; Z≥4 flip **0.06%**, still monotonic | 98.7% OOS, +3–4%, 0.27% flip |
| **Weather forecast-buy** | ✅ HELD | 6/6 tradeable cities +; DEN/SEA lost as MAE filter predicts; precip + MAE→EV persisted | +6.17¢/trade |
| **Gold × BTC-drop** (at-50) | ⚠️ DECAYED | 54.5% win, +3.2¢/trade — +EV but z=0.8 over breakeven; stricter cutoffs recover to ~58% | 57.8–61.3% |
| **Streak / breadth reversal** | ✅ HELD (direction), Kalshi-untradeable | pooled 54.4% (z=2.8), all 5 assets + Poly >50%; break-even after Kalshi fee; breadth added nothing | ~52%, Poly-only |

## Detail

### Lock edge — HELD (the standout)
- Window: 1,957 fresh KXBTC15M markets, 9,778 late flip-checkpoints.
- Main cell (93–97¢, Z≥4, one entry/mkt, ask+0.5¢, fee): **n=138, 99.28% win (1 loss), +3.11¢ (+3.25%/trade)**, avg entry 95.9¢. Inside the vault's +3–4% / 98.7% OOS band.
- TRUE flip rate by Z (9,778 obs) still **perfectly monotonic**: 0-1→34.7%, 1-2→6.7%, 2-3→2.9%, 3-4→1.2%, 4-5→0.22%, ≥6→0%. Cumulative **Z≥4 = 0.06%** (3/4969), Wilson UB 0.18% — *better* than the claimed 0.27%.
- Sweet spot reproduced: 93–95¢ +5.5% (vault +5.7%). Positive across every Z∈{4,5,6} and band ≥93¢.
- Single loss: 2026-06-28 15:15, Z=4.1 (barely over threshold) — the expected marginal-whipsaw tail. Zero losses at Z≥5.

### Weather forecast-buy — HELD
- 20 settled days, all 8 cities. Method faithful to `weather_fc.py`, no lookahead (bias from a strictly prior window).
- Tradeable-6: n=112, +12.39¢/trade pooled — **but inflated by a NY/BOS heat wave** (+102%/trade with them, only +5.7% without). Durable read = **~+3.5–5¢/city**, bracketing the vault's +6.17¢.
- Negative controls lost hard: **DEN −29¢ (0/17), SEA −19¢** — exactly as the city-MAE filter predicts.
- corr(2yr-MAE, forward-EV) = **−0.70** (vault −0.93); precip filter held (dry +16.7¢ vs wet +1.2¢); entry prices fresh (median 4 min old).

### Gold × BTC-drop — DECAYED (still +)
- n=156, 54.5% win, +3.17¢ (+6.4%/trade). Below the vault's 57.8–61.3% but net EV still in the +2.2–6.1¢ band (entries came cheaper, ~50¢).
- Margin over break-even is only z=0.80 — statistically indistinguishable from break-even this window. Stricter cutoffs (quartile/P30) recover to 57–59%, monotonic in strictness → real signal, thin margin. At-50, high variance.

### Streak / breadth reversal — HELD direction, not Kalshi-tradeable
- 1,011 4-streak signals across 5 assets. Pooled reversal **54.4% (z=2.8)**; Poly BTC 5m 3/4/5-streak all >50% (z up to 2.8). No decay.
- Tradeable EV at real open price net of fee: pooled **+0.6¢ → break-even at 1¢ spread**. Open pre-prices the reversal to ~52.6¢. SOL negative; DOGE +11.3% is thin-n noise. **Profitable only on Polymarket** (near-zero fee), unchanged from [[12 - Independent Verification (external review)]].
- Cross-asset breadth added ~nothing on Kalshi 15m (the 5 cryptos are too correlated for breadth to be orthogonal; opposite-direction control n=0).

## Sizing check ($1000, realized path, 20 days)
- Lock @5%/trade compounded: **$1000 → $1,250** (+25%), **5% max drawdown** — ~1.1%/day, matches the vault's ~1–3%/day doctrine.
- Weather regime-robust (flat small $): ~+$300; the raw +$8k was the one heat wave — don't design around it.
- Combined realistic **~$1,250–1,550**, of which the lock +$250 is the dependable core.
- Naive %-of-bankroll compounding on weather gives fantasy (+2500%) — rejected: can't scale bet size on thin temp markets, and it was regime-inflated.

## What this does and does NOT settle
- **Settles:** the edges are real and persist out-of-sample. Lock's flip physics reproduced from scratch.
- **Still open (unchanged):** lock's all-assets-at-once crash tail (no crash in this calm window) and live order-book fill depth at the exact final-2-min entry. Both are sizing/fill questions, not edge questions — only live trading closes them.

## Implication for the build
Confirms the [[00 - Implementation Overview]] plan: **Lock = the dependable engine, Weather = uncorrelated satellite; drop Gold (decayed, at-50) and Streak (Kalshi break-even).** Forward-collected trade logs will keep extending this test.

Scripts/data: `~/kalshi_data/scripts/fwd_{lock,weather,gold,streak}_*.py`, `sim_bankroll.py`; `~/kalshi_data/forward_*.json`.

Related: [[00 - START HERE]] · [[12 - Independent Verification (external review)]] · [[00 - Implementation Overview]] · [[08 - The Lock Edge - Settlement-Lock Favorite]] · [[08 - Broad-Kalshi & Cross-Venue]]
