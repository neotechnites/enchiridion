# verify-fomc-move (JOBS #7) — FOMC-day SPX move calibration vs KXSPXFOMC-26JUL29 ladder

Date: 2026-07-24. Deadline event: Jul 29 2026 FOMC.

## Data
- SPX daily closes: Yahoo Finance `^GSPC`, 2021-07 → 2026-07-24, n=1255 days (FRED/stooq were blocked; Yahoo keyless JSON worked).
- FOMC announcement dates: 36 meetings 2022-01 → 2026-06 (statement-day, 2nd meeting day). Metric = |close_t / close_{t-1} − 1|, matching the market's rule verbatim ("absolute percentage change in the S&P 500 Index closing level for July 29, 2026").
- Live ladder: `KXSPXFOMC-26JUL29`, pulled 2026-07-24 (yes = "abs move ≥ X%").

## Realized |close-to-close| on last 30 FOMC days
mean 0.83%, median 0.72%. Exceedance frequencies:

| rung | realized ≥ (30 obs) | recent regime (last 12: 2025→) |
|------|--------------------:|-------------------------------:|
| ≥1.0% | 11/30 = 36.7% | 2/12 |
| ≥1.5% | 5/30 = 16.7%  | 0/12 |
| ≥2.0% | 2/30 = 6.7%   | 0/12 |
| ≥2.5% | 2/30 = 6.7%   | 0/12 |
| ≥3.0% | 0/30 = 0.0%   | 0/12 |

Only 2 moves ≥2% in 30 meetings: 2022-11-02 (2.50%, hiking-panic era) and 2024-12-18 (2.95%, hawkish-cut shock). Last 12 meetings max is 1.36%; the current regime is heavily vol-compressed.

## Live ladder prices (2026-07-24) vs fair
yes_bid / yes_ask (dollars):

| rung | yes_bid | yes_ask | mid | fair (30obs) | fair (recent) |
|------|--------:|--------:|----:|-------------:|--------------:|
| ≥1.5% | 0.15 | 0.21 | 0.18 | 0.167 | ~0.00–0.05 |
| ≥2.0% | 0.06 | 0.10 | 0.08 | 0.067 | ~0.00 |
| ≥2.5% | 0.03 | 0.08 | 0.055| 0.067 | ~0.00 |
| ≥3.0% | 0.00 | 0.05 | 0.025| 0.000 | ~0.00 |

## Verdict: RICH (for buyers) — ≥1.5% and ≥2% rungs
- Buying yes is negative-EV at every rung: ≥1.5% pay 0.21 for a 0.167 event (−0.043 gross); ≥2.0% pay 0.10 for 0.067 (−0.033). Against the recent low-vol regime the overpricing is far worse (realized ~0 for both).
- The edge is on the SELL side but thin after spread+fees. Buy-no on ≥2%: cost 1−yes_bid = 0.94, wins 1 at 93.3% → EV +0.06·0.933 − 0.94·0.067 ≈ −0.007 gross, −0.011 after fee (0.07·0.94·0.06≈0.004). Buy-no on ≥1.5%: cost 0.85, EV ≈ 0.833−0.85 = −0.017. Both marginally negative at the touch — the market has already priced most of the vol-compression.
- **The only real seam is maker-selling the ≥2%/≥2.5%/≥3% tail rungs at the ASK** (offer yes at 0.10/0.08/0.05), collecting the 3–5c overpricing when no big move occurs — subject to (a) one-sided fill reality (thin yes-side demand; you sit at the ask) and (b) genuine tail risk: 1-in-15 meetings delivered ≥2.5% (Dec-2024). n=30 is too few to trust the 3% tail = 0.
- **Trade note for Jul 29:** ≥1.5%/≥2% buyer-side is rich; do not buy the move. Selling the tail is EV-positive on the point estimate but is a small-premium, fat-tail short — size tiny or skip. Not a standalone TRADE; RICH-confirmed calibration.

## Kill taxonomy: CONDITIONAL (regime) — rungs are rich vs both full and recent history, but the tradeable side (maker-sell tail) is fill/tail-limited, not free money.

Files: analysis `/tmp/fomc_analysis.py`; ladder `/tmp/spxfomc.json`; SPX `/tmp/spx_yahoo.json`.
