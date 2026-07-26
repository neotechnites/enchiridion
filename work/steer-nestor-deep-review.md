# STEER: Nestor deep review (2026-07-26) — kill the next lesson before live teaches it

> Ryan's order: "a deep review of nestor with all of your current knowledge, so we dont keep
> wasting days 'learning lessons'." Every expensive lesson so far was one of FOUR CLASSES.
> Each lane owns one class and hunts its NEXT instance. You are adversarial: your job is to
> BREAK the code, not to admire it. A lane that returns "all clean" without evidence of a
> genuine hunt has failed.

## The four lesson classes (the priors — study before hunting)
- **A. Underived defaults** (note 23 §II): bid-the-observed-ask, the 15s poll on a 60s
  window, taker-fee-on-maker-fills. A constant nobody derived is a bug that hasn't fired.
- **B. Spec-vs-reality**: the settled filter's 36s lag, phantom 44s from 0.5-3s-stale REST
  quotes, lazy expiration (~2-3min), eventually-consistent resting list, order-status GET
  404ing on filled IOCs, fills `fee_cost`/`is_taker` fields, markets opening AT T0. Reviews
  verify code against our MODEL of Kalshi; only probes verify the model. Find assumptions
  still resting on docs or inference.
- **C. Execution-truth / money-path**: accepted≠filled, positions-schema drift making the
  divergence breaker blind, i64 truncating sub-cent fees, coid dedupe across restarts.
  The ledger, kill-switch, and bankroll must never believe fiction.
- **D. Unasked data**: paper's decision stream showed the settled-filter anomaly for hours
  and nobody asked it the question. Anything not logged cannot be reconstructed; anything
  logged but never reconciled is a wasted sensor.

## Common ground rules (all lanes)
- Repo: ~/Documents/senate/nestor (READ ONLY — no edits, no builds, no branch checkouts;
  the live bot runs from this tree). Live data: nestor/data/*.jsonl, logs/run.log.
  Enchiridion context: notes 39, 23, 15; work/verify-streak-execution.md,
  verify-house-truth.md, verify-volbook-execution.md, build-house-probe.md.
- NEVER touch prod keys, live state, or place orders. Demo probes: PROPOSE them (exact
  probe design), do not run them — Fable batches probe execution.
- Every finding: file:line, the failure scenario (concrete inputs → wrong money outcome),
  severity (CRITICAL = wrong money/blind kill-switch; HIGH = missed EV or false verdict;
  MED = diagnostic gap), and the cheapest decisive check that would confirm or kill it.
  Findings without a failure scenario are noise. Rank most-severe first.
- Report to your assigned work/review-nestor-{lane}.md, commit to enchiridion (no push).

## Lane charters
1. **CONSTANTS** (class A): enumerate EVERY constant, default, cadence, timeout, retry
   count, threshold, and clamp across all crates + nestor_bin (grep-driven, exhaustive —
   report the full inventory count). For each: derived (cite the evidence) / judgment
   (reasonable, note it) / UNDERIVED (flag with the failure it invites). Special attention:
   volbook (1+1@1s retry, 60s cadence vs entry window math, cluster cap arithmetic), house
   (3s loop vs 75s TTL vs 60s requote staleness interplay), streak exec (BACKSTOP_AT 45 vs
   MIN_TTC guard vs 4×2s ladder arithmetic — does the last retry actually land in-window?),
   engine (timeouts, backoff, breaker thresholds, MAX_LIVE_BANKROLL vs current $106.03).
2. **REALITY** (class B): inventory every exchange-behavior assumption in the code and
   classify: PROVEN (cite the demo/prod evidence in the vault) vs INFERRED (docs/reasoning
   only). For each INFERRED: the failure if wrong + the cheapest probe design. Hunt
   especially: settlement/result timing for METAL dailies (volbook settles on what, when —
   streak's T+10s lesson was crypto-specific), partial-fill semantics on GTC resting orders,
   ws seq/reconnect edge cases (seq gap during an entry window), rate-limit budget of three
   strategies + ws + 1Hz sampling in one process (sum the worst-case req/s vs known limits),
   fee schedule on non-crypto series, subaccount/balance semantics volbook+house share.
3. **MONEYPATH** (class C): chaos-review the full order lifecycle state machines. Streak's
   new maker leg: crash between place_maker and reservation persist; restart during a live
   maker leg (startup sweep cancels it — but does the reservation/ledger reconcile?); the
   may_be_resting ambiguous path; backstop size-at-46 vs reservation made at 40 (does the
   cap release the difference?); cancel_failed retry loop across window boundaries; two
   coins signaling simultaneously under one exec_lock with resting legs. House: fill during
   pull_quotes race; ledger mark-to-mid with a one-sided book; the −$20 stop with fee=0
   fills. Volbook: retry after partial; cluster cap with three series racing; settlement
   adoption of a position the process never saw fill. Engine: reconcile's divergence breaker
   arithmetic against maker-reserved-but-unfilled capital.
4. **SENSORS** (class D): for each strategy, (a) can every plausible anomaly be RECONSTRUCTED
   from what is logged today (list the reconstruction each record type supports; find the
   gaps — e.g. does streak log the book at maker-place time? at cancel time? does house log
   the mid path between quote and fill?), and (b) does a written EXPECTATION exist to
   reconcile the decision stream against (volbook has Monday predictions; streak's new
   policy has NONE — write them: expected maker-fill rate ~24%, backstop rate ~21% of
   unfilled, no-trade rate ~55%, from the ledger; house has the probe protocol — check its
   metrics are actually computable from the current jsonl fields). Deliver: the missing
   instrumentation list + a conformance-check table (metric, expected value, source, where
   to read it) ready to run against tomorrow's tape.

## Report format (per lane)
Findings ranked most-severe first (file:line, scenario, severity, cheapest check) → the
inventory/table your lane owns → what you verified clean (with how) → proposed probes.
Brief, numbers first, no essays.
