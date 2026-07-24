# verify-fed-hold (BUFFETT B5) — mechanism check on kxfed_settled.json

Date: 2026-07-24. Source: `~/kalshi_data/kxfed_settled.json` (16 settled markets).

## What the 16 markets actually are (honest n)
The file is NOT 16 independent meetings. It is **one meeting** (Jun 17 2026 FOMC), split across two ladders:
- `KXFED-26JUN` — 11 rungs: rate-level ladder ("upper bound > X%").
- `KXFEDDECISION-26JUN` — 5 buckets: the decision ladder (Hike>25 / Hike25 / **Hold (H0)** / Cut25 / Cut>25).

So the "no change" question has **n = 1 meeting**. Treat as mechanism-check only.

## Result
- `KXFEDDECISION-26JUN-H0` ("Fed maintains rate") → **result = yes**. Fed held in Jun 2026. ✓ mechanism.
- Rate ladder corroborates: `>3.5%` yes, `>3.75%` no ⇒ upper bound settled at 3.75% (unchanged).

## Price fields (limitation)
Only `last_price_dollars` / `previous_price_dollars` are informative; the live book is dead post-settle (yes_bid 0.00 / yes_ask 1.00 on every rung — settled snapshot, not an entry).
- Hold rung H0: last = **0.98**, prev = 0.98 → the crowd priced the hold at 98¢ and it resolved yes.
- All other decision buckets: last = 0.01, resolved no.

## Verdict: MECHANISM CONFIRMED, NO EDGE
The hold market resolves yes when the Fed holds (mechanism sound), but:
1. n = 1 meeting in this file — cannot estimate hit-rate or calibration.
2. The hold was already priced at near-certainty (0.98); max gross on buying it = 2¢, minus fee (0.07·0.98·0.02 ≈ 0.0014) and the real tail risk of a surprise move → not tradeable at that price.
3. No live entry price survives on settled markets, so entry realism is unverifiable here.

**B5 is a mechanism-check pass, not a strategy.** To assess a real edge you'd need forward capture of the hold rung across many meetings BEFORE the decision (pre-close quotes), which this file does not contain. Kill taxonomy: not DEAD (mechanism fine) but n=1 → **no verdict possible beyond mechanism**; needs forward pre-close capture to advance.

Files: analysis `/tmp/fedhold.py`.
