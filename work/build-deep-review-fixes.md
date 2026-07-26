# BUILD: Deep-review fix batch (2026-07-26 evening) — before Monday's windows

> **You are implementing an INTENT; this charter is evidence of intent, not truth. Enumerate
> the design decisions your code embodies; derive each from the goal or flag it UNDERIVED;
> where charter and first principles diverge, STOP and surface.** (note 23 §II)

Source findings: work/review-nestor-{constants,reality,moneypath,sensors}.md (read the
relevant finding IN FULL before each fix — they carry file:line and failure scenarios).
NEW EVIDENCE (Fable, demo, 2026-07-26 ~19:20Z): **a resting unfilled bid does NOT debit
/portfolio/balance** (balance identical before/during/after a 1-lot GTC rest; cancel
reduced_by "1.00"). Prod unverified — fixes must be robust to BOTH branches.

## ORDERED FIXES (stop at the line if time-constrained; 1-6 must land tonight)

1. **Divergence breaker vs resting legs** (reality F1, constants F1, moneypath F3 — the
   confirmed halt). Robust-both-ways fix: reconcile's divergence check widens its tolerance
   by the sum of currently-RESTING reservations (threshold' = $2.00 + Σ resting_reserved).
   Do NOT simply exclude resting reservations from expected_cash — if prod (unlike demo)
   debits on rest, that inverts the halt. Track resting vs committed reservations in
   RiskManager (a `resting: bool` on the reservation, flipped on fill/cancel). Log which
   branch reality takes on the first live leg (real_cash moved during rest or not) —
   that record converts INFERRED→PROVEN for free.
2. **House joins the risk regime — minimum-safe version** (moneypath F2, constants F1):
   (a) every house pass checks the GLOBAL risk halt and stands down (cancel all house
   orders) when halted; (b) house fills feed a cash-delta the reconciler includes in
   expected_cash (a simple signed house_cash_cents ledger on RiskManager is enough — do
   NOT force house through one-position-per-ticker, it's two-sided by design); (c) house's
   orphan sweep filters to house coids only (it currently cancels streak's maker leg —
   moneypath F5). Keep the probe's own −$20 stop as-is.
3. **Partial maker fill: cancel the remainder** (moneypath F1). In settle_maker_fill, when
   f.count < leg.order.count: cancel the order BEFORE drop_leg; treat cancel-404 there as
   benign (fully gone); log remainder_canceled/reduced_by. Never top up (that stays).
4. **Resting-path 409 = may_be_resting** (reality F2). In place_resting, classify 409
   order_already_exists as may_be_resting: true (mirror the taker path's benign-409).
5. **Backstop sequence hardening** (constants F3, moneypath F4): wrap the backstop cancel
   in in_window; guard attempt 1 of the IOC ladder with the same ttc check as attempts 2-4;
   make the settled_for fetch non-fatal for the pass (must not `?`-return above
   supervise_makers — supervision runs even when the results fetch fails).
6. **404 after expiry ≠ filled** (reality F3, sensors P2c). In the cancel-404 branch and
   mark_gone: if now > expiration_ts + small slack, the bid may have EXPIRED, not filled —
   release the episode as expired_unfilled (record it, no CRITICAL alert, no permanent
   backstop-withhold). Before expiry, 404⇒filled stands (demo-proven).
--- must-land line ---
7. **Fee truth to proven precision** (reality F7): taker_fee ceils at $0.0001 not whole-cent
   (fix the stale doc comment); engine taker path debits actual_fee_cents when the exchange
   provides it, formula as fallback; house fee fold per-row (one missing fee_cost row must
   not convert the whole batch to formula).
8. **House metrics tell the truth** (sensors F2/F3/F6/F7, instrumentation I2/I3/I5/I6):
   quote_secs emitted as per-pass delta (report sums correctly); metric 2 averages ALL
   markouts and nets fees (carry fee_cents + fill id on house_markout); gap-through stop
   evaluates at the FIRST pass with age ≥ 60s (today it skips <60 then requires ≤60 —
   fires only on exactly-60-62); mid_used/mid_age_secs/mid_source on markouts; house_pass
   record every pass {book, ticker, best_bid, best_ask, spread, gate, quoting}; log the
   no_two_sided_book and spread_lt_2c gates (once per (ticker,reason)).
9. **Streak episode observability** (sensors F1/F5/F8, I1/I4/I7): per-pass heartbeat when
   current_market() is None {series, into_window, reason}; write base_record at maker
   placement (outcome:pending) and re-emit terminal; capture book+observed ask at backstop
   fire; rename record field book→book_at_signal.
10. **exec_lock not held across unbounded I/O** (constants F2): the fills GET inside the
    lock gets in_window or moves out; re-check maker runway after acquiring the lock.
11. **Volbook re-arm on miss** (constants F4): a rung episode that ends Missed re-arms for
    later passes in the same window (dedupe stays for filled/rejected). Also: volbook_skip
    records gain numeric fields {implied_pct, ttc, no_ask, ceiling} (sensors F13/I13).
12. **MAX_LIVE_BANKROLL 100→150** (constants F5) — covers $106.03 recovery; derive the
    number in a comment (current bankroll + headroom, still a seed sanity cap).

## Constraints
- nestor repo, branch `deep-review-fixes` off main. Commit there; NEVER merge — Fable
  reviews and merges. Live bot keeps running; do not touch data/state.json or restart.
- Tests: every fix carries a test where the harness allows (the gap-through age fix, fee
  ceil, 409 classification, partial-cancel flow, divergence-tolerance arithmetic are all
  unit-testable). Full suite + clippy clean.
- Demo verification where cheap (the 409-coid behavior, partial cancel reduced_by shape)
  — demo key in SECRETS.local.md / secrets/Demo.txt; cancel everything you place.
- Do NOT chase the standing Δ$0.25 (noted for weekly review); do NOT touch ws internals
  (VPS-era work); do NOT redesign house into full reservations.

## Report
Enumerated decisions (DERIVED/JUDGMENT/UNDERIVED) → per-fix: what changed, file:line,
test → demo evidence if any → suite/clippy status → branch+commit → what you did NOT do.
