# 53 - LIVE STATE (updated in place — the CURRENT block below is the truth)

> Entry: [[SENATE STATEMENTS]] → [[56 - THE MACHINE (fresh-Claude implementation guide)]] →
> this note's CURRENT block.

## CURRENT — 2026-07-31 ~4:20 PM MT: VIRGIL LIVE
- **VIRGIL is the LIP maker now** — new standalone package `tools/virgil/` (nestor worktree
  nestor-wt-lipv5), NOT a lip_v5 version. Five boxes: alpha (rate = share×pool/2×presence,
  rank by marginal rate) / risk (conversion cap 20%/day HARD — Ryan; swing √(Σw²(1−p)/p) ≤
  20% capital on FILLED dollars by settle source; rung fill budget $50/day) / chooser
  (greedy $10 slices) / executor (vg-* coids only, never sells, 30s min resting life, REST
  polling no websockets) / attribution (estimates poll 300s — Ryan: estimates≈paid, close
  enough; measured rate recorded but NOT yet fed back into allocation = first upgrade).
- **Live since 22:00Z on $1,000**, service `virgil` on the VPS, systemd-enabled. First
  cycle: 24 orders, 0 errors, 0 instant fills, ~$340 deployed (conversion cap binds),
  predicted $448/window CEILING (thin evening books; the number to distrust). Book: rain
  city dailies + gas 4.105/4.110 + primaries/oddballs, 7 settle sources, both sides.
  Rider mode v1: only joins sides rivals already qualified (1000c); S=0/empty books
  refused (D2); tick floor (<5c) refused (Ryan's order at go). Treasuries join when
  their windows open Aug 1.
- **Jul 27 forensics (the $70 day, from the tape)**: NOT 1-2s churn — 5,546 places/14.4h,
  median rest 2s but continuous presence via instant replacement; paying presence was
  70-90% at 6c+; only 9 instant fills/261 contracts; −$196.99 IS sourced
  (external_cash.jsonl, fills+settlements). Statements 43/45 need correction, 40 refines
  to $36.28 phantom across 4 hourly-index programs — Ryan not yet ratified.
- **Open**: (1) φ=0-measured families (McMorrow $120) charge zero conversion/swing — thin
  evidence hole, propose flooring φ at phi_quiet without strong n; (2) estimate→paid
  ratio lands with tomorrow's statement = attribution's first real datapoint; (3) pool
  unit CONFIRMED $100/program/window (÷10000).
- Watch: background monitor on first 3h (service, fills, churn, accrual). Kill switch:
  `sudo systemctl stop virgil` (cancels own orders on SIGINT).

## PRE-VIRGIL — 2026-07-31 end of day (details in [[55]]'s tail)
- **v5 STOPPED** (Ryan's order, ~12:03 PM MT 07-31; 43 positions ride to settlement — it
  never sells). The $1-earned day confirmed the dispersion diagnosis: $10 seats accrue
  under the $1 cliff and forfeit.
- **v6 STOPPED + DISABLED** (cannot return on reboot) after seven same-day deploys, all
  pulled by Ryan — churn at the truncation boundary, then EV-crumb books (ledger carried
  the dead book across deploys; fixed by clean-slate archiving), then an unexplained SME
  placement lane. Committed on v6-build: sticky book (proven live, zero churn), measured-
  rate loop, seed mode (untested on wire), multi-market-per-cluster, rank-truncation dials
  (C_DIALS=2000 vs C_CASH=deposit), v4-rank chooser (corrected full-wall denominator).
- **DEPLOY GATE #0 (permanent)**: no v6 deploy until `lip_v5.dryrun` prints the production
  would-buy book offline and WE verify it against expectation. NOTHING runs until Ryan says.
- Open: seeds on the wire? · what book the corrected chooser builds (~4 undisturbed min
  from cold boot) · the estimator vs the tape ($19.7 projected vs ~$2 payable — THE open
  question; prime suspect S mismeasurement).

## OLDER LOGS
The dated running logs (2026-07-29 → 07-30 build/incident record) live in this file's
git history. Page them in only when a specific question needs them.
