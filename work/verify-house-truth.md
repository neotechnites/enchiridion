# VERIFY: house-probe measurement truth (DEMO) — 2026-07-26

Charter: work/steer-house-truth.md. DEMO ONLY (demo-api.kalshi.co, key …fb, PEM
nestor/secrets/Demo.txt). Every order carried `expiration_ts`; all orders cancelled; resting
list empty at exit; prod keys and the nestor repo untouched (nestor read-only, for the order
body shape).

Scripts: `/Users/ryanwhitehead/kalshi_data/scripts/probe_maker_fill_truth.py` (taker + maker
modes), `/Users/ryanwhitehead/kalshi_data/scripts/probe_selfcross_maker.py` (self-cross
attempt). Raw payload log: `/Users/ryanwhitehead/kalshi_data/house_truth_probe.jsonl`.

---
## Q1 — VERBATIM MAKER FILL in /portfolio/fills

**R99 IS DEAD: `/portfolio/fills` is NOT empty on demo.** It returns both taker and maker fills,
with a fee field and an explicit maker/taker marker. Verbatim entry (our resting bid crossed by
the demo MM, size 1):

```json
{
 "action": "buy",
 "book_side": "bid",
 "count_fp": "1.00",
 "created_time": "2026-07-26T18:16:32.629277Z",
 "fee_cost": "0.000000",
 "fill_id": "e9de20cf-5fd1-48d0-6824-4d79982362d3",
 "is_taker": false,
 "market_ticker": "KXBTC15M-26JUL261430-30",
 "no_price_dollars": "0.6100",
 "order_id": "60f7a9c6-916c-478e-bc10-4aadba4d71bb",
 "outcome_side": "yes",
 "side": "yes",
 "subaccount_number": 0,
 "ticker": "KXBTC15M-26JUL261430-30",
 "trade_id": "e9de20cf-5fd1-48d0-6824-4d79982362d3",
 "ts": 1785089792,
 "yes_price_dollars": "0.3900"
}
```

Field truth:
- **fee field = `fee_cost`**, a DOLLAR string with 6 decimals (`"0.014500"` = 1.45¢). NOT
  `fee`, NOT `average_fee_paid` (that name only exists on the create-order 201 response).
- **maker/taker marker = `is_taker` (bool)**. maker ⇒ `false`.
- prices are `yes_price_dollars` / `no_price_dollars` (4-dp dollar strings); size is `count_fp`
  ("1.00"); ids `fill_id`/`trade_id`/`order_id`; ticker duplicated as `ticker` and
  `market_ticker`; time as ISO `created_time` (µs) and unix-second `ts`.
- **No `_fp` envelope**, no cents integers anywhere in a fill entry.
- Taker entries are identical in shape with `is_taker: true` and a non-zero `fee_cost`.
- Create-order 201 (flat, NO `order`/`fill` wrapper — a parser that reads `body["order"]` gets
  nothing): `{"average_fee_paid":"0.0145","average_fill_price":"0.7100","client_order_id":…,
  "fill_count":"1.00","order_id":…,"remaining_count":"0.00","ts_ms":1785089355658}`.

## Q2 — FILLS-INDEX LATENCY at 1s polling

Measured as (time our 1s poll first saw the entry) − (the fill's own server `created_time`).

| # | kind | price | lag: fill → seen in index | rest → fill (quote lifetime) |
|---|-------|-------|---------------------------|------------------------------|
| 1 | maker | 0.98  | **0.38 s** | 7.0 s |
| 2 | maker | 0.39  | **0.22 s** | 4.7 s |
| 3 | maker | 0.39  | **0.93 s** | 8.7 s |
| 4 | maker | 0.37  | **0.33 s** | 45.9 s |
| 5 | maker | 0.41  | **0.13 s** | 29.6 s |
| 6 | taker | 0.929 | **0.08 s** | — (immediate on 201) |
| 7 | taker | 0.929 | **0.07 s** | — |
| 8 | taker | 0.929 | **0.07 s** | — |

**The fills index is real-time, NOT an eventually-consistent index: n=8, min 0.07 s, median
0.28 s, max 0.93 s, zero misses.** Every fill was found on the very first poll after it
happened, so these numbers are dominated by our own 1 s poll grid — the true index lag is
≤ ~0.9 s and plausibly ~0.

**Contrast — the ORDER indexes DO lag (R114 family, confirmed again):**
- `GET /portfolio/orders/{order_id}` → **404 `not_found` (service "query-exchange")** when
  queried ~0.3 s after a 201 that returned that exact id, and still 404 through a fill.
- `GET /portfolio/events/orders/{order_id}` → **404 page not found** (endpoint does not exist).
- So order-status polling is USELESS for fill detection on demo; `/portfolio/fills` is strictly
  faster and is the only usable detector. `/portfolio/orders?status=resting` remains fine for
  orphan sweeps only (it was correctly empty after every cleanup).

## Q3 — ACTUAL MAKER FEE

**Maker fee = ZERO.** `fee_cost = "0.000000"` on 6/6 maker fills today (7/7 incl. 2026-07-25),
including four at
**0.37-0.41** where the taker formula would have charged 1.6-1.7¢ — a fully discriminating
price range, not a rounding artifact.

| fill | is_taker | price P | fee_cost | 7·P·(1−P)/100 |
|------|----------|---------|----------|---------------|
| maker | false | 0.3900 | **0.000000** | 0.016653 |
| maker | false | 0.3900 | **0.000000** | 0.016653 |
| maker | false | 0.3700 | **0.000000** | 0.016317 |
| maker | false | 0.4100 | **0.000000** | 0.016933 |
| maker | false | 0.9800 | **0.000000** | 0.001372 |
| maker | false | 0.4000 | **0.000000** | 0.016800 |
| maker (2026-07-25) | false | 0.0200 | **0.000000** | 0.001372 |
| taker | true | 0.7100 | 0.014500 | 0.014413 |
| taker | true | 0.9290 | 0.004700 | 0.004617 |
| taker | true | 0.9290 | 0.004700 | 0.004617 |
| taker | true | 0.2700 | 0.013800 | 0.013797 |
| taker | true | 0.0700 | 0.004600 | 0.004557 |

**Taker fee = `ceil(0.07·P·(1−P)·C, $0.0001)`** — exact on 5/5 (note: rounding up to
$0.0001 / one-hundredth of a cent, NOT up to a whole cent as the old fee tables state).
**Maker fee = 0, not "25% of taker".** Full maker exemption on the demo exchange.

Balance/position cross-check: after the taker sequence, `market_positions[].fees_paid_dollars`
= 0.059200 = exactly the sum of the taker `fee_cost`s; maker fills added 0.000000. Fee truth is
consistent across fills, positions and balance.

## Bonus mechanics established (all demo)
- **`self_trade_prevention_type` is REQUIRED** (400 `missing_parameters` if omitted) and
  `taker_at_cross` genuinely PREVENTS self-match: resting bid @0.50 + our own IOC ask @0.50 →
  aggressor came back `fill_count "0.00", remaining_count "0.00"` (cancelled, no trade, no fee).
  Self-crossing to manufacture fills is impossible; good safety property for two-sided quoting.
- **Resting works exactly as the build ledger recorded**: `time_in_force="good_till_canceled"` +
  future `expiration_ts` + `taker_at_cross` → 201, `fill_count "0.00"`, `remaining_count "1.00"`.
- **Aggressive-maker fills are easy and adverse**: resting 1¢ inside the best ask on the live
  KXBTC15M window filled in **4.7 / 7.0 / 8.7 / 29.6 / 45.9 s** (5/5 attempts filled inside the
  120 s TTL) — every fill was the market coming to us as it moved through our price (bought
  0.39-0.41 as the book fell). Demo has a real counterparty
  that picks off inside quotes; the ±1¢ house quote will fill, and it will fill adversely.
- **`/portfolio/orders?status=resting` entries carry their own fee fields** —
  `maker_fees_dollars: "0.000000"`, `maker_fill_cost_dollars: "0.000000"`, `fill_count_fp`,
  `initial_count_fp`, `no_price_dollars`/`yes_price_dollars`. Confirms the exchange models a
  maker-fee line item and books it at zero on demo.
- Cancel-by-id returns `{"order_id":…,"reduced_by":"1.00","ts_ms":…}`; cancelling an
  already-filled order returns 404 `not_found` (expected, harmless).

---
## WHAT THE HOUSE CRATE SHOULD CHANGE

1. **Book fees from `fee_cost` in the fills entry, never from a formula.** The crate's P&L /
   −$20 stop must read the exchange's number. If a fill has `is_taker: false`, the fee is 0 —
   a formula-based ledger would over-charge the maker sleeve ~1.7¢/contract at mid prices, i.e.
   it would fabricate the entire cost of the strategy and kill a probe that is actually free.
2. **Parse the fill entry with these exact names**: `fee_cost` (dollar string, 6dp),
   `is_taker` (bool), `yes_price_dollars`/`no_price_dollars` (4dp dollar strings), `count_fp`,
   `order_id`, `ticker`, `created_time`/`ts`. No `_fp` envelope, no cents ints. Verify
   `parse_fills` in engine/kalshi.rs handles `fee_cost` and surfaces `is_taker` — the maker
   sleeve's promote/kill metric is *maker-fill count*, so `is_taker` must be recorded per fill.
3. **Create-order 201 is FLAT** — read `order_id`/`fill_count`/`average_fee_paid` at the top
   level, not under `body["order"]`.
4. **3 s fill-detection polling is FINE.** Index lag is ≤1 s, so the detection budget is
   essentially the poll period itself: worst case ≈3 s from fill to knowing, mean ≈1.5 s. That
   is the dominant term, so if two-sided pickoff exposure matters, tighten the poll (1 s costs
   nothing and cuts worst-case exposure by 2/3) rather than changing the index.
5. **Do NOT use order-status/resting-list for fill detection** — 404s / lags for seconds.
   `/portfolio/fills` filtered by ticker is the detector; the resting list is orphan-sweep only.
6. **Fee-aware edge maths**: at zero maker fee, a ±1¢ quote earning the 2¢ spread keeps the
   whole 2¢; the round-trip only pays fees if we flatten as a TAKER (~1.6¢ at mid). The crate
   should book the flatten leg's taker fee explicitly — that, not the maker leg, is the cost.

## WHAT STAYS UNKNOWN UNTIL PROD
- **Whether prod also charges zero maker fee.** Demo says 0; Kalshi's public schedule has
  historically applied maker fees on some series. The crate must READ `fee_cost`, so it will be
  correct either way, but the promote/kill *economics* (2¢ gross vs fee) change if prod charges
  ~0.4-1.7¢ per maker fill. First live maker fill settles it — log it loudly.
- **Prod fills-index latency.** Demo is sub-second on a low-traffic exchange; prod at load may
  differ. Same measurement (fill `created_time` vs first-poll-seen) should be logged on the
  first 10 live fills before trusting a 3 s loop.
- **Fill rate / adverse selection on the actual vehicles** (KXAPRPOTUS, KXCPIYOY). The 5-10 s
  fills here were on a 15-minute BTC book against an active demo MM; slow political books will
  fill far more rarely and from a different counterparty mix.
- **Expiration-sweep laziness (≈2-3 min past `expiration_ts`, prior run)** was not re-measured
  today; the ~4 min worst-case orphan window stands.
