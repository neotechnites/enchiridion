# verify-cpi-ladder (EV4) — CPI YoY point-persistence vs uniform ladder

Date: 2026-07-24. Source: BLS public API v1, series CUUR0000SA0 (CPI-U, NSA index).

## Data limitation (honest)
FRED/ALFRED were network-blocked (HTTP 000). BLS v1 (keyless, unregistered) caps returns at the most recent ~2.5 years regardless of `startyear` — so I have index Jan-2024→Jun-2026 = **17 clean YoY prints** (needs 36; a registered v2 key would extend it). One month (Oct-2025) missing from the BLS return → 16 month-over-month deltas.

## CPI YoY prints (Jan-2025 → Jun-2026)
3.00, 2.82, 2.39, 2.31, 2.35, 2.67, 2.70, 2.92, 3.01, [Oct gap], 2.74, 2.68, 2.39, 2.41, **3.26, 3.81, 4.25, 3.53** (%).
Note the 2026 reacceleration: Feb 2.41 → Mar 3.26 → Apr 3.81 → May 4.25 (jumps of 0.85, 0.55, 0.44pp) — a high-vol inflation regime, not calm.

## Modal persistence: |YoY[m] − YoY[m−1]|, n=16
mean 0.287pp, median 0.245pp.

| within | fraction |
|--------|---------:|
| ≤0.05pp | 3/16 = 18.8% |
| ≤0.1pp  | 6/16 = 37.5% |
| ≤0.2pp  | 7/16 = 43.8% |
| ≤0.3pp  | 10/16 = 62.5% |

## Verdict on the seam: REAL but SMALL and regime-fragile
The EV4 thesis is that a uniform ~4¢-per-bucket curve ignores clustering near the prior print. Reality:
- The modal bucket (prior ±0.1pp) lands only **37.5%** of the time — real concentration (a uniform spread over ~10 buckets would give the mode ~10%, so the mode is ~3–4× underpriced *IF* the book were truly uniform), **but** it is nowhere near a lock. The print moves a median 0.25pp/month.
- The seam is really in the **wings being too rich**, not the mode being a giveaway: 62.5% land within ±0.3pp, so buckets ≥0.4pp away are worth little.
- **Regime kills it right now:** the live 2026 window shows 0.4–0.85pp jumps. In a reaccelerating-inflation regime the modal-persistence bet is actively dangerous — 4 of the last 5 prints broke the ±0.1pp band hard.
- **Fill reality:** these point-ladder books are one-sided (demand clusters at consensus); you cannot passively lift the cheap modal bucket at 4¢ in size — the strawman "uniform 4¢ curve" is not what actually quotes.

Kill taxonomy: **CONDITIONAL (regime)** — modal concentration is real (~38% at mode) in calm months, but the current high-vol CPI regime and one-sided books shrink the exploitable seam. Not a standalone trade on n=16; re-measure with a full 36-month v2 pull in a calm regime.

Files: analysis `/tmp/cpi_yoy.py`; raw `/tmp/bls_cpi.json`.
