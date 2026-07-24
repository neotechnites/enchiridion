# Night Loop — Lane A (Structure & Venue) · 2026-07-24T09:56:40Z

Runner: NIGHT-IDEATOR lane A, Opus/medium, capped rep. Playbook note 20. 0 sub-agents, 0 pulls.
2 inline decisive kills RUN on disk (S1 range-seam dutch; S4 15M-ETH new-listing first-print, n=997); rest DEAD-cited against the **seam toolkit** (idx_seam / range_seam / analyze_cross_eth / xseam — a full settlement-identity harness set that reps 27–30 never cited) + `computable_class_catalog.json`. Fresh doors only; no re-skin of the live slate or prior Night notes.

## Key discovery this rep
The settlement-identity / dutch door is **already fully built out** in `~/kalshi_data/scripts` (idx_seam.py, range_seam.py, analyze_cross_eth.py, xseam_*.py, seam_scan.py) and `computable_class_catalog.json` catalogues the computable-settlement-ratchet universe (only survivor: KXLASTWORDCOUNT, dormant). None of this was in Night notes 27–30. This rep consolidates its verdict into the ledger with a fresh clean kill number.

## Ledger

| id | idea | mechanism (who's wrong) | kill-test | n | calib/win | EV-net | verdict | files |
|----|------|--------------------------|-----------|---|------|--------|---------|-------|
| S1 | Range-bucket↔15M settlement-identity dutch (KXBTC hourly $100 bucket vs 15M strike, same BRTI top-of-hour instant) | two order books, one settlement number; over-round should be lockable | fresh-vs-stale net-edge split on `range_seam_out.json` (54 opps/153h) | 54 | — | **FRESH(≤5s): max +0.63c, 0 opps ≥2c**; STALE(>5s): mean +3.67c max +24c | **DEAD (structural)** | range_seam_out.json, range_seam.py |
| S2 | Index daily↔hourly settlement-identity dutch (KXINX brackets ↔ KXINXU cumulative, same 20:00Z S&P) | same, index crowd | idx_seam built; over-round = house vig | — | — | 1.180 over-round eaten by spread + ~12×ceil-fee (rep30 A2) | **DEAD-cited (structural)** | idx_seam.py |
| S3 | 15M↔daily cross-horizon dominance (KXBTC15M nested in hourly, same instant) | slower daily crowd lags fast 15M | analyze_cross_eth Test C dominance | — | — | coherent within fees when fresh (same class as S1) | **DEAD-cited (structural)** | analyze_cross_eth.py, KXETHD_cross.jsonl |
| S4 | New-listing first-print sloppiness, 15M ETH ladder (powered analog of rep30-A5 KBT) | freshly-listed 15M rung's first trade is uninformed vs BRTI-fair | first-print calibration + FOLLOW/FADE EV vs settlement, placebo=last-print | **997** rungs / 521 ev | first-print err ≤+1.7pp (except [60,80) +6.0pp n=68) | **FOLLOW −0.34c, FADE −2.65c** | **DEAD-as-edge (efficient)** | KXETHD_cross.jsonl (inline) |
| S5 | Min-tick(1c)-vs-fee-floor being-house in deep wings | at 3–8c taker fee ≈0.2–0.5c < 1c tick; fee-exempt maker NO captures full tick | cost identity | — | — | quoted inside fee floor; adverse (taker-only) | **DEAD-cited (cost fact)** | rep30 A8, fee_floor.py |
| S6 | Kalshi maker-rebate / LP incentive program (promo door) | a rebate flips uninformed thin-book spread from adverse-selection trap → being-house edge | none on disk | — | — | — | **CONDITIONAL-research → SURFACE to Ryan** | (none) |
| S7 | Thinner commodity-daily families beyond the 5 (Platinum/Copper/Cocoa/Wheat) | thinner ladders = sloppier uninformed wing flow; extends A6/L5 capacity w/ more independent bets | one market-list pull (<15min), then cwing_analyze | — | — | — | **CONDITIONAL-research (unrun, cost)** | cwing_pull.py, cwing_analyze.py |
| S8 | Being-the-house on Poly-US crypto ladder (maker fee = 0) | Poly maker is free; rest inside the wide thin spread | needs Poly fill/trade tape (not on disk) | — | — | — | **CONDITIONAL-research** | rep29 A3/A4, poly_ladder_*.jsonl |
| S9 | KBT 15M first-quote sloppiness (carried from rep30-A5) | stale/default quote at open+0.6s before MMs | first-snap-vs-settle at n≥60 live-book | ~19 | — | — | **CONDITIONAL-research (prior lowered by S4)** | capture_kbt.py |

## Survivors (frozen numbers)

**No new TRADE / PROMISING. Nothing clears a holdout.** Structure/venue lane is heavily mined (seam toolkit + reps 27–30 + catalog).

Frozen kill numbers:
- **S1 (settlement-identity dutch, DEAD):** fresh-print (both legs ≤5s) max net **+0.63c**, **0/54 opps ≥2c**. Every fat dutch (+2c→+24c) carries stale ≥6–30s on the matched leg → unfillable stale-quote artifact. Built-in placebo = the fresh/stale split: fresh shows nothing, stale lights up → artifact confirmed. Kills the settlement-identity family (S1/S2/S3) structurally.
- **S4 (15M new-listing first-print, DEAD-as-edge):** n=997 rungs / 521 events. First-print calibration error ≤+1.7pp in every populated bucket except a thin one-sided [60,80) slice (realized 75 vs px 69, +6.0pp, n=68). FOLLOW-first EV **−0.34c** (≈ fee drag), FADE-first **−2.65c**. Powered confirmation of rep29-A1 (open calibrated) and it **lowers the prior on S9 (KBT A5)** — the powered new-listing-first-print door is efficient.

Best forward thread (small-bankroll aligned): **S7 thin-family scout** — one <15min market-list pull to see if Platinum/Copper/Cocoa/Wheat daily wings exist and are thinner than the current 5; if so they extend the live cwing wing-sell (A6/L5) capacity with fresh independent bets, exactly the small-bankroll capacity edge the staged goal wants. **S6 LP-program** is the one genuinely unwalked venue door and is Ryan-only to inquire — surfaced per playbook.

## Notes
- The [60,80) +6pp first-print flicker (S4) is thin, one-sided, FOLLOW-EV-negative → noise, not promoted (matches the standing first-print-slightly-underprices-favorite-YES drift). No garden-of-forking-paths flip.
- Cost: 2 inline disk passes, 0 pulls, 0 sub-agents.
