# Charter: lip_v5 FINISH ROUND (2026-07-28, chartered by Ryan: "fix what needs fixing")

Base: worktree `/Users/ryanwhitehead/Documents/senate/nestor-wt-lipv5`, branch `lip-v5-build`,
from commit 4579fb7. v4 is FROZEN — zero changes outside `tools/lip_v5/`. v5 stays
STAGED-INERT: nothing in this round deploys, and no code path may start quoting without the
staged README sequence plus an explicit operator-created artifact (staged-calls doctrine).

## The finding this round exists to fix
`Maker.place()` — "the ONLY way an order reaches the exchange" — has ZERO call sites. The
requoting stage was never written; the assembled system computes allocations and drops them.
Four findings hide behind it. Two restart blockers + two should-fixes stand from the prior
adversarial round. Full evidence: session R-log (grep "affirmative-path") + the review of
2026-07-28 afternoon.

## Work items (all must land; each with tests that the defect would have failed)

### A. The quoting stage (the big piece)
Per spec §4 / v1 §4.1-4.7, a requoter driven from `engine.cycle()`:
- Diff target allocation (post-forfeit-gate) against resting orders each cycle; emit
  place/cancel ONLY through `Maker.place`/`Maker.cancel` (the rails stay the one path).
- Make-before-break with the insufficient-balance degrade to cancel-first (T* = 46 s) and
  `mbb_degraded` ledger row.
- v1 §4.3 triggers: refill at 50% remaining, S-move 25%, safety resync 60 s, min resting
  life 30 s (trigger (a) overrides), `expiration_ts = close − CLOSE_MARGIN_S`.
- Land-grab orders from the qualification pass.
- THE SHED PATH: maker-shed orders for (1) cutover-triage verdicts and (2) inventory whose
  venue fails (★) ongoing — priced per v1 §5.4, never crossing (G6 stays off), `fully_closing`
  so the halt/day-stop exemptions apply. Completed sheds feed `l_shed` measurements.

### B. The plumbing pass (wire what exists)
- `books`/`yes_mids` from classifier.table (+ WS books when connected) into
  `runner.iteration` → `cycle()` — wakes the day stop (B2).
- Populate `self.venues` (ratchet.VenueState) from presence/accrual/verification data —
  wakes §1.4 venue caps in allocation.
- REAL market close time: classify must carry the market's settlement close_ts (from the
  market object), NOT the program window end. `Slot.close_ts` = market close;
  `program_end_ts` = program end. This wakes the horizon exclusion and fixes carry for
  markets settling after their reward window (the PYPL geometry).
- Fix the B3 equity measure: drawdown must measure LOSS, not deployment. Resting collateral
  and inventory marked value are assets; derive equity accordingly and add the test
  "full deployment at zero loss ⇒ drawdown 0".
- Wire `fees_paid` (cash.pay_fee) on any fee-bearing event.
- Wire P6: public trade tape check over P6_LOOKBACK_DAYS for candidate markets (the
  "does anyone ever trade here" mirror), through the classify_sweep lane.

### C. Restart correctness (prior round's blockers)
- BLOCKER-1: rebuild `self.orders` from replay (place_resp/cancel_resp rows) AND the v1 §9.4
  step-4 coid-prefix sweep against GET /portfolio/orders at recovery; exchange orders with our
  prefix unknown to replay → B10 UNKNOWN machinery. Cash feed must count rebuilt resting
  collateral (the invariant: published never above truth).
- BLOCKER-2: adoption idempotent — write an `adopt` ledger record; skip adoption when replay
  shows it. `position_cost` must not double.
- SF-3: every halt path flattens once; `iteration_error` drops to a slow halted-idle cadence
  (no spin, no crash-loop exit).
- SF-4: shutdown crash-proof — `o.get("coid")`, the three steps (cancel-all, handback, zeroed
  feed) each survive the others' failure; handback survives everything.

### D. Entry path + the aliveness doctrine
- `main()` gains the staged live branch: requires `--live` AND an operator-created gate
  artifact (e.g. `v5_go.json` written by hand per README step 4) — bare `--live` still
  refuses. Service file gets the real ExecStart (deploy remains Ryan-gated).
- AFFIRMATIVE TESTS (briefs/implementor.md aliveness rule): FakeExchange + one good venue →
  orders appear within N cycles; a failing adopted position → a shed order appears; the
  one-path-to-the-wire test asserts `len(placed) > 0` (kill the vacuous 0==0).
- Fix `series_denied` overbroad bare-`startswith` clause.

## Definition of done
Suite green INCLUDING the new affirmative tests; every new constant derived or UNDERIVED-
flagged; every guard's mirror named; report per briefs/implementor.md (commits, decisions,
UNDERIVED list, deviations). Then ONE adversarial round on the assembled system, briefed
affirmative-path-first, before any gate talk.
