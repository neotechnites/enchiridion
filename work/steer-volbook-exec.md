# STEER: Volbook execution fit + Monday conformance predictions (2026-07-26)

> **You are implementing an INTENT; specs and prior policies are evidence, not truth.
> Enumerate the design decisions your analysis rests on; derive each or flag it UNDERIVED.**

## Mission
Apply the streak execution-fit METHOD (worked example: `work/verify-streak-execution.md` —
read it first, it is the template) to VOLBOOK's entry moment. Volbook (live Monday, first
window ~T-3h before metal daily close): buys NO on OTM metal wings (implied YES 0.05-0.35),
IOC at a per-bucket willingness-to-pay ceiling, 1+1 retry, first-sight entry ranked by
calibration gap. Two questions, in order of value:

1. **Is first-sight the right entry time?** From the captured metal books, reconstruct the
   NO-ask path through the entry window (entry_ttc_lo..entry_ttc_hi from
   `~/Documents/senate/nestor/data/volbook_calib.json`). Does the wing ask dip/decay inside
   the window (sellers arriving, spreads tightening) or is early best? Produce the same
   artifacts as the streak ledger: ask-path distributions, P(ask ≤ L within window) fill
   table, E[EV captured] per policy (current take-at-first-sight-≤ceiling vs take-at-best-
   observed-time vs rest-bid-at-L + take-backstop-at-ceiling-before-window-end).
2. **Monday conformance predictions** (the ritual that caught the settled-filter bug, run
   proactively): from calib + the captured books, predict Monday's decision stream — expected
   qualifying rungs per series (gold/silver/copper), expected skip-reason histogram, expected
   fill EV range. Write them as falsifiable numbers so Monday's tape can be reconciled
   against them line by line.

## Data
- `~/kalshi_data/cwing_books_KXGOLDD.jsonl`, `_KXSILVERD.jsonl`, `_KXCOPPERD.jsonl` (also
  KXNATGASD/KXBRENTD, secondary) — metal daily book snapshots since ~Jul 24. Decode the
  schema from the capture script (`~/kalshi_data/scripts/capture_cwing_books.py`) — do NOT
  guess field meanings. API truth: only *_dollars/_fp fields are live on Kalshi payloads.
- Apply the R13 stale-field discipline: drop byte-duplicate/frozen snaps, require backing
  size, and say what fraction you dropped.
- `data/volbook_calib.json` in the nestor repo — ceilings, wing band, entry window, margins.
- Note the sample is DAYS, not weeks — label everything with its n and be conservative;
  Thu-Sun tape may not represent Mon-Wed (the edge is Mon-Wed only). If the tape contains
  zero Mon-Wed entry windows, say so loudly — shape conclusions then rest on the wrong days
  and the fill table is indicative only.

## Constraints
- A maker (resting-bid) leg is ANALYSIS ONLY — maker toxicity on these books is unmeasured
  (the house probe exists to measure it; graveyard says resting orders are pickoff magnets
  91-100% in fast books; slow deep metal books may differ). Present the EV table; recommend;
  do not treat resting as shippable until the house probe reports.
- Fees: taker 7·P·(1−P)/100¢ per contract; if you model maker fills, flag the maker-fee
  assumption explicitly (schedule unverified).
- Cheapest decisive pass first; ONE lane, no subagents, no sweeps. Work on disk under
  ~/kalshi_data/scripts/ (analysis scripts) — do NOT touch the nestor repo.
- Money-impact numbers only by reconstruction from the tape, never inference.

## Report → work/verify-volbook-execution.md
Same shape as the streak ledger: data + filters → ask-path facts → policy EV table →
prescribed policy (or "current is right", if it is — do not manufacture a change) →
Monday's falsifiable predictions → assumptions/caveats flagged. Brief, numbers first.
