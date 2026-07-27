# Charter: settled-set guard in reconcile (incident #5 code fix)

Date: 2026-07-27 ~21:45Z. Origin: R171 / note 41 §0. Pipeline: this charter → implementor → adversarial review → tests → Fable merge (note 23 Part III: no hand-deployed money code).

## The defect (observed live today, 8× re-book)
The re-book loop has TWO legs, both unguarded:
1. `RiskManager::settle()` (crates/engine/src/risk.rs) books P&L for any ticker it finds in `state.open` — it has no memory of having settled that ticker before. It pushes a `Settled{ticker, won, pnl}` record but never consults that list.
2. `reconcile_exchange_truth()` (crates/engine/src/reconcile.rs) `adopt_orphan`s any exchange position on a NESTOR_SERIES ticker absent from `state.open` — including one we ALREADY settled whose exchange-side payout hasn't processed yet (metals today: nestor booked wins ~21:00Z; exchange still showed position_fp≠0 at 21:41Z). Loop: settle removes from open → next pass adopts it back as an orphan (also inflating `day_spent` by its stake each time) → next pass settles it again (+$2.17/pass). 8 passes today produced bankroll $122.64 vs real ~$100, `settled` 55 entries for 20 real, day_spent 175.66.

## The fix (intent, not prescription — enumerate and derive your own decisions, note 23 Part II)
A settled ticker must never be re-processed, even when state is wrong, even across restarts:
- **Adoption guard (primary — kills the loop's driving leg):** `adopt_orphan` must refuse a ticker present in the settled set. Rationale: exchange settlement/payout indexes lag booking by minutes-to-hours (F8 family, prod-observed today), so "exchange shows a position we don't have open" is NOT sufficient evidence of an orphan when we settled that ticker.
- **Settle guard (backstop):** `settle()` must refuse (return None, log loudly — this firing means state regressed) a ticker already in the settled set.
- **Settled set = derived from persisted `state.settled` tickers** (it survives restarts; a separate new field must justify itself against "state.settled already IS the record"). Note the MAX_SETTLED=1000 drain bound: derive whether 1000 recent settlements is a sufficient guard window (a re-book needs the ticker still on the exchange — hours — vs 1000 settlements ≈ weeks; state your derivation in the code comment).
- Consider: does the settle guard strand a genuinely re-opened position? Derive why not (a Kalshi market settles exactly once; a ticker can never legitimately be open again after settlement).

## Constraints
- Branch `fix/settled-guard` off main (e14e028) in ~/Documents/senate/nestor. Do NOT touch main, do NOT deploy, do NOT touch the VPS or any live state. Merge is Fable's, after review.
- Regression tests must reproduce today's loop shape: (a) settle → adopt_orphan same ticker → refused; (b) settle → settle again → None, bankroll/day_spent unchanged; (c) normal orphan adoption + normal settle still work; (d) guard survives persist→reload.
- Match surrounding code style/comment density. `cargo test` and `cargo clippy` clean. Report: files changed, design decisions enumerated with derivations, test count, anything you could NOT derive (flag, don't ship).
- Where this charter and first principles diverge, STOP and surface — do not faithfully implement a mistake.
