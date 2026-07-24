# Night Loop — Lane A (Structure & Venue) · 2026-07-24T (UTC, overnight rep)

Runner: NIGHT-IDEATOR lane A, Opus/medium, capped rep. Playbook note 20. Fresh doors only — pushed into two zones prior reps (27, 28) never walked: the **15M timescale** (slate only trades hourly/daily/calm-clock crypto) and the **Polymarket-US venue** (prior reps were Kalshi-only). 2 real inline harness passes + placebos, 0 pulls, 0 sub-agents.

## Ledger

| id | idea | mechanism (who's wrong) | kill-test | n | win% | EV-net | verdict | files |
|----|------|------|-----------|---|------|--------|---------|-------|
| A1 | 15M near-ATM opening-print calibration (BTC/ETH/SOL/XRP) | 15M lists every 15min; if MMs set sloppy opening quotes, the first-print is mispriced vs result | bucket open(1-5s) price vs realized win% | 4324 | — | fade EV −1.0 to −3.5c | **DEAD** (structural — calibrated; bias <2c all buckets, big coins) | scratch_15m_open.py |
| A2 | DOGE 15M opening-fade (favorite-longshot / intra-15m mean-reversion) | retail momentum pushes thin DOGE 15M open to extremes; DOGE reverts | open-entry fade-to-50 vs **established(60-120s) entry** + era split | 201 | 31% | open +5.9c → **established −1.6c** | **DEAD** (structural — stale-open mirage: open→est move 13.3c; fill-realistic entry negative; edge one-era: late +14.9 / early −4.2) | scratch_doge.py |
| A3 | Poly-US crypto-ladder monotonicity dutch (BTC/ETH "above X on date") | multi-strike Poly ladder mispriced → higher strike quoted above lower | count adjacent-strike ymid inversions >0.5c | 1457 pairs | — | 11/738 BTC, 18/719 ETH (~2%, noise-level) | **DEAD** (structural — near-tight, sub-0.5c) | scratch_poly.py |
| A4 | Poly-US crypto-ladder calibration fade | Poly "above" ymids systematically off → buy-NO the rich side | ymid bucket vs realized res | 1601 | — | uniform −0.03 to −0.10c (BTC every bucket) | **DEAD** (regime-fake — uniform-direction = down-drift over capture window; stale snapshots age~84h, not tradeable entry) | scratch_poly.py |
| A5 | Being-the-house at the DOGE 15M stale-rich open (rest a maker into the 13c open-move) | wide/uninformed opening quotes look rich → quote the other side | is the 13c open→est move adverse selection? | — | — | — | **DEAD-cited** (taker-only doctrine, note 18 — resting = informed-flow magnet; A2's 13.3c open-move IS the pickoff, confirms it) | — |
| A6 | Fee-cliff C* structural (1-ct extreme-price penalty) | ceil-per-order fee 0.07·P(1−P) makes tiny clips at extreme prices pay 5× | fee at 3¢ vs C* floor | — | — | — | **DEAD-as-edge** (structural cost, not counterparty edge; already doctrine, fee_floor.py) | — |
| A7 | KBT frozen-book (~48%) being-the-house | ~half of KBT books are source-frozen → quote where they're stale | can you fill a frozen book? | — | — | — | **DEAD** (structural — frozen = no counterparty, no fills; note 18) | — |
| A8 | Cross-venue Poly↔Kalshi crypto settlement identity | same crypto event on both venues → settlement lock | prior basis test | — | — | — | **DEAD-cited** (graveyard I6 basis-not-dutch / I7 offshore-premium; corroborated: A4 Poly snapshots are stale, no tight cross-lock) | — |

## Totals
8 ideas. **7 DEAD (structural/cited), 0 CONDITIONAL+, 0 TRADE, 0 survivors.** Two genuinely fresh doors (15M timescale, Poly-US venue) opened and closed with numbers. Zero graveyard re-litigation (A5/A6/A7/A8 cited from doctrine, not re-run).

## Survivors
**None cleared.** The rep's product is two fresh doors closed with frozen kill numbers:

- **15M near-ATM binaries are efficient at the open** for liquid coins (BTC/ETH/SOL/XRP opening-print bias <2c per bucket; fade EV −1 to −3.5c). Door closed.
- **DOGE 15M opening-fade — the one door that lit up (+5.9c at open-entry) — is a stale-open-print artifact, not an edge.** Frozen kill numbers: mean open(1-5s)→established(60-120s) move = **13.3c**; at the fill-realistic established price the fade goes to **−1.6c (n=201)**; the raw open-entry edge is one-era (late +14.9c / early −4.2c). Same class as the A1 stale-print mirage and kbt frozen-book traps. The apparent edge requires transacting at a stale extreme that nobody actually offers.
- **Polymarket-US crypto ladders** are monotone within ~2% (sub-0.5c, no dutch) and their apparent calibration bias is a directional down-drift over the capture window on stale (age~84h) snapshots, not a pricing edge.

## Pipeline note
The 15M open→established 13.3c move quantifies how sloppy DOGE 15M opening quotes are — a large stale/uninformed spread that decays in ~60-120s. It is NOT takeable (stale mark) and NOT quotable (taker-only; you'd be the pickoff). If Kalshi ever ran a maker-rebate/LP promo on these thin 15M books, that same 13c uninformed spread would flip from trap to being-the-house edge — flagging as a venue-promo watch item, not a current trade.
