# BUILD: Pre-T0 discovery + early maker rest (2026-07-27)

> **You are implementing an INTENT; this charter is evidence, not truth. Enumerate every
> design decision; derive or flag UNDERIVED; where charter and first principles diverge,
> STOP and surface.** (note 23 §II)

## Evidence (all measured)
- Nestor discovers each 15m window via the `status=open` LIST — a cached 15s index grid
  lagging T0 by median 21-32s → we arrive AFTER the fitted dip (T0+4.8s median) in ~98.5%
  of windows (work/lane-VENUE-MECHANICS-jul27.md, n=536 obs + n=356 kbt).
- The market is fetchable DIRECTLY by constructed ticker pre-T0 (probe 6/6; ticker encodes
  ET close time), via the uncached `Kalshi::market()` (engine/kalshi.rs:388).
- **Demo-proven 2026-07-27: a POST at T0−34.9s returns 201 and rests across the boundary.**
  (503 at T0−399s — acceptance starts somewhere between; T0−35s is available.) File:
  /tmp/pret0_probe.py output; treat prod as unverified until the first live pre-T0 accept
  and handle a 503/rejection gracefully (fall back to current at-T0 flow).
- MMs quote thousands of contracts from T0+2s (kbt: two-sided 356/356, median BTC top
  depth 4,415) — the book exists immediately; only our discovery was late.

## The changes (execution timing ONLY — no parameter changes: rung stays 40, ceiling 46,
## backstop T0+45; those are under separate verification)
1. **Constructed-ticker discovery**: streak derives the NEXT window's ticker from the ET
   close time (deterministic, probe-proven format) and polls it directly via the uncached
   market GET, instead of (in addition to, as fallback) the status=open list.
2. **Pre-T0 maker rest**: when derive-fourth is decisive before the boundary (sampler is
   decisive by ~T0−10s; 6/6 verified record), post the resting 40¢ bid at ~T0−10s on the
   next window's market. Handle rejection (503/400) as benign → retry at T0 (current
   flow). Cancel-on-flip if official result contradicts the derivation (machinery exists).
   expiration_ts unchanged (T0+60).
3. **Fix the ttc conflation** (signal.rs:140-144): ttc>900 currently returns NotEntryWindow
   (terminal) — "too early" and "too late" must be distinct; too-early is retryable/waitable.
4. **WS interest pre-registration** (strategy.rs:479): register the next ticker with the ws
   maintainer at derivation time, not after REST discovery.
5. Instrumentation: rest_placed_rel_t0 on the maker record; a discovery_rel_t0 field on
   the pass heartbeat — so tomorrow's tape measures the recovery directly.

## Constraints
- Branch `pret0-discovery` off main, commit there, NEVER merge — Fable reviews.
- Live bot untouched; no restarts; data/state.json untouched.
- Demo verification: the pre-T0 POST path end-to-end (construct ticker → GET → decisive
  mock → POST at T0−10s → 201 → cancel). Cancel everything you place.
- Tests + clippy. Rate-limit courtesy: the direct poll replaces list polls in-window, must
  not ADD net request load (the 429s at 05:58Z are live evidence of budget pressure —
  count your requests in the report).

## Report
Enumerated decisions → demo evidence → request-budget delta → tests/clippy → branch+commit
→ open risks. Brief.
