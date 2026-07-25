# Lane 7 — DUTCH + DERIBIT (Saturday burst wave 2)

Data: `~/kalshi_data/dutchbook_paper.jsonl` (28 rows), `~/kalshi_data/deribit_gate_hourly.jsonl` (4325 rows).
Both are PAPER/WATCH captures — no executed trades. All money figures are reconstructed from the
records (R104); the "would-have" P&L is paper, labeled as such.

================================================================
## PART A — DUTCHBOOK  →  VERDICT: CONDITIONAL (threshold sub-family real; detector polluted)
================================================================

Capture = selection-filtered log of combos the watcher flagged net>0. 28 rows over 59.0h
(2026-07-22 21:57 → 07-25 08:59Z). Each row is a single snapshot (t, close), NO repeated
snapshots of the same combo ⇒ zero persistence/duration data.

Fields: pairs a 15m market leg (t15, strike K) with a same-close daily-hourly threshold leg
(tD, threshold T). `net` = 100 − cost − fees (cents/contract, AFTER fees). All 28 rows net>0
by construction of the filter.

### Reconstructed realized payout (guaranteed-$1 test)
Split by combo family (K vs T same close time, near-identical strikes):

THRESHOLD combos — DN+15Y, DY+15N (opposite-direction legs): n=18
  - Realized total-leg payout = 1.0 on ALL 18 (verified from res15/resD). GENUINE dutch book:
    below both strikes only the NO/daily leg pays, above both only the YES leg pays, and the
    narrow K–T overlap pays 2 (bonus). Payout always ≥1, cost 95.0–99.4 < 100 ⇒ locked profit.
  - net cents: min 0.50 / median 0.65 / max 3.85
  - size (depth): min 0.25 / median 94.5 / max 2000 contracts; 12 of 18 have size≥10
  - Paper locked profit at full captured size: $54.93 total (≈all of it in the 12 size≥10 rows).
  - Frequency: 07-22:1, 07-23:3, 07-24:10, 07-25:4  (~ few/day, clustered).

BAND combos — BB_NO+15N, BA_NO+15Y (SAME-direction legs, both NO or both YES): n=10
  - FALSE arbs. These are 2× a directional bet, NOT a partition. Reconstructed realized payout:
    6 rows paid 0 (BOTH legs lost → −99¢ full loss), 3 paid 1, 1 paid 2.
  - The watcher's `net>0` filter wrongly assumes a guaranteed $1 here. These BLEED: 6/10 total losses.
  - The 6 losers are all BB_NO+15N with res=yes/yes (price ended above both strikes ⇒ both NO legs void).

### Dutchbook conclusion
- Real, verified edge exists in the THRESHOLD sub-family only (18/18 payout=1, positive net after fees).
- BUT the deployed detector is polluted: 10/28 flagged combos are same-direction false arbs that
  lose 60% of the time. Detector MUST reject same-direction leg pairs before any capital.
- Net is thin (median 0.65¢/contract) and there is NO persistence data — cannot confirm both legs
  are simultaneously fillable, and one-leg-fill on a 0.65¢ edge is a total loss. This is the binding gate.
- GATE to make tradeable: (1) fix detector to threshold/opposite-direction combos only;
  (2) capture leg-book depth + quote-lifetime to prove both legs hittable inside one window before
  the price moves; (3) require net ≥ ~1.5¢ to survive single-leg slippage. Until (2) exists it stays paper.

================================================================
## PART B — DERIBIT GATE (hourly)  →  VERDICT: DEAD (tenor-mismatch artifact)
================================================================

4325 rows, 67.7h, 108 events, btc 2087 / eth 2238. Fields: kmid (kalshi YES mid), pD
(deribit-implied YES prob), gap=kmid−pD, side (fade the rich venue), result. 4199 have settled result.

### The gap is not a mispricing — it's a horizon mismatch
Ladder inspection (KXBTCD-26JUL2218, ~57min to close): kalshi ladder is STEEP (kmid 0.98→0.01
across ±$1500) because the 1-hour market is tightly pinned near spot; deribit pD is FLAT
(0.96→0.04 over the same strikes) because deribit's nearest option expiry is multi-day, so its
implied distribution is far more diffuse. gap median −0.539 (|gap| median 0.539) — a 54¢ "gap"
is not an arb, it's a 1-hour distribution vs a multi-day distribution.

### Calibration proves kalshi is truth, deribit is noise
Brier vs realized outcome (lower better):
  ALL rungs (n=4199): kalshi kmid = 0.0240   deribit pD = 0.4390
  Near-money 0.15–0.85 (n=381): kmid = 0.2193   pD = 0.3778
Deribit pD mean in NTM = 0.936 while realized YES-rate = 0.575 → deribit wildly overconfident.
Kalshi near-money is well calibrated: BTC mean kmid 0.493 vs realized YES 0.497.

### The apparent "fade profit" is in-sample directional drift, not edge
Fade-the-rich-side realized P&L (kalshi taker fee 0.07·p·(1−p)):
  ALL: net/ct ≈ −0.001 to 0.000 (breakeven→loss), winrate 6–14%.
  NTM 0.15–0.85: net/ct +0.085, winrate 57.5% — LOOKS good, but:
    - 361/381 NTM signals are fadeNO = "buy YES". It's a levered long.
    - BTC NTM: yes-rate 0.497 vs kmid 0.493 → edge ≈ ZERO.
    - ETH NTM: yes-rate 0.637 vs kmid 0.499 → ETH drifted UP over the 68h window. ALL the profit
      is ETH in-sample upward drift.
    - Effective n is ~50 events (rungs within an event are perfectly correlated: 28 all-YES,
      29 all-NO, 46 mixed across 103 events). Not significant.

### Deribit conclusion
Dead. The signal is a structural tenor artifact (deribit has no hourly expiries to match KXBTC/ETHD
1-hour ladders), deribit pD is badly miscalibrated (Brier 0.44 vs 0.024), kalshi near-money is the
better price, and the only positive P&L is ETH directional luck. Not a death that a gate fixes —
the fix would require a same-expiry deribit contract that does not exist for these hourly markets.

================================================================
Tokens well under ceiling. No git, no subagents.
