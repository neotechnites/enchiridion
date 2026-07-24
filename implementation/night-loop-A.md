# Night Loop — Lane A (Structure & Venue) · 2026-07-24T08:52:28Z

Runner: NIGHT-IDEATOR lane A, Opus/medium, capped rep. Playbook note 20. 2 inline harness passes over on-disk bases, 0 pulls, 0 sub-agents. Doors chosen to NOT re-skin reps 27/28/29: the **KXINXU S&P-hourly-wing family** and the **idx bracket complete-partition dutch** are both untouched by prior night reps; deep-tail fee-cliff and KBT new-listing re-open on FRESH bases.

## Ledger

| id | idea (mechanism) | kill-test | n | win% | EV-net | verdict | files |
|----|------------------|-----------|---|------|--------|---------|-------|
| A1 | **Index-hourly wing-sell richness (KXINXU)** — S&P hourly "above T" wing buyers are retail lottery flow; sell (buy-NO) 3-25c YES wings like the commodity/crypto edge. WHO's wrong: same uninformed-longshot overpay, new thin family. | buy-NO 3-25c, fresh age≤300s, +1c slip, fee in, event-weighted; **placebo = ATM 40-60c (must be ~0)** | 126 rungs / 59 ev | 87.3 | **evw −3.86c** | **DEAD (artifact)** — wrong direction AND placebo fails: ATM 40-60c = **−10.07c** ≠ 0 → whole base is bull-drift contaminated (Jul 10-22 up-window), wing number is directional not structural. | wing_obs_KXINXU.jsonl (inline) |
| A2 | **Idx bracket complete-partition dutch** — buy NO on every bracket of a mutually-exclusive index-range event; if YES-asks over-round >1, collect (n−1) for less. Conditional re-entry of rep27-A2 with NEW gate: confirm partition completeness + net vs spread+n·ceil-fee. | event ymed-sum + exactly-1-yes check, both families | 43 ev INX / 43 NDX | — | over-round eaten | **DEAD (structural)** — partition IS complete (INX 43/43, NDX 42/43 exactly-1-yes) but ymed-sum 1.180 (INX)/1.085 (NDX) is a MID sum; buy-all-NO nets sum(YES_bid)−1 minus n·ceil-fee (~12 brackets × ~1.5c ≈ 18c) → the vig is the house's, uncapturable as taker. | idx_obs_KXINX/KXNASDAQ100.jsonl (inline) |
| A3 | **Deep-tail fee-cliff dead-zone** — ceil-per-ORDER fee makes 1-3c wings uneconomic to sell for small players, so the ~2c tail overprice survives... or does the fee exactly offset it? | buy-NO 1-3c band, same harness, 2nd family (INXU) | 50 / 34 ev | 22.0 | **+0.15c** | **DEAD-confirmed (structural)** — fair. Matches rep27-A5 (+0.46c fee-noise) on a new family: ceil fee ≈ tail overprice → net zero. Doctrine "deep tails 1-3c FAIR" now 2-family confirmed. | wing_obs_KXINXU.jsonl (inline) |
| A4 | **NDX bracket dutch (thinner variant)** — same as A2 but NASDAQ100 brackets are thinner (nq med 7 vs INX 20), maybe sloppier partition. | same event-sum + completeness | 43 ev | — | — | **DEAD (structural)** — smaller over-round (1.085) + thinner + 1/43 event NOT exactly-1-yes (settlement-ambiguity risk on the "buy all NO" identity). Worse than A2, same fee/spread drag. | idx_obs_KXNASDAQ100.jsonl (inline) |
| A5 | **New-listing first-quote sloppiness on 15M crypto** — freshly-listed KBT market may carry a stale/default quote before MMs arrive; KBT now captures from market open (first snap @ open+0.6s). The untested rep27-A8 door, now disk-testable. | first-snap best-quote vs settle, live-book markets only | ~19 live | — | — | **CONDITIONAL-research** — testable but UNPOWERED: only 37 KBT markets captured, ~half frozen → n≈19 « 60. Gate: re-run at n≥60 live-book markets (~2wk KBT accrual). | kbt_books_*.jsonl |
| A6 | **Being-the-house / maker on index-hourly wings** — capture uninformed wing overpay with a resting fee-exempt ask (the A6 commodity crack, new family). | (subsumed by A1) | — | — | — | **DEAD-cited** — A1 shows the INXU base is DIRECTIONAL, not the uninformed-lottery structure that gives commodity dailies their maker crack. No stable overpay to quote against; taker-only doctrine holds. | — |
| A7 | **INXU close-hour venue-thinness gate** — do specific close_hr_utc (thin/overnight S&P hours) carry a clean wing-sell edge once direction is removed? | clock split on A1 base | 126 / 7 hrs | — | — | **CONDITIONAL-research** — can't gate cleanly while the base is bull-drift contaminated (ATM −10c) and n≈18/hour. Needs detrended base + more data. Park. | wing_obs_KXINXU.jsonl |
| A8 | **KBT L2 spread-floor = 2×ceil-fee** — is there any structural room (taker or maker) on 15M crypto books, or does the quoted spread sit inside the fee floor? | best-bid/ask spread vs 2·ceil-fee, sample book | n=1 book | — | — | **DEAD-as-edge (venue characterization)** — inspected near-ATM 15M book: yes 0.61/0.62 = **1c spread at P~0.6 where 2×fee≈3.4c** → quoted INSIDE the fee-viable floor. Makers already sub-fee-tight; no room for taker or new maker. Cost fact, not counterparty. | kbt_books_btc.jsonl |

## Totals
8 fresh doors. **5 DEAD (structural/artifact/cited), 2 CONDITIONAL-research (A5, A7), 1 DEAD-as-edge characterization (A8). 0 TRADE, 0 PROMISING, 0 survivor cleared anything.**

No graveyard re-litigation: KXINXU index-hourly-wing family, complete-partition dutch, and KBT-first-quote are all doors reps 27/28/29 did not walk. A2/A4 are a legal conditional re-entry of rep27-A2 (new gate = partition-completeness + executable-spread/fee accounting) and closed it structurally.

## Survivors (frozen kill numbers)

**None positive this rep.** Every tested door came back DEAD or negative. Closest live thread:

- **A5 — new-listing first-quote sloppiness on 15M crypto · CONDITIONAL-research.** The only door with a plausible-but-unmeasured edge. Cheapest decisive test is defined (first-snap quote vs settle on live-book markets) and the data pipe (kbt_books) already captures from open — it is simply not yet powered (n≈19 « 60). **Frozen gate: re-run when ≥60 live-book KBT markets have accrued (~2wk).** Zero build cost; rides the existing machine.

## Frozen negatives (keep them dead)
- **Index-hourly wings are NOT a wing-sell family.** KXINXU buy-NO 3-25c evw **−3.86c**, ATM 40-60c placebo **−10.07c** → base is directional, not structurally rich. Do not add INXU to the unified wing book.
- **Idx/NDX bracket partitions are complete (INX 43/43, NDX 42/43 exactly-1-yes) but the 1.18/1.085 over-round is the house's vig** — uncapturable as a taker after spread + ~12×ceil-fee. Rep27-A2 dutch stays dead, now with the completeness number that rep lacked.
- **Deep-tail 1-3c fair on a 2nd family** (INXU +0.15c) — ceil-per-order fee offsets the tail overprice; doctrine confirmed beyond crypto/commodity.

## Pipeline note
The single cheapest un-opened structural door left is **A5 KBT new-listing first-quote** — no pull, no new harness, just accrual on the running `capture_kbt.py` machine until n≥60 live-book markets, then one first-snap-vs-settle pass. Flag for a future rep with accrued data.
