# 38 - The Strategy Book (living inventory)

> THE single source of truth for the pipeline. **The pipeline IS Ryan's four columns
> (canonical, 2026-07-25): CREATED → TRIAL TESTED → TESTED BY FABLE → WORTH IMPLEMENTING (or
> dead at any stage).** Every idea sits in exactly one column. The weekly Fable review is the
> column-3 process: it takes trial-test survivors and moves each to column 4 or the grave.
> Post-compaction Claudes: this frame was lost once and Ryan had to restore it by force — it
> lives HERE now; never answer pipeline questions in any other structure.
>
> **EVIDENCE CLASSES (Ryan's rulings, 2026-07-25 — CORRECTED same day, his words):** Class A =
> full multi-year backtest; Class B = short history. *"dont assume strategies with less
> backtesting are more likely wrong, assume they are more likely to STOP EXISTING… if we see
> something thats been going on for 2 months, we dont need to be more cautious about it, it will
> likely continue, but it will likely continue for less time."* Handling differs by LIFETIME,
> not confidence: Class B edges are harvested at full appropriate size WHILE ALIVE, with tight
> decay tripwires and low weight in long-horizon projections. Class A terrain gets the hunting
> effort because durable edges compound. (Any new strategy's first live days start small for
> EXECUTION validation — that's mechanics, class-independent, and temporary.)

## COLUMN 4 — WORTH IMPLEMENTING (cleared Fable testing)
- **VOLBOOK** [Class B — 9.5wk = API retention cap; harvester accruing toward 2yr] — metal
  daily-wing seller Mon-Wed by calibration gap. **BUILT 2026-07-25 (nestor 0f89954), Fable-
  reviewed, paper-shadowing** — per-bucket willingness-to-pay ceilings, triple-gated from live
  (VOLBOOK_LIVE=1 + run-wiring + sizing all required). First live-shaped window: Mon T-3h.
  AWAITING RYAN: sizing (flat/day/cluster $, small per lesser-class rule). Queued enhancements:
  copper dollar half-weight (needs small risk ext; ranking already demotes it), oil flag
  (verdict said minimal-not-zero; shipped OFF, one flag-flip).

## GRADUATED (implemented, live)
- **STREAK** [Class A — 2yr backtest] — fade 4-streaks ≤44¢, BTC+ETH 15m. $106.03, first win
  banked, retry binary live 2026-07-25. Week-2 sizing pending.

## COLUMN 4 additions — FABLE REVIEW 2026-07-25 (primary ledgers read)
- **H10 econ point-ladder maker** + **H9 political spread-capture** — IMPLEMENTED SAME DAY
  (Ryan's blanket authorization): maker module + house probe sleeve built (nestor e1b989d),
  demo-proven mechanics (build-house-probe.md DEMO EVIDENCE: resting = good_till_canceled +
  future expiration_ts + taker_at_cross; expiration enforcement LAZY ~2-3min → worst-case
  orphan ~4min at 75s TTL, accepted at size 1; resting list eventually-consistent — responses
  are truth). ARMED (HOUSE_PROBE=1): quotes 1-lot two-sided when spread ≥2¢, −$20 in-code sticky
  stop, probe metrics via `nestor house-report`. Promote/kill per protocol after 2-3 days.

## COLUMN 3 — AWAITING FABLE TESTING (with tonight's evidence workers in flight)
1. **SEED-PRIOR** listing harvest — TOP of column 3. Gate #2 CLOSED (build-seed-prior.md):
   hierarchical word-prior from the FULL settled corpus (14,410 markets — 30× the first lane,
   which hadn't paginated), LOO-validated +26.4¢ net / 0.774 hit on the confident subset
   (n=7,798); artifact ~/kalshi_data/seed_prior.json + runtime procedure written. Remaining
   gate #1 (closes passively): confirm non-earnings families get the flat 0.54 seed — the
   60s fast-snap catches the next new listing's seeds. Caveat held: confirmed-seed family
   (earnings) is only +5.8¢; the fat edge rides on unverified-seed families.
2. **DOGE MAX ivol ramp** — RULING (verify-doge-ivol.md): real but NOT money. Persists across
   both retrievable issuances (4/4 NO wins, ~+33¢/contract first-hour) BUT DOGE-specificity
   FALSIFIED (BTC/ETH tops same pattern same months = quiet-regime artifact, rally month
   unsampled, n=2) and capacity ~$5/issuance vs $50-150 claimed. BENCHED; trigger = MAX-ladder
   proliferation (more issuances/coins → capacity) or a vol-regime gate.
3. **INFLATION-FLASH** — RULING (probe done, verify-inflation-flash-access.md): the front-run
   path is STRUCTURALLY DEAD (prelim-settled markets freeze trading 1 min BEFORE the release);
   access itself is solved (Eurostat flash = free keyless JSON). Surviving sliver: KXHICP
   (final-settled, ~17-day window, flash public) — narrowed gate: calibration check of the book
   vs the ≤0.1pp revision base rate, next burst.
4. **SPOKEN-COUNT** (MUSK family) — vehicle-gated; Tuesday's capture builds Warsh base rates.
5. **DUTCHBOOK** — ruling: stays 3; measured median 0.65¢ is sub-slippage. Gates: detector
   opposite-direction fix (queued athena change) + ≥1.5¢ + leg-quote-lifetime capture accrual.
6. **FED-HOLD** — stays 3; n=1, nothing reviewable until forward capture accrues.
7. **ZERO-FEE SERIES** — stays 3; trigger DEFINED: any of the 13 series showing OI>1k or a
   two-sided book (queued: add to listing_monitor watch).
8. **RENTEC forward doors R10-R12** (coin-trajectory model on fresh channel / hourly books /
   cross-coin breadth) — capture-gated: need LIVE-orderbook capture (queued athena demand).

## REVIEW CORRECTIONS + CAVEATS (2026-07-25 Fable pass)
- **RENTEC R1 flagship: DEAD, RETRACTED by its own burst-2** (KBT source-frozen-book artifact;
  de-contaminated EV −12.5¢ uniform; the +31¢ leader idea was stale-book lookahead, 224/225
  frozen). My earlier Book entry "PROMISING held" propagated an R127 log-compression ERROR —
  the primary ledger says the opposite. Lesson: the Book links to ledgers; reviews read ledgers.
- **KBT FROZEN-BOOK CAVEAT on streak-retry numbers:** ~half of KBT-captured books carry a stale
  book field (R13 doctrine, lane-RENTEC-burst2). Lane 1's flicker/fill-rate projections (0.926 /
  88.5%) did not churn-filter and may be inflated. The retry FIX stands (our own live post-miss
  Kalshi-API snapshots showed the ask present — independent of KBT), but projected fill rates
  are UNVERIFIED until live fills accumulate. Live tape supersedes.

## OUTSIDE THE FUNNEL (Ryan's pre-senate slate — alive, own tracks)
- **PCE/GDP index event wings** + **MSFT/META gap wings** — calendar-scheduled Jul 30 (adjacent-
  kill flag from EVENT-VOL noted; INDEX family unaffected).
- **WEATHER** — NOT dead; parked by Ryan's own streak-first redirect. In-sample calibration (8
  city biases) intact; named gate = FORWARD out-of-sample (ens_forward capture daily since ~Jul
  23, matures ~mid-Aug) → returns to a weekly review with real evidence then.
- **LOCK** — DECAY-benched (not structural): +1.72¢/contract in the old window → −1.07¢ on the
  recent kill-scan; the market closed it. Re-entry test built in (`nestor backtest-lock`).

**WEEKLY REVIEW STANDING DUTIES (added 2026-07-25 after Ryan's "are we killing real edges"
challenge):** every weekly review must (a) run `backtest-lock` re-entry scan, (b) check weather
forward-capture maturity, (c) sweep the DECAY bench and every column-3 trigger — a bench whose
re-entry checks never run is a graveyard with extra steps.
- **POLYLAG** — auto-gated: analyze only tapes containing a ≥3¢ Poly move (daemon running).

## COLUMNS 1-2 — CREATED / TRIAL TESTING
Continuous (ideator bursts + verify lanes). ~200 raw ideas processed to date; trial-test
mortality is the funnel working (cheapest decisive kill first). Survivors surface into column 3.

## DEAD (with numbers — money the kills saved)
FOMC-move ladder buying (0/12 recent clear the priced move) · EIA-day wings (−6..−8¢ every
family) · crypto favorite-buying 95-98¢ (n≈4,700) · xvenue MLB arb (pre+in-game; artifacts) ·
deribit hourly gate (tenor artifact) · earnings-MENTION lag (closes-on-occurrence) · naive
count-lag (29/29 priced) · buy-the-jump on liquid ladders (n≈1,576) · thin-frontier structural
locks (4,740-event scan).
