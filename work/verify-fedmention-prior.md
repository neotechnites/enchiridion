# Lane 5 — FEDMENTION-PRIOR (KXFEDMENTION-26JUL) — PREP ONLY

Built 2026-07-25. Event locks at end of the **Jul 29 2026** FOMC presser. No trading; Ryan decides Tuesday.

## Method
- **Rungs/prices:** live from Kalshi API (`markets?event_ticker=KXFEDMENTION-26JUL`), 43 markets, `*_dollars` fields. Price shown = mid = (yes_bid+yes_ask)/2.
- **Settlement rule (from `rules_primary/secondary`):** YES if **the Chair** says the word **≥1 time** during introductory remarks **+ Q&A**. Exact word plus **plural/possessive** count; other tense inflections do not. Binary — count magnitude only matters as ≥1.
- **Transcripts:** downloaded 12 FOMC press-conference PDFs from federalreserve.gov (2025-01-29 … 2026-06-17), extracted with PyMuPDF **to disk only** (transcript text never entered agent context). Counting done programmatically: per-transcript speaker segmentation (line-anchored ALL-CAPS headers), keep **Chair-only** speech, regex word/phrase match with plural/possessive, case-sensitive for acronyms (AI/ADP/QT/QE).
- **Priors:** `p_powell` = fraction of the 11 Powell pressers where the Chair said it ≥1x. `p_nonSEP` = same but restricted to the 6 non-SEP meetings (Jan/May/Jul/Oct-type). `Wjun` = raw Warsh count in his June presser.

## TWO STRUCTURAL CAVEATS THAT DOMINATE EVERYTHING
1. **SPEAKER CHANGE.** July 29 presser is **Kevin Warsh**, not Powell. June 17 2026 was **Warsh's first presser** (1 data point). All other 11 transcripts are Powell. Powell-based priors are the wrong speaker's habits.
2. **NON-SEP MEETING.** July has **no Summary of Economic Projections**. Words tied to SEP ("Projection", "Goods inflation") are far more common at SEP meetings (Mar/Jun/Sep/Dec). Conditioning on non-SEP corrects them.

**Net: once you condition on (Warsh) + (non-SEP), the market is ~efficient.** Every ≥15¢ divergence vs the naive Powell prior is explained by these two artifacts — the naive prior is mis-specified, the market is not obviously mispriced. This is the headline. 17 rungs "flag" against the naive prior; none survive as a clean edge after conditioning.

## Full table (sorted by |divergence vs Powell prior|)
mid = market; pPow = Powell n=11; pNS = non-SEP n=6; hits = Powell hit count; Wjun = Warsh June raw count; div = mid − pPow.

| rung | word | mid | pPow | pNS | hits | Wjun | div | flag |
|------|------|-----|------|-----|------|------|-----|------|
| GOOD | Good Afternoon | 0.17 | 1.00 | 1.00 | 11/11 | 0 | -0.83 | FLAG |
| GOODD | Good Day | 0.74 | 0.00 | 0.00 | 0/11 | 1 | +0.74 | FLAG |
| PAND | Pandemic | 0.18 | 0.91 | 0.83 | 10/11 | 0 | -0.72 | FLAG |
| ANCH | Anchor / Anchored | 0.29 | 0.91 | 0.83 | 10/11 | 0 | -0.61 | FLAG |
| LAYO | Layoff | 0.30 | 0.91 | 0.83 | 10/11 | 0 | -0.60 | FLAG |
| UNCH | Unchanged | 0.29 | 0.82 | 0.83 | 9/11 | 0 | -0.52 | FLAG |
| SLOW | Slowdown / Slow Down | 0.14 | 0.55 | 0.67 | 6/11 | 0 | -0.40 | FLAG |
| REST | Restrictive | 0.61 | 1.00 | 1.00 | 11/11 | 1 | -0.39 | FLAG |
| PROJ | Projection | 0.36 | 0.73 | 0.50 | 8/11 | 4 | -0.36 | FLAG(SEP) |
| OIL | Oil | 0.70 | 0.36 | 0.33 | 4/11 | 2 | +0.34 | FLAG |
| UNCE | Uncertainty | 0.67 | 1.00 | 1.00 | 11/11 | 1 | -0.33 | FLAG |
| GOODS | Goods inflation | 0.23 | 0.55 | 0.17 | 6/11 | 0 | -0.32 | FLAG(SEP) |
| QT | QT / Quantitative Tightening | 0.14 | 0.36 | 0.33 | 4/11 | 0 | -0.23 | FLAG |
| CRED | Credit | 0.27 | 0.46 | 0.67 | 5/11 | 0 | -0.19 | FLAG |
| SOFT | Softening | 0.17 | 0.36 | 0.33 | 4/11 | 0 | -0.19 | FLAG |
| CENT | Central Bank | 0.81 | 0.64 | 0.83 | 7/11 | 8 | +0.18 | FLAG |
| TAX | Tax | 0.26 | 0.09 | 0.00 | 1/11 | 0 | +0.16 | FLAG |
| AI | AI / Artificial Intelligence | 0.77 | 0.64 | 0.50 | 7/11 | 6 | +0.13 | |
| PROD | Productivity | 0.77 | 0.64 | 0.50 | 7/11 | 5 | +0.13 | |
| CONS | Consumer Confidence | 0.12 | 0.00 | 0.00 | 0/11 | 0 | +0.12 | |
| SHUT | Shutdown / Shut Down | 0.15 | 0.27 | 0.33 | 3/11 | 0 | -0.12 | |
| EGG | Egg | 0.12 | 0.00 | 0.00 | 0/11 | 2 | +0.12 | |
| BAL | Balance Sheet | 0.82 | 0.73 | 0.67 | 8/11 | 5 | +0.10 | |
| NATI | National Debt | 0.10 | 0.00 | 0.00 | 0/11 | 0 | +0.10 | |
| GAS | Gas / Gasoline / Natural Gas | 0.28 | 0.18 | 0.17 | 2/11 | 0 | +0.10 | |
| RECE | Recession | 0.18 | 0.27 | 0.17 | 3/11 | 0 | -0.09 | |
| IRAN | Iran | 0.17 | 0.09 | 0.17 | 1/11 | 0 | +0.08 | |
| RENO | Renovation | 0.07 | 0.00 | 0.00 | 0/11 | 0 | +0.07 | |
| PROB | Probability | 0.23 | 0.27 | 0.00 | 3/11 | 0 | -0.05 | |
| DOLL | Dollar | 0.32 | 0.36 | 0.50 | 4/11 | 0 | -0.05 | |
| ADP | ADP | 0.14 | 0.09 | 0.17 | 1/11 | 0 | +0.04 | |
| CRYP | Crypto / Cryptocurrency | 0.14 | 0.09 | 0.17 | 1/11 | 0 | +0.04 | |
| VOLA | Volatility | 0.14 | 0.18 | 0.17 | 2/11 | 0 | -0.04 | |
| PRES | President | 0.41 | 0.36 | 0.67 | 4/11 | 1 | +0.04 | |
| TRU | Truflation | 0.04 | 0.00 | 0.00 | 0/11 | 0 | +0.04 | |
| GOLD | Gold | 0.07 | 0.09 | 0.17 | 1/11 | 0 | -0.03 | |
| DISS | Dissent | 0.15 | 0.18 | 0.33 | 2/11 | 0 | -0.03 | |
| SHOC | Shock | 0.53 | 0.55 | 0.67 | 6/11 | 1 | -0.02 | |
| QE | QE / Quantitative Easing | 0.12 | 0.09 | 0.17 | 1/11 | 0 | +0.02 | |
| STAG | Stagflation | 0.10 | 0.09 | 0.00 | 1/11 | 0 | +0.01 | |
| NQE | Event does not qualify | 0.01 | 0.00 | 0.00 | 0/11 | 0 | +0.01 | |
| BITC | Bitcoin | 0.09 | 0.09 | 0.17 | 1/11 | 0 | +0.00 | |
| TRUM | Trump | 0.10 | 0.09 | 0.17 | 1/11 | 0 | +0.00 | |

## Interpretation of the flags (why they are NOT edges)
- **Powell-boilerplate that Warsh dropped (market repriced DOWN, Warsh Jun=0 confirms):** PAND, ANCH, LAYO, UNCH, SLOW, SOFT, QT, CRED. Powell said these 45-91% of the time; Warsh said none in June; market sits 0.14-0.30. Market is tracking Warsh, not Powell. **Prior is stale, not the price.**
- **Powell tics Warsh uses far less (Wjun=1 vs Powell avg 5):** REST (mkt 0.61), UNCE (mkt 0.67). Still ≥1 in June so binary-hit, but market's discount from Powell's ~1.00 is directionally right.
- **Greeting flip (verified):** Powell opened "**Good afternoon**" 11/11 (never "Good day"). Warsh opened June with a clean standalone "**Good day**" and did NOT say "good afternoon." Market: GOODD 0.74 / GOOD 0.17 — already inverted to match Warsh. Prior column is exactly backwards here.
- **SEP-only words (July has no SEP):** PROJ pNS=0.50 (vs 0.73 all-Powell) → mkt 0.36 gap shrinks to ~-0.14, no longer a flag. GOODS pNS=0.17 → mkt 0.23 now *aligned*. Calendar artifact, market efficient.
- **Warsh talks up (market repriced UP, Wjun high confirms):** OIL (Wjun=2, mkt 0.70), CENT (Wjun=8, mkt 0.81), BAL (Wjun=5, mkt 0.82), AI (Wjun=6), PROD (Wjun=5). Market leads the Powell prior in the correct direction.

## Residual CONDITIONAL candidates (n=1 Warsh evidence — PREP notes, NOT recommendations)
Per enchiridion-15, named with gates; all rest on a single Warsh presser so size would be lottery-only if ever taken.
1. **GOODD "Good Day" YES @ ~0.74.** If "Good day" is Warsh's fixed opener (n=1: he used it, Powell never did), true P ≈ 0.85-0.90 → ~+11-16¢. **Gate:** confirm from ≥1 more Warsh public appearance that "Good day" is his habitual greeting (not a one-off). Mirror: **GOOD "Good afternoon" NO @ 0.17** if Warsh has abandoned it.
2. **EGG YES @ 0.12.** Warsh used "eggs" 2x in June in cost-of-living framing ("dozen eggs", "eggs, or milk"); Powell 0/11. If that grocery-basket rhetoric is a persistent Warsh motif, 0.12 underprices. **Gate:** verify eggs/grocery-basket framing recurs in Warsh remarks; else treat as June-topical noise. Weak.

## Data quality / provenance
- All 12 PDFs HTTP 200, 42-57k chars each; chair-only segmentation captured 51-63% of transcript chars per presser (sanity-consistent). One extraction bug caught and fixed: initial pass dropped every opening statement (greeting/scripted words undercounted to 0) — corrected by line-anchoring headers; June typo label "CHARIMAN WARSH" folded into Warsh. Greeting and egg matches spot-verified with minimal context (no bulk text into ledger).
- Money-impact: none claimed (prep only, no trade).
- Artifacts on disk: `/tmp/fedtx/` (PDFs, `txt/*.chair.txt`, `counts.json`, scripts). Live prices snapshot: `/tmp/fedjul.json`.

## Verdict
**No clean tradeable edge as of build.** The order book already prices the Warsh regime and the non-SEP calendar; the large historical-vs-market gaps are artifacts of an inapplicable (Powell/SEP) prior. Best-defensible lean if Ryan wants a lotto Tuesday: **GOODD "Good day" YES** (greeting-habit gate). Everything else: hold. Revisit only with more Warsh transcripts to build a real speaker-specific prior.
