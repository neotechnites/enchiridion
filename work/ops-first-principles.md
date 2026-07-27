# OPERATIONS FROM FIRST PRINCIPLES (2026-07-27 evening, Ryan-ordered pre-funding sweep)

> Cause: three same-class incidents in 36h (F8 self-halt on own winnings; dot-coid; LIP
> fills halting nestor). Common root: ACTIONS TOUCHING THE LIVE ACCOUNT WITHOUT DERIVING
> THEIR INTERACTIONS. This doc enumerates every money flow, process, and scheduled event,
> derives its handling, and becomes the checklist every NEW operation passes before it runs.

## I. MONEY FLOWS — every dollar path in/out of the account, and which ledger sees it
| # | flow | ledger visibility | breaker interaction | status |
|---|---|---|---|---|
| 1 | nestor strategy fills/settlements (streak/volbook/house) | risk ledger (native) | native + F8 pending widening | ✅ |
| 2 | LIP probe fills + settlements (tonight) | external_cash.jsonl (−11.07 + pending 130) | reconciled Δ$0.00 live | ✅ interim; reconcile to actuals Tue AM |
| 3 | LIP reward credit (Tue ~16Z) | covered by external pending | positive-side widened | ✅ interim |
| 4 | FOMC strangle (Wed, manual) | MUST get an external_cash entry AT PLACEMENT | else halts within 60s | ⚠ in Wed runbook |
| 5 | Ryan's $1-2k deposit (Wed) | a deposit = unexplained +cash → HALT | procedure below | ⚠ REQUIRED READING |
| 6 | Subaccount transfers | unknown: does main balance read include subs? | PROBE at creation | ⚠ Wed gate |

**DEPOSIT PROCEDURE (flow 5, the pre-funding requirement):** deposits go in EITHER (a) with a
simultaneous external_cash entry equal to the deposit, or (b) directly routed to the maker
subaccount IF the main balance read excludes subs (probe first). Never bare. After the R153
subaccount exists, ALL side-operation flows (2,3,4) move inside it and their external_cash
entries are deleted — the wall replaces the ledger.

## II. PROCESSES — everything running, and their pairwise interactions
- **nestor (A1, systemd)**: 3 strategies + ws + reconcile. Auto-restart. State: one writer.
- **LIP tonight (A1)**: requoter (120s, caps 40 orders/±10 per rung, exits 03:50Z),
  fill-checker (cron 10min — NOT window-gated: benign after tonight, REMOVE Tue), book
  sampler (90s, exits 03:59Z). Disjoint tickers/coids from nestor; STP on; rate budget
  +~0.15 req/s (sum with nestor's worst case stays inside measured limits, but 429s at
  05:58Z today mean the budget has no slack — no NEW pollers without retiring one).
- **Captures (A1)**: flycan (2/day + Fri), hearing (Wed self-stopping), athena daemons
  (supervisor 5min), health_watch (10min → ntfy topic; **RYAN UNSUBSCRIBED — alerts void
  until the app is installed; this is the single dark link in the alarm chain**).
- **Mac/micro**: verified nothing senate-relevant runs on either.

## III. NEXT-72H EVENT CALENDAR — each event pre-derived
| when (Z) | event | handled by |
|---|---|---|
| Mon 21:00 | metals settle (5 volbook positions) | reconcile + F8 widening ✅ |
| Mon 21:00-22:00 | volbook conformance grading vs predictions | Fable, on settlement |
| overnight | streak signals — FIRST pre-T0 maker episodes | new machinery; watch derive_verify partial-call rate |
| Tue 03:50-03:59 | LIP orders expire; requoter+sampler exit | by construction ✅ |
| Tue ~04-05 | gas positions settle | external pending ✅ |
| Tue 12:00 | new gas event lists (26JUL29) | nothing auto-quotes ✅; $300 window + arm C = Tue night, scripted with Fable |
| Tue ~16:30 | LIP reward pays | external pending ✅; readout = M1-M8; reconcile ledger to actuals |
| Wed 12:25 | hearing capture starts | cron, self-stopping ✅ |
| Wed (Ryan) | subaccount + $1k + restricted key + probes (flows 5,6; key-capability check) | runbook below |
| Wed 18:40 | FOMC strangle gate read (≤20¢ take / ≥30¢ walk) | Ryan+Fable live; external_cash entry if placed |
| Wed 18:45-20:00 | strangle bracket rides | settlement via same entry |
| Thu+ | maker binary build (R153) on subaccount | after Wed gates |

## IV. THE CLASS FIX — the operations descent (note 23 Part III)
Before ANY new operation touches the live account (probe, manual trade, deposit, new
poller, schedule change), enumerate IN WRITING:
1. **Cash**: what dollars move, when, and which ledger sees each movement?
2. **Breaker**: what does the divergence check read at each step? (Both directions.)
3. **Schedule**: what fires later because of this (settlements, credits, expiries) and is
   each pre-covered?
4. **Collisions**: coids, self-trade, rate budget, state writers, dedupe keys.
5. **Alerts**: if this goes wrong at 3am, what pages whom?
An operation that can't answer all five doesn't run. (Today's LIP probe failed #2 and #3;
the morning's F8 was #3; the wings would have failed nothing — different class.)

## V. FUNDING-READINESS GATES (what must be TRUE before the $1-2k)
G1 reward unit confirmed (Tue AM readout). G2 $300-scale window clean (Tue night). G3
subaccount created + main-balance semantics probed (flow 6). G4 restricted-key capability
probe (expiration_ts GTC + cancel on V2 — demo-style probe on the new key BEFORE funding).
G5 Ryan on ntfy (the alert chain has a human). G6 maker binary passes its own review with
the §IV descent attached. Money moves when all six are green, not before.
