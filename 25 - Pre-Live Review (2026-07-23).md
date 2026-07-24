# 25 - Pre-Live Review (2026-07-23) — the R97 mandate, executed

> Four adversarial Opus reviewers, each briefed to BREAK an angle, before any real money. Composite verdict: **FIX-FIRST — then safe.** The money-correctness core held everywhere (pricing, IOC, signing, caps, sizing isolation); every real hole found is in the *recovery/accounting* class — losses the bot wouldn't see, not losses it would cause. All fixes specced to one worker (branch `pre-live-fixes`).

## The headline answers Ryan asked for
- **Can it blow the $300? NO.** The $200 perps collateral is **structurally unreachable**: Kalshi segregates perps margin from predictions cash ("a liquidation event cannot touch your predictions funds" — and the reverse holds), event orders draw only predictions cash, and nestor links **zero** perps, transfer, or withdrawal endpoints — a maximally-adversarial bug controlling nestor's code can reach at most the ~$100 predictions cash. Rate limits don't matter; the balance IS the ceiling.
- **Honest worst-case loss with the caps working:** ~$20-23 in a day (15% realized halt + open overshoot), ~$30-38 total before the 30% drawdown halt freezes everything (persisted, restart-honored, human `resume` required).
- **Strongest containment (DO THIS when creating the prod key):** Kalshi now supports **subaccount-restricted API keys** (July 2026) and **`write::trade`-only scope** (no transfer access, June 2026). Create the key trade-only + restricted to a subaccount funded with exactly the $100 → the ceiling becomes **exchange-enforced**, independent of our code entirely.

## What held under attack (all four reviewers, attacks that FAILED)
IOC guarantee (no code path can rest an order — sole order POST hardcodes IOC+taker_at_cross) · NO-side price translation incl. edge prices and gate rounding (44¢ gate checked before rounding) · signing on every call (query-string class bug swept, clean) · caps enforcement in `run` (no order reaches Kalshi without `evaluate`; exec lock kills the concurrent-sizing race; sizing floor-bounded to +$0.005) · **real-balance isolation confirmed** (sizing never reads the $300 account balance) · fee single-charge · double-settle · DST/ticker-hour handling · midnight-ET budget rollover · torn-state-file (atomic rename) · second-process lock (and no stale-lock wedge) · hung HTTP (30s timeouts) · panic containment + mutex-poison recovery · gzip/live-file race.

## Must-fix list (consolidated from 4 reports — in the worker's ticket)
1. **Close the orphan/lost-ack hole (2 reviewers found independently — the big one).** A timeout AFTER Kalshi accepts = real position, booked as "OrderError, nothing happened," never retried, never reconciled → kill-switch computes on a lie. Fix: on OrderError, reconcile via fills/positions by deterministic coid; make the settlement sweep compare local state against **exchange** positions; periodic bankroll-vs-real-balance divergence check that HALTS.
2. **Idempotency:** the one-order-per-market guard is in-memory only — restart in-window re-fires; behavior on duplicate client_order_id is an unverified assumption with a dangerous branch (echo → double-booked P&L). Fix: same-ticker guard in `on_fill_actual` + empirically verify duplicate-coid behavior on demo.
3. **State persistence failures must halt** (currently an eprintln — disk-full could swallow a kill-switch flip), and a MISSING state file must not silently re-arm a halted bot with a fresh bankroll.
4. **Pin the bankroll seed** — config default is $1000 (!): a lost nestor.toml would set halts at 10× and make the account drainable. Fail closed if unset/oversized.
5. **429/5xx exponential backoff** (currently a 1s hammer that would prolong bans through entry windows).
6. **Gate standalone `streak` out of live** (without the reconcile task there is NO kill-switch at all — undocumented load-bearing invariant).
7. Minor: sub-cent fee truth (done same day), fill-price-fallback marked estimated, dead comments removed, compress task panic-wrapped, `selftest-order` documented as risk-bypassing (operator-only).

## Also noted
- Clock skew after Mac sleep → all signed calls 401 while public data flows (bot looks alive, can't trade — fail-safe but silent; alert-on-consecutive-401s added to ticket #5's scope).
- secrets/ were world-readable → `chmod 600` applied same evening.
- Paper state at review time: bankroll $95.97 after one honest $4 paper loss — fee accounting and settlement working live.

**FIXES LANDED (same night, merged to main after audit — 85 tests, clippy clean):** every must-fix above is implemented: lost-ack recovery (`RecoveredFill` path — fills-probe before any OrderError verdict), exchange-truth reconciliation each sweep (orphans auto-adopted conservatively + alert), $2 bankroll-vs-balance divergence breaker, duplicate-position guard, persist-failure halts, live refuses missing state without `--fresh-state`, live bankroll mandatory and capped at $100, GET-only backoff (2→60s) + 5-consecutive-error sticky halt + 401 clock-skew alert, pre-order balance check, 5s in-window call timeouts, all standalone strategy subcommands gated out of live. **The last unknown closed empirically: duplicate client_order_id → HTTP 409 `order_already_exists` on demo — Kalshi rejects, never echoes; the double-book branch does not exist.**

**Gate status: CODE-READY.** Remaining before live money: Ryan reads this note → creates prod key (subaccount-restricted + write::trade-only, fund with the $100) → $1 selftest → $100 week.
