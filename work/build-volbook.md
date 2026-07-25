# BUILD: volbook (strategy #2 — metal daily-wing seller) — 2026-07-25

Ryan approved strategy #2 = the B9 volbook correction. This file is the implementor's charter.

---

## DECISIONS (enumerated + derived-or-UNDERIVED) — implementor, 2026-07-25

Every design decision the code embodies, derived from the verdict's numbers or the
regenerated corpus calibration (`data/volbook_calib.json`, from `scripts/volbook_calib.py`,
which reproduces the verdict EXACTLY: metal gap +10.1pp, EV +8.61¢, realized touch 3.57%,
gold 3.0% / silver 2.0% / copper 6.9%). Tag key: **DERIVED** (from a verdict/corpus number),
**JUDGMENT** (a defensible regularization/tolerance choice, not a verdict number, flagged for
review), **UNDERIVED** (needs Fable/Ryan — a money-sizing decision or an unverified widening).

1. **Mechanism / side — DERIVED.** Sell rich OTM wings by BUYING NO on rungs whose implied
   YES ∈ [0.05,0.35). Market prices ~13.7% touch, realizes 3.57% → NO is systematically cheap.

2. **Family gate — DERIVED.** METAL only enabled. Gas `enabled:false` (EV +0.08¢ ≈ 0, era-
   fragile). Oil `enabled:false` — NOTE this is slightly stronger than the verdict's "keep oil
   minimal, do NOT zero"; oil data ships in the artifact and is one flag-flip to enable, but
   the *deployable* book is metal-only. Revisit when oil is sized as a below-metal sleeve.

3. **Series + weights — DERIVED (weight magnitude UNDERIVED).** KXGOLDD×1.0, KXSILVERD×1.0,
   KXCOPPERD×0.5 (verdict: copper positive but weaker, higher touch, single ~9wk series). The
   0.5 is carried in the artifact + participation log and ranks copper below gold/silver, but
   the actual *dollar* halving requires per-series flat sizing the risk layer does not expose
   (Signal has no size scale, and the charter forbids touching risk semantics). In paper the
   flat size is uniform across series. **UNDERIVED: live copper half-sizing → Ryan/risk ext.**

4. **Entry time-of-day — DERIVED center, JUDGMENT width.** T-3h before close (verdict entry).
   Implemented as ttc ∈ [9000,12600]s = T-3h ± 30min. Width is JUDGMENT: the corpus accepted
   prints up to age≤4h, so the signal is demonstrably time-insensitive; ±30min gives an hourly-
   scale poller room while staying at the measured entry point.

5. **Wing band (edge domain) — DERIVED.** implied YES ∈ [wing_lo,wing_hi) = [0.05,0.35).
   Membership price = mid = (yes_ask + (100−no_ask))/2 (falls back to the one priced side).

6. **Limit = willingness-to-pay (NOT the book) — DERIVED (enchiridion 15).** Ceiling(¢) =
   fair_NO − fee − margin, where fair_NO = (1 − realized_touch)·100¢ and fee = 7·p·(1−p) at the
   fair NO price. A NO ask above the ceiling has no edge → not traded (self-gating). An IOC at
   the ceiling still price-improves to the resting ask. LIVE limit = ceiling; PAPER limit = the
   observed NO ask (so paper P&L books the price actually payable, mirroring streak).

7. **Per-bucket realized touch — DERIVED + JUDGMENT shrinkage.** Touch RISES monotonically with
   implied (metal: 0% in [.05,.15) → 23.8% in [.30,.35)), so the ceiling MUST be per-bucket, not
   family-aggregate — else a 0.32-implied rung would be paid near 96¢ though it touches ~24%.
   Buckets are 0.05-wide over the band. Observed touch is shrunk toward the bucket's implied
   mean with pseudocount **K=20 (JUDGMENT)** so a lucky 0% on a thin bucket cannot justify a
   99¢ NO. K=20 gives thin buckets (n≈21) ~50% prior weight, fat buckets (n≈142) <15%.

8. **Margin / EV floor — JUDGMENT.** margin = 2¢ guaranteed per-contract EV on the calibrated
   distribution. Conservative slice of the +8.6¢ headline, leaving headroom for touch sampling
   error while still clearing typical +8¢ fills (asks ~86¢ sit well under the ~94¢ ceiling).

9. **Ranking — DERIVED (verdict rule 5).** Candidates across ALL metal series are pooled per
   pass and entered highest calibration-gap first, so when the shared per-day cluster cap binds
   the richest wings win. (Amplitude-ranking is backwards for the sell side — verdict.)

10. **Weekday gate — DERIVED.** Mon-Wed only (weekday 0,1,2 in America/New_York from the
    market's close time). Thu excluded (residual +5.5¢ but touch jumps to 10.1%); Fri excluded.

11. **Sizing structure — DERIVED; magnitude UNDERIVED.** SizingHint::Flat (thin daily ladders,
    like weather). Cluster = `volbook-{family}-{ET close date}` → all gold+silver+copper wings
    on one ET day are ONE correlated bet (verdict: rungs within-day correlated). **UNDERIVED:**
    flat_usd / daily_budget / cluster_cap_frac — money sizing is Ryan's; currently the sleeve
    would inherit streak's RiskConfig ($4 flat / $60 day / 15% cluster) which is fine for a
    paper shadow but must be set deliberately before live.

12. **Skip conditions — DERIVED.** NotTradingDay, NotEntryWindow (both silenced — resting
    state), Unpriced (no NO ask), OutOfBand, NoCalib, NoEdge (ask > ceiling). Informative
    skips logged once per (ticker,reason) with reason to `data/volbook.jsonl`.

13. **Retry policy — DERIVED-minimal.** 1 IOC + 1 retry (1s) per rung episode; one episode per
    rung per process/day. Metal wing NO books carry deep resting size (books corpus: hundreds of
    contracts on the nb ladder) so a cross at/under the ceiling normally fills at once; we never
    chase above the ceiling (a NoEdge miss = no edge, not a fill to pursue). Streak's tight
    3×2s flicker loop is unneeded — the window is hours, not 60s.

14. **Settlement — DERIVED (no new code).** Positions are tagged `strategy:"volbook"`, Side::No,
    and settle through the shared reconcile loop against Kalshi `result` at the daily close —
    strategy-agnostic, already correct.

15. **Paper/live gating — DERIVED (triple-gated).** (a) `volbook`/`volbook-once` standalone are
    banned in live by nestor_bin (like weather/lock); (b) NOT scheduled in `run`; (c) the sleeve
    refuses to place a REAL order unless `VOLBOOK_LIVE=1` — in live-without-flag it shadow-logs
    the would-be order. Paper mode shadows the full strategy. "Explicit config enable to trade
    real money" = wiring into `run` + setting the flag + sizing (11), all Ryan's.

16. **Calibration artifact — DERIVED.** `data/volbook_calib.json` (loaded at startup; override
    `VOLBOOK_CALIB_PATH`) + regenerator `scripts/volbook_calib.py`. Ships buckets, per-series
    weights, family EV/gap provenance, band, weekday gate, entry window, margin, shrink-K.

**Untested:** the live-market Entry→fill path is exercised only by paper unit tests + a Saturday
paper smoke run (correctly all-skipped by the Mon-Wed gate). No live/paper order has filled on a
real metal wing yet — first Mon-Wed T-3h window will be the first end-to-end fill.

---

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
