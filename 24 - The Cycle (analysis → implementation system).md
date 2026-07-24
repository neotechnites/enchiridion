# 24 - The Cycle (analysis → implementation system)

> The Senate's operating process, aligned with Ryan 2026-07-23 (log R91-R93). Everything here is an adopted HYPOTHESIS — profit judges it, structure is revisable. Canonical answer to "how are decisions made": **by data collection, analysis, and implementation.**

## The shape (researched, log R92)
- **One top mind** (Ryan's single interface — he never bounces between windows). It orchestrates everything below.
- **Analysis = parallel fan-out** (proven pattern: orchestrator + 3-5 specialist workers beats single-agent ~90% on research at ~15x tokens — spend it only where parallelism is real). Each worker spawned with: objective, output format, sources, boundaries.
- **Implementation = single-threaded** per change, tightly briefed (multi-agent coordination demonstrably breaks on coupled code).
- **No Claude middle-managers at current scale** — every layer is a lossy, costly context hop; a middle layer buys span-of-control we don't yet need. Revisit when concurrent workers outgrow one orchestrator's context.
- "Implementation" means **"what change to the system maximizes profit"** — a strategy add/kill, risk change, ideation-method change, even language choice. Anything under the sun is changeable; code is one case.

## The standing pipeline (continuous, cheap)
- **Ideators run constantly on the Mac** (lid-open = uptime), in two modes:
  1. **Exhaustive creative search** of the full hypothesis space — premise: *"out there is some signal telling us how to bet the bitcoin 15 minute that could give us 10% every single time; we just dont know what it is."* Free brainstorming with discipline: graveyard dedup, mechanism named, cheapest kill run, numbers recorded. Not mere change-monitoring.
  2. **Depth miners** on whatever is currently working — which market, which hour, which day, what internet-visible condition correlates (= gate-hunting industrialized).
- **Token schedule:** night = Ryan uses nothing → deep sweeps with all free capacity (even Haiku at zero marginal cost is net-positive); day = light and conservative, never crowd his work. Tune runs/night by results.
- All workers bootstrap from this vault (note 23 ritual included) and write ledgers back into it. Note 22 measures whether the machine improves.

## The cycle (Ryan-triggered, ~weekly)
"Run the cycle" → analysis fan-out sweeps EVERYTHING (ideator ledgers, nestor's live participation record, athena's captures, the internet: what's working for others, what changed on venues) → top mind synthesizes into ONE ranked list: *what change would most increase profit right now, with the predicted effect stated* → Ryan approves → single-threaded execution per change → results measured against the prediction that justified them → outcome written back, right or wrong. Triggers besides the calendar: kill-scan events, major market events.

## Capital allocation (replaces binary kills — log R93)
Confirming a thin edge's death takes hundreds of trades (2-4 weeks) — binary kill-calls are late or guessy. Instead: **every engine's bankroll share slides continuously with recent evidence of its edge.** Decay = automatic taper, not a cliff. Tapered-to-zero engines are paper-tracked FREE via athena's captures (never paid probes — fees buy nothing there); re-entry is automatic when the paper record recovers. Outright deletion remains reserved for STRUCTURAL kills (taxonomy, note 15). The weekly kill-scan remains the measurement; allocation is the response. (Engine spec enters nestor when strategy #2 goes live — sliding shares need ≥2 engines to mean anything.)

## Current build order (2026-07-23)
1. Tonight: ONE night-ideator rep (1-2 Opus-medium workers, capped, ledgers by morning) — tests the whole pipeline before "constant" scales.
2. This week: data-correctness audit of every capture stream (everything downstream inherits its errors).
3. ~~Weekend: streak live~~ **AHEAD OF SCHEDULE (Jul 23 evening): the redirect is fully built, audited, and merged (nestor main `3f105b1`) — paper mode is RUNNING and catching signals (first paper fill: NO@39¢ post-up-streak, fee ceil'd correctly). Remaining before live: Ryan's prod key → $1 selftest → $100 week. Demo env supported via KALSHI_API_BASE (plumbing-grade only).**
4. Next week: first full Ryan-triggered cycle, run on a week of accumulated material.
5. Sliding allocation specced into nestor at strategy #2.
