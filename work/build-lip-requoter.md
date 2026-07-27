# BUILD: LIP gas requoter (2026-07-27 ~19:45Z — Ryan-ordered, runs TONIGHT)

> **You are implementing an INTENT; this charter is evidence, not truth. Enumerate every
> design decision; derive or flag UNDERIVED; where charter and first principles diverge,
> STOP and surface.** (note 23 §II) — AND answer the OPERATIONS DESCENT (note 23 §III)
> five questions in the file header: cash / breaker / schedule / collisions / alerts.

## Mission
One standalone python file (`lip_requoter_v3.py`, stdlib + cryptography only) that keeps
both sides of the qualifying gas rungs quoted from launch until 03:45Z, so resting-order
LIP score accrues continuously. It replaces a hand-written v1 that lost ~$9 tonight; the
post-mortem of v1 is REQUIRED READING: R169 in note 17 + work/verify-ops-map.md (F7).

## The traps that already fired tonight (each one is a hard requirement)
1. **NEVER trust the resting-orders or positions indexes** — both are eventually-consistent
   and v1's trust of them stacked 13 orders on one rung. ALL self-knowledge comes from a
   LOCAL ledger built from synchronous truths: placement responses (fill_count,
   remaining_count, order_id) and cancel responses (reduced_by).
2. **Cancel path is `DELETE /portfolio/events/orders/{id}`** — the `/portfolio/orders/{id}`
   path returns 410 and v1 logged those as successful cancels, leaving phantom stacks.
   TREAT ANY non-200/404 cancel as "order may be live" and stop posting that rung.
3. **Stack-proof by construction**: every cycle = cancel ALL orders this process placed
   (from the local ledger) → learn fills from reduced_by → post at most ONE fresh pair per
   rung around the CURRENT book. Never post without having cancelled first.
4. **Pair-centering**: both legs priced off the same book read (join best bid / best ask,
   never improve, skip if spread <1¢ or book one-sided) so a mid-drift between legs cannot
   manufacture >100¢ boxes (v1's box losses).
5. **Order body**: V2 `POST /portfolio/events/orders`, side bid/ask, count "10.00", price
   4-dp dollars string, good_till_canceled + expiration_ts 03:50Z, taker_at_cross STP,
   coids dot-free (`.`→`_`) and unique per post.
6. **Books**: `orderbook_fp` key, `*_dollars` levels, best = max price level.

## Parameters (derived tonight)
- RUNGS: KXAAAGASD-26JUL28-**4.105** and **4.085** ONLY (4.100 is the incident rung —
  excluded; 4.110 holds a full-cap position; others pinned/dead).
- Inventory cap: 10 contracts per rung per side, counted in the LOCAL ledger, from fills
  learned. At cap on a side → stop posting that side (never top up, never flatten).
- Cycle 180s. Error budget: 3 consecutive cancel anomalies or 6 post errors → cancel
  everything owned, push ntfy (topic senate-nestor-2732e947), exit.
- Cleanup on ANY exit path (including signals): cancel all owned orders.
- Log every event to ~/nestor/data/lip_probe.jsonl ({"event": "v3_..."}).
- Cash effects are covered by the existing external-cash allowance — do NOT touch
  data/external_cash.jsonl; worst-case new collateral ≈ $20.

## Verification before you call it done
- Pure-python unit test of the local-ledger arithmetic (simulated placement/cancel
  responses incl. partial fills, 404-as-filled, 410-as-anomaly) — runnable with `python3
  -m unittest` from the file itself or a sibling test file.
- A `--dry` mode that does everything against the LIVE books except POST/DELETE (prints
  intended actions) — run it on the Mac against prod public endpoints and show output.
- Auth machinery: copy from ~/kalshi_data/scripts/probe_maker_fill_truth.py (proven).
  You cannot reach the VPS; deliver the file at ~/kalshi_data/scripts/lip_requoter_v3.py
  on the Mac — Fable reviews, ships, launches, and watches the first cycle.

## Report
Ops-descent answers → enumerated decisions → unit test output → --dry output against live
books → the file path. Speed matters (the window is bleeding score) but every trap above
is non-negotiable.
