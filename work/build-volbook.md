# BUILD: volbook (strategy #2 — metal daily-wing seller) — 2026-07-25

Ryan approved strategy #2 = the B9 volbook correction. This file is the implementor's charter.

## Intent (evidence: work/verify-b9-widened.md — read it fully; it is a CLAIM, not an authority)
Sell (via taker NO buys) systematically-rich wings on METAL daily ladders (gold/silver/copper)
Mon-Wed, ranked by CALIBRATION GAP (market-implied vs realized distribution from the harvested
corpus), gas at most a small monitored sleeve, oil minimal. The mechanism: absent MMs never
enforce distributional sanity on thin daily ladders early in the week.

## DOCTRINE (enchiridion 23 Part II — binding)
You are implementing an INTENT; the verdict doc is evidence of intent, not truth. BEFORE writing
code, enumerate EVERY design decision the code will embody — entry time-of-day, richness
threshold, limit prices (the limit is a WILLINGNESS-TO-PAY derived from the edge boundary, never
a transcription of the observed book — enchiridion 15), per-rung + per-day sizing, family gates,
skip conditions, retry policy (engine now supports execute_attempt with -r{n} coids; streak's
44¢-gate + 3×2s retry pattern is the reference), settlement handling. For each: derive it from
the verdict's numbers or mark it **UNDERIVED** in this file's Decisions section and continue —
Fable resolves flagged items at review. Where the verdict and first principles diverge, STOP and
write the divergence here.

## Constraints (hard)
- New crate `crates/volbook` implementing the `Strategy` trait (see crates/streak as the pattern;
  crates/engine is the shared layer — touch it ONLY for the nestor_bin registration line and, if
  truly needed, additive helpers; never modify risk/execution semantics).
- Every order routes through Engine::execute/execute_attempt. Taker IOC only.
- **Gated OUT of live**: paper-mode only until Ryan sizes it (follow how nestor_bin gates
  weather/lock out of live; volbook must require an explicit config enable to trade real money).
- Calibration tables: derive from the harvested corpus (settle_harvest outputs + cwing books in
  ~/kalshi_data — ls first; verify-b9-widened.md documents the corpus). Ship them as a data
  artifact nestor loads, plus the script that regenerates them.
- `cargo test` green (existing 116 + your new tests), `cargo clippy` clean. NO git operations —
  Fable reviews and commits. Record API quirk: only *_dollars/*_fp fields are live.
- Write your Decisions section (enumerated, derived-or-UNDERIVED) at the top of this file when done.
- Token ceiling ~450k: if the full build won't fit, deliver signal + calibration + paper scan
  first (a paper-shadowable strategy), execution polish second.

## Deliverable
A branch-ready diff: paper-shadowable volbook strategy + Decisions section here + 5-line final
summary (what works, what's UNDERIVED, what's untested).
