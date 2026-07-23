# 10 - Stress Test & Verdict (cheap-side reversal)

> ⚠️ **Historical (session 1/2 era).** The slate, goal, and verdicts have changed materially since this was written — [[18 - LIVE STATE (2026-07-23)]] wins every conflict (notably: lock edge = DEAD by decay; goal = staged $1k→$10k→$50k→$1k/day, not flat %/day; kill verdicts governed by the Kill Taxonomy in note 15).

> Session 2026-06-25. Full stress test of the [[08 - Cheap-Side Reversal (off-50 candidate)]] edge, demanded before risking money ("a 12% edge is crazy, make sure it's real"). **Bottom line: NOT confirmed for real money. Passes every Kalshi-internal test; fails or can't-be-validated on Polymarket; has a late-period decay; only ~2 months of one-regime Kalshi data exists. Forward paper-trading is the only remaining arbiter.**

## What PASSED (Kalshi-internal)
- Real liquidity at cheap entries (median trade size 18 vs 20 baseline; prices sustained, not stale prints).
- Survives spread (+1¢ → +16¢), VWAP fill (+10¢), 65% fill rate, adverse selection (filled-on-dip still +12.7¢).
- Discriminator: beats generic cheap sides by +9–11pp at same price (it's the signal, not longshot bias).
- Robust across entry thresholds; positive in both period-halves (but decaying).

## What FAILED / couldn't validate (Polymarket — the independent lab)
| test | n / span | cheap<48¢ result |
|---|---|---|
| Poly 5m, 4-streak | 979 | breakeven/noise (cheap n=28) |
| **Poly 5m, 60-min window** (correct horizon) | 1169 / 133d | **win 39.1%, EV −2.2¢** (52–58¢ rich = +1.1¢) |
| Poly 15m (same horizon as Kalshi) | 799 mkts, 69 signals | **only 3 cheap entries exist**; all-signal 53.6% win, −3¢ |

On Polymarket the cheap filter **does not replicate** — at 5m it inverts (cheap loses, "cheapness is information"), at 15m there are almost no cheap entries to test.

## The crucial reconciliation
**Polymarket structurally cannot validate this edge.** Kalshi produces a cheap reversal entry **~38%** of the time; Poly **~7%**. Kalshi's 15-min markets open in a thin book with a post-streak skew (retail over-prices continuation); Poly's continuous CLOB opens flat at ~50. The thing the edge depends on — the **Kalshi opening skew** — doesn't exist on Poly to test, and the few cheap entries Poly does have are 5-min (no time to mean-revert) and lose.
- So Poly is NOT a valid OOS for THIS edge (unlike for breadth-reversal, which it did validate).
- This is consistent with Ryan's core thesis (Kalshi = retail + simple bots, inefficient at the open). It could be a **real Kalshi-specific edge** — OR an overfit. The data can't currently distinguish them.

## Why it's NOT confirmed
1. Cross-platform: contradicted (5m) or untestable (15m).
2. Cross-period: only ~2 months of Kalshi 15m data exists (API retention from ~Apr 18); the one split-half showed **decay +7.0¢ → +1.6¢**.
3. Cross-asset helps (SOL/XRP confirm directionally) but same recent regime.
→ Every historical OOS path is exhausted or contradictory.

## The only remaining arbiter: FORWARD PAPER-TRADE
The notes' own ultimate test ([[07 - Overfitting & Validation Discipline]]). Log every cheap signal + entry live across all 5 assets (~6.5/day → a few hundred trades in 2–4 weeks), settle whether the +12.7% holds out-of-the-data. Do NOT size real capital until it does. If it holds forward → it's a genuine Kalshi opening-skew edge worth ~2%/day at ¼-Kelly. If it decays to ~0 → it joins the favorite-underpricing graveyard.

## Honest EV-improvement summary
- Before this session (verified): blanket reversal +2.2¢ (+4.3%/trade), ~0.4–0.9%/day.
- This session's candidate (UNCONFIRMED): cheap<48¢ +4.8¢ (+12.7%/trade), ~2%/day at ¼-Kelly — pending forward proof.
- 10%/day remains out of reach from anything verified; the path requires this edge to survive forward-testing AND stacking (breadth sizing, more assets, half-Kelly).

Related: [[08 - Cheap-Side Reversal (off-50 candidate)]] · [[09 - Deep-Favorite Lock (DEAD)]] · [[07 - Overfitting & Validation Discipline]] · [[11 - Session 2 Scripts & Data]]
