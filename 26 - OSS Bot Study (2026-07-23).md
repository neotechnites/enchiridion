# 26 - OSS Bot Study (2026-07-23) — three public Rust Kalshi bots, read cover to cover

> Ryan-ordered: find 3 public Rust Kalshi bots, read them fully, review our code through their lens. Three Opus readers, one repo each, full read both sides. Repos: **poly-kalshi-arb** (446★, real arb bot), **poly-kalshi-sports-bot** (135★, real cross-venue sports bot, single author, production scars), **DRADIS** (29.6k lines, production-grade — but Kalshi "not yet implemented"; used as architecture reference). All three legitimate, none hollow.

## The headline: nestor is AHEAD of all three on execution rigor
- **All three ship order code against the DEAD legacy endpoint** (`POST /portfolio/orders` — the one we empirically 410-proved today). We are on V2 `events/orders` with demo-verified real fills. The 446★ bot's Kalshi order leg has likely never worked against current prod.
- **None of the three has idempotency** (no deterministic client_order_id anywhere; DRADIS US explicitly sends None). Ours dedupes retries by design.
- **The 135★ bot has NO risk layer at all** — no bankroll, no drawdown, no halt concept; the 446★ one caps per-market contract counts only, no correlation model. Our cluster/portfolio/daily/drawdown stack is beyond all three.
- Also better: atomic state persistence (446★ bot corrupts on crash mid-write), fill truth (201 + fills cross-check + sub-cent real fees; they trust responses coarsely or assume), settlement/reconcile (135★ bot never settles — logs attempts and stops), day-attributed daily-loss, panic-supervised loops, signing hygiene (446★ bot logs signature fragments; also signs query strings and survives only by luck — our bare-path rule independently confirmed by two repos).

## Adopted into the pre-live fix branch (sent as addendum to the worker)
- Wire our existing-but-unplugged `net.rs` backoff primitives into the scan loop; **retry GETs only, never the order POST** (446★ bot's exact discipline).
- Consecutive-error breaker: 5 straight order/API failures → sticky halt (their circuit-breaker idea, minus the unsafe auto-cooldown).
- Per-call 3-5s timeouts inside entry windows (DRADIS's 2026-05-01 overnight-freeze scar: a TCP stall inside select! blocks everything; 30s client cap = half our window).
- Live pre-order balance check (doomed-order insurance).
- Orphan-recovery hint: our fills parser makes the OrderError→recover path ~15 lines.

## Deferred, with reference implementations pinned
- **Kalshi WebSocket for entry windows** — adopt only if week-1 data shows 1s REST staleness costing entries. Full recipe captured from two repos: `wss://api.elections.kalshi.com/trade-api/ws/v2`, channel `orderbook_delta` (snapshot then deltas), auth = sign `{ts}GET/trade-api/ws/v2` with our existing RSA headers on the upgrade; **book is bids-only — the ask = 100 − best opposite bid**; sequence numbers must advance by exactly 1, any gap → resubscribe for fresh snapshot; staleness-reject frames >200ms old. Reference implementation: `DRADIS/src/venues/us/ws.rs` (signed reconnect, gap→resync, watch-channel fan-out, cancellation teardown). Local clones: `~/.claude/jobs/14170a4a/tmp/oss/`.
- **Dutch-book two-leg protocol** (when that sleeve goes live): sequence the UNCERTAIN leg first, fire leg 2 only on confirmed leg-1 fill (135★ bot's pattern) — and close the gap they left: on leg-2 failure, actively unwind leg 1, never alert-and-eat. Re-assert the arb from the live book at fire time. Idempotency on both legs.
- **OS-thread watchdog** (DRADIS): heartbeat outside the tokio runtime, process::exit on 5-min silence with an atomic phase-breadcrumb — cheap insurance once on the VPS.
- **HTTP pool rebuild** if long-lived-connection hangs ever appear (135★ bot rebuilds its client every 30 min after fighting keep-alive death).
- Do NOT port DRADIS's order-lifecycle reconciler — it solves resting-order problems our IOC-taker doctrine designed out (stated so nobody "improves" us into it).

## Validation summary
The study's biggest output is confidence: three real codebases, hundreds of hours of other people's work, and on every money-correctness dimension we came out ahead — while harvesting their tuition (rate-limit discipline, timeout scars, WS recipes, two-leg sequencing) at zero cost.
