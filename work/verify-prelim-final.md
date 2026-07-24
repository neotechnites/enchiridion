# verify-prelim-final (IC5 / MUSK B6) — final-market lock thesis

Date: 2026-07-24.

## Pull status: BLOCKED (skipped gracefully, per protocol)
Revision/vintage data requires an as-published archive (flash value at release + final value later). Every reachable path to that was down in this environment:
- **FRED / ALFRED** (`fred.stlouisfed.org`): HTTP 000 — network-blocked.
- **INSEE BDM** (`api.insee.fr`): HTTP 000 — blocked.
- **BEA API** (`apps.bea.gov`): HTTP 200 but empty body — requires a registered UserID key (not available).
- **Eurostat** (reachable, HTTP 200) returns only the *current* (final) index — no flash/vintage series, so no |final − flash| can be computed from it.

No fresh distribution could be pulled. The below is the **documented revision record** (flagged: NOT a fresh pull), sufficient to rate the thesis conditionally.

## Documented |final − prelim| by print type
| pair | typical |final − prelim| | tight? |
|------|-----------------------|--------|
| Euro-area / French HICP **flash → final** (headline YoY) | 0.0pp majority of months; ≈never > ±0.1pp; matches to 1 decimal >90% | **YES** (ε ≈ 0.1pp) |
| French INSEE CPI provisoire → définitif (headline) | ≤0.1pp almost always, frequently 0.0 | **YES** |
| US GDP **advance → second estimate** (annualized QoQ) | mean abs ≈ 0.5pp, std ≈ 0.6pp | **NO** (loose) |

## Verdict: CONDITIONAL — lock thesis ALIVE for inflation flash→final, DEAD for GDP
- A market that **settles on the FINAL print while the PRELIM is already public** is a near-lock **only for headline flash-inflation** (HICP/CPI provisoire): final tracks prelim within ±0.1pp >90% of the time, so if the market's rung boundaries are ≥0.2pp wide, the prelim already fixes the outcome. → **CONDITIONAL-ALIVE**, gate = (a) inflation flash/provisoire, not GDP; (b) prelim public before the final-market closes; (c) rung width ≥ ~0.2pp so the residual revision can't flip the bucket.
- The GDP advance→second pairing named in the task is **DEAD** for a lock — 0.5pp mean revision routinely crosses a rung.

## Next step to confirm (needs a key)
Register a FRED/BEA API key OR scrape release-day archives to pull ≥24 flash/final pairs for one HICP series and measure the empirical |final−prelim| CDF; confirm >90% ≤0.1pp before sizing any final-market lock. Until then this stays CONDITIONAL on documented (not freshly-verified) revisions.

Files: probes only (no dataset saved); reachability logged above.
