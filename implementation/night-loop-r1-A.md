# Night-loop rep 1 · Lane A (Structure & Venue) · 2026-07-24 05:49 UTC

Opus/medium, capped overnight rep. Fresh structure/venue doors NOT walked by note-27 / night-rep-A/B (those covered: commodity monotonicity lock, index partition dutch, meat be-the-house, deep-tail sell, far-rung staleness-as-noneedge, maker-wing A6, new-rung research). Cheap kills run inline over on-disk `cwing_obs_*.jsonl` (5 commodity-daily families, fresh age3≤300 + full-staleness variants) + `wing_obs_KXWTIH.jsonl`. Entry realism: sell-YES-wing = buy NO at (1−y)·100 +1¢ slip, per-contract fee 7·y·(1−y)¢, one obs/rung, res-based payoff. Harness: `scripts/laneA_r1.py`.

## Tally: 8 numeric ideas — 5 DEAD-as-stated (structural/wrong-direction), 1 CONDITIONAL (top survivor), 2 CONDITIONAL-research. 0 TRADE, 0 holdout cleared.

> Resume note (relaunch): A1–A7 + survivors below are the completed 05:49 rep, preserved intact. A8 (round-strike anchoring) added on relaunch as one fresh structure door; `laneA_r1_round.py`.

## Ledger

| # | idea | mechanism (who's wrong/why) | kill-test | n | win% | EV-net | verdict | files |
|---|------|------|------|---|------|--------|---------|-------|
| A1 | Thinness-IS-the-edge: thinnest wings richest (small-bankroll reserved) | low-vol far-OTM rungs less arbed → most overpriced | sell-YES EV by vol tercile, placebo=meat | 285 | 90-94 | THIN +2.51 / MID +3.56 / THICK **−0.53** | **DEAD-as-stated** (wrong direction: mid best, thinnest NOT richest) — but structural fact kept: THICK wings ARBED DEAD → cwing edge is a thin/mid-capacity edge (small-bankroll positive) | cwing_obs_* |
| A2 | New-listing sloppiness: wings rich EARLY, decay to T-3h → sell early (maker rest at T-6h) | early MMs haven't calibrated the fresh daily listing | mean(y3−y6) drift + EV each horizon, placebo=meat | 73 | — | drift **+3.18c** (wings RICHEN late); sell@T-6h −1.12 / sell@T-3h +2.21 | **DEAD-as-stated** (wrong direction) — retail piles into lottery wings as event nears; CONFIRMS existing T-3h entry, do not sell early | cwing_obs_* |
| **A3** | **Staleness-as-selection: stale far-OTM wings are systematically richest (be-the-house / thinness)** | a wing nobody refreshed in hours drifts stale-high while true prob→0; last-print age flags the richest rungs | sell-YES EV by age3 tercile; +price-control, meat placebo, event-weight, per-family | 406 | **99.8** | FRESH +3.09 / MID +3.52 / **STALE +4.57** (monotone) | **CONDITIONAL(+gate: stale·deep·non-WTI family)** — clears placebo (meat-stale −1.88), price-control (3-12¢: STALE +4.17 vs FRESH +2.85), event-weight (+3.47c/104 ev), 4/4 families +. Blocker = fill (stale book = thin NO offer) → same probe as A6 | cwing_obs_*, laneA_r1.py |
| A4 | Point-settle favorite fade: commodity 90-98¢ favorites flip MORE than priced (no crypto 60s-avg cushion) | crowd mis-applies crypto lock/averaging intuition to point-settled commodity favorites | realized YES vs priced at 90-98¢; buyNO EV | 175 | — | realizedYES 92.0% vs priced 94.3% → 2.3pp overpriced; buyNO ≈ breakeven (sub-fee) | **DEAD** (structural: overprice real but 2.3pp < round-trip fee; the averaging-cushion contrast exists but is unbankable — mirror of the wing edge, not independent) | cwing_obs_* |
| A5 | WTIH clock: which oil-hourly session carries wing richness | thin/dead session = less-quoted = richer wings | sell-YES EV US(13-21Z) vs OFF | 172 | 84-93 | OFF **+4.34** / US **−3.14** | **NOISE/underpowered** (n=104 off, ~3-day retention; INVERTS unified-book "oil richest in US session +8.7¢" — flag for weekly re-scan, do NOT act; out-of-lane re-skin) | wing_obs_KXWTIH |
| A6 | Settlement identity WTI daily(KXWTI)↔hourly(KXWTIH) same strike | two Kalshi series on same underlying/settlement → dutch if prices diverge | tried to match (strike,settle-time) pairs | — | — | grids/settle-times don't align (daily T74/83 vs hourly T86; suffix hour ≠ hourly close) | **CONDITIONAL-research** (needs a ticker/settlement-time alignment pull to confirm any shared print; not confirmable from current cubes) | — |
| A8 | Round-number strike anchoring: retail lottery-buys YES on round-number strikes → those wings richest → sell them preferentially (venue strike-grid attention) | crowd anchors on round price lines ($5 Brent, $100 Gold); round-strike wing YES over-bid vs off-round neighbours | sell-YES EV round vs off-round in 3-25¢ band; placebo=round meat 40-60¢; per-family | 13 rnd / 272 off | 100 / 92 | ROUND **+9.78** vs OFF +1.48; meat-round placebo −22.7 (edge on the wing, not roundness artifact); Brent within-fam ROUND +10.29 vs OFF −2.88 | **CONDITIONAL-research** (predicted direction holds + clears placebo, but n=13 pooled with 11 of 13 = Brent $5-lines; Gold/NatGas/WTI have 0 round-strike wings at the 10×grid line → underpowered/family-concentrated, correlated-sample trap; needs longer strike history to power the round cell before any weight) | cwing_obs_*, laneA_r1_round.py |
| A7 | Richest-single-rung concentration: is the wing edge one fat rung/event? (fee-cliff sizing) | if edge is 1 rung, size there; else spread the band | sell-YES EV richest-rung/event vs rest | 285 | 87-97 | RICHEST +0.71 / REST **+3.00** | **DEAD-as-stated** (structural sizing fact: the top-priced ~25¢ rung carries genuine risk; edge is in the REST of the 3-25¢ band → sell the whole band, don't concentrate on the richest) | cwing_obs_* |

## Survivors (frozen kill numbers)

### 1. A3 — Staleness selection overlay on the commodity-daily wing engine · CONDITIONAL (top survivor)
**Rule (plain English):** among the 3-25¢ commodity-daily YES wings the cwing engine already sells, PRIORITISE the ones whose last trade is oldest — stale far-OTM rungs are the richest and essentially never hit. Exclude WTI.
**Mechanism:** a far-OTM rung nobody has refreshed in hours holds a stale-high YES quote while realized prob drifts to ~0; last-print age is a free richness selector. Uninformed lottery-flow family (the A6 crack in taker-only doctrine).
**Frozen numbers (sell-YES = buy-NO, +1¢ slip, per-order fee in):**
- STALE tercile (median last-print age 9603s): **n=406, win 99.8%, EV +4.57c**; monotone above FRESH +3.09 / MID +3.52.
- Price-controlled (3-12¢ only, removes strike-depth confound): **STALE +4.17c vs FRESH +2.85c** — ~+1.3c independent staleness premium.
- Event-weighted (one obs/event, correlated-sample trap): **+3.47c across 104 events.**
- Per-family (cross-asset): Brent +3.19 / Gold +3.96 / NatGas +5.77 / Silver +4.60, all win 100%; **WTI −10 (n=8) → excluded** (matches WTI-out doctrine).
- Placebo (meat 40-60¢ stale): **−1.88c** — edge correctly vanishes off the wings.
**Blocker:** fill realization — a stale book means a thin/absent NO offer to take (same problem A6 flagged). Not disk-testable → resolved by the SAME commodity-daily book/quote-capture probe A6 needs. **Does not clear a holdout; cap at CONDITIONAL.** Contribution: sharpens A6 with a concrete free selector (age3), lifting the sell-band from +2-3c toward +4c.

### 2. A6 — WTI daily↔hourly settlement identity · CONDITIONAL-research
Needs a ticker + settlement-time alignment pull; current cubes show non-aligned strike grids. Cheap to check with one metadata pull; queue alongside the A8 new-rung watcher from the prior rep.

### 3. A8 — Round-strike anchoring overlay · CONDITIONAL-research
Suggestive within-Brent (round $5-lines +10.29c vs off −2.88c, right direction, clears round-meat placebo −22.7c) but pooled n=13 with 11 Brent — a single-family/correlated cell below the n≥60 bar. Cheap to re-power once more commodity strike history accumulates (or by loosening roundness to 5×grid, which is a sweep → deferred, not run under cap). If it survives powering, it's a free strike-level selector that stacks with the A3 staleness selector on the same cwing sell-band. Do not weight yet.

### Structural facts retained (not edges)
- **A1:** THICK (most-liquid) commodity wings are ARBED DEAD (−0.53c) — the cwing edge is a thin/mid-volume capacity edge, well-matched to a small bankroll.
- **A2:** commodity wings RICHEN into T-3h (+3.18c drift) — validates the existing T-3h entry timing; do not sell early.
- **A7:** sell the whole 3-25¢ band; the single richest (~25¢-end) rung underperforms (+0.71 vs REST +3.00).
- **A4:** commodity point-settle favorites flip ~2.3pp more than priced (no crypto 60s-avg cushion) — real structural contrast, sub-fee, unbankable.

## Notes
- No graveyard re-tests: crypto dutch/bucket coherence, MVE parlays, fee-floor, honeymoon, funding/kimchi all cited as settled, not re-run.
- Cost: 2 inline harness passes (main + I3 trap), zero pulls, zero sub-agents.
- Highest-leverage next build stays the same single probe the prior rep named: **live commodity-daily book/quote capture** — it unblocks A6-maker, A8 new-rung, and now A3 staleness-selection simultaneously.
