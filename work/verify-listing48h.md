# Lane 4 — LISTING-48H verify ledger (2026-07-25)

Data: ~/kalshi_data/listing_events.jsonl (203 events), listing_books.jsonl (1854 snaps).
Capture window: 2026-07-24 19:09 → 07-25 16:35 UTC (21.4h). Snapshot cadence: **1200s (20 min)** — coarse.
API quirk respected: used *_dollars/*_fp fields only (oi_fp); ya/yb are decimal prices.

## Counts (first look since 12,179-series baseline)
- NEW_SERIES since baseline: **5** in 21.4h (Mentions 1, Financials 1, Elections 2, Climate 1).
- BOOK_LIVE events: 198. Series that produced live books: **4** of 5 (KXHOBBYTEMP listed, no book captured).
- Tickers with any two-sided book in first 48h: **32 of 39** (yb>0.01 & ya<0.99 & ya>yb).

## Spread width (median of two-sided snaps)
- KXACQANNOUNCEOPENR (1 mkt): 5.0¢
- KXRPRESPRIMARY (10 mkt): 3.0¢
- KXDPRESPRIMARY (15 mkt): 3.0¢
- KXEARNINGSMENTIONGRAB (13 mkt): **13.0¢** (p10 6¢) — widest, the sloppy one.

## Anchorable mispricing — the finding
**All 13 mention-grab markets opened at an identical seed: ya=0.54 / yb=0.46 (mid 0.50), 8¢ spread.**
The seed ignores per-word base rates. Within ≤20 min (capture floor) MM reposts real quotes; final mids:

| word | seed mid | final mid | move | word | seed mid | final mid | move |
|---|---|---|---|---|---|---|---|
| BUYB | .50 | .71 | +.21 | STAS | .50 | .61 | +.11 |
| COMM | .50 | .70 | +.20 | WERI | .50 | .61 | +.11 |
| GRAB | .50 | .64 | +.14 | MAI | .50 | .57 | +.07 |
| DELI | .50 | .62 | +.12 | IRAN | .50 | .55 | +.05 |
| FOOD | .50 | .62 | +.12 | ROBO | .50 | .51 | +.01 |
| SUPE | .50 | .62 | +.12 | TAIL | .50 | .42 | -.08 |
| | | | | HEAD | .50 | .35 | -.15 |

Directional bias: **11/13 drifted UP** from the 0.50 seed (mention markets → words tend to get said). Mean |move| ≈ 0.12.

### Is the seed harvestable?
- Pure microstructure: **NO clean edge.** At t0 every market looks identical (0.54/0.46) — the informative
  repricing IS the correction. 20-min capture floor cannot confirm a sub-20-min actionable seed window.
- HYPOTHETICAL naive "buy YES at 0.54 seed on all 13, exit at final mid" (R104 — unverified, no fills):
  aggregate +0.51 / 13 = **+3.9¢/contract GROSS**, before ~2¢ round-trip fee and mid→bid slippage. Marginal.
- Real edge lives in **per-word external prior** (e.g. company's own name "GRAB", generic "COMM/BUYB" almost
  surely mentioned) vs the flat 0.50 seed. That is a research bet, not a microstructure bet.

## Other series (not the target)
- KXRPRESPRIMARY / KXDPRESPRIMARY: most candidates seeded FLAT at ya .05 / yb .02 (default), 2–3 already
  repriced (R: JVAN .42, MRUB .33; D: KHAR .18). Sum yes_ask 1.14 (R) / 1.04 (D) = overround, **no partition arb**.
  Shorting no-hope 5¢ seeds = buy NO at yb .02 → pay .98 for ~2¢ max, capital locked to 2028. **Dead** (capital-inefficient).
- KXACQANNOUNCEOPENR: single mkt, alternated real 0.67/0.59 with 0.99/0.01 stub snapshots (capture noise), 5¢ spread.

## Verdict: CONDITIONAL (not dead) — gate = external base-rate model
The sloppy window is REAL: new markets, esp. **mention-grab**, open at a uniform 0.50 / flat-seed that ignores
base rates, and the seed persists up to the 20-min capture floor. But it is NOT harvestable as pure
microstructure — converting it to money requires an external prior that diverges from the seed. Mention-grab is
the ripe target (structural upward bias, 13¢ spreads, 11/13 up-drift). Sample is tiny (first 21h look), so this is
a design green-light, not a proven edge.

## Probe sketch (no build)
1. Tail listing_events for NEW_SERIES in high-prior categories (mention-grab first).
2. On first BOOK_LIVE, if seed mid ≈ 0.50 AND an external prior (word base rate / company-self-name heuristic)
   diverges ≥15¢, hit the seeded YES/NO ask.
3. Exit when book reprices to true value (typically within first 1–2h here).
4. **Prereq before any capital:** raise capture to ≤1-min poll for the first 2h of each new mention series to
   measure the true seed-window duration (20-min floor here can't). Then paper-trade the prior model on ≥30
   mention markets to confirm the +move is systematic, not this-sample noise.
