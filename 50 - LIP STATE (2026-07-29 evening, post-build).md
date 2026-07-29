# 50 - LIP STATE — 2026-07-29 evening, post-build

> Written at context exhaustion. Read [[42]] entry path, then this. **Supersedes the design half
> of [[47]] and ALL of [[48]]** — note 48's allocator numbers are artifacts, see §4.

## 1. STATE RIGHT NOW
- **v5 STOPPED**, disarmed. Ryan stopped it 2026-07-29 ~06:10 MT. Nothing is trading but nestor.
- **Branch `lip-v5-build` pushed**, 4 new commits, **626 tests green, NOT deployed, NOT reviewed-clean.**
- **A review found 2 BLOCKERS. Do not deploy.** See §3.
- Stashed, deliberately not shipped: `ACQUIRE_FLOOR_C = 6` price floor (`git stash list`).

## 2. WHAT IS COMMITTED (89aadcd, 744ceec, 2d6df9f, 83be682)
1. **B15** ceiling binds at *placement* — was a plan-time budget only; `place_allowed` had no
   total-collateral test. Mechanism behind $6,077 resting notional under a $45 ceiling.
2. **B16** per-market cap `MARKET_CAP_FRAC = 0.10` — **BROKEN, see D2.**
3. **FREE_RIDE_ONLY armed** — 1¢ land-grab path dead. **BROKEN, see D1/D3/D4.**
4. **NEW-1c** allocator cluster seed from the same book the rails read. **VERIFIED CORRECT.**
5. **No-change requote suppression** — `TRIG_S_MOVED`/`TRIG_RESYNC` dropped at the touch with no
   refill due. Attacks the 1.9-second median order life / 73.9%-same-price churn / 10.6% presence.
6. **`floor_clearing_size`** staged-inert: `q = Q·(SCORE_SIDES·T/pool)/(1−…)`, `SCORE_SIDES=2.0`.
   NOT wired — as a hard cap it took `test_orders_appear_within_three_cycles` to **zero orders**,
   because it also feeds the forfeit gate's `q_min`, so it changed which slots are ADMITTED.

## 3. THE BLOCKERS — fix before anything runs
- **D1. Free-ride deletes the exit on fill.** Gate's only exemption is `own_qty > 0` from live
  *orders*; `book_fill` pops the order at `remaining<=0`, so a filled order gives `own_qty = 0` —
  the exemption vanishes exactly when we first hold inventory. No slot ⇒ `update_shed_targets`
  can never START a shed (`s is None`) and `requote_pass` can't price one. Re-opens the
  "de-polled held market never requoted, never shed, fills arrive as surprises" failure.
  **Fix: `build_slots` needs a `held` parameter and a held-inventory exemption.**
- **D2. B16's cap is below the plan's own per-slot cap and forbids the second leg.** At $300:
  `slot_cap_usd = $30` = B16 cap $30, and B16 sums BOTH legs of a ticker with no side test, so a
  full bid leg refuses the ask entirely. At $45: plan $10 vs rail $4.50 ⇒ permanent re-offer loop.
  **Fix: reconcile against `slot_cap_usd`; decide per-leg vs per-market-gross.**
- **D3.** The gate's `− own_qty` is unreachable dead code (guarded by `own_qty<=0`), and
  `cum_size` is the *qualifying walk* (breaks at target), not side depth — understates rival
  depth 2×. The "per SF-5" comment is unsupported. Gate is really `if not sd["qualifies"]`.
- **D4.** `land_grab = 0` is load-bearing, its "unreachable" comment is FALSE (`own_qty>0` and
  `not qualifies` reaches the `elif`), and **deleting it passes all 626 tests.**
- **D5.** B15/B16 count `gone_404` phantom collateral. One-line filter in `place_context`.
- **D7.** The inertness test can't detect a real call site. **D8.** Two requote tests were
  *weakened* not inverted (`our_price_c=39` moves them off the touch).
- **Zero integration coverage:** across 206 tests, **0 `ceiling` and 0 `market_cap` refusals ever
  fired.** Every B15/B16 test hand-builds a `PlaceContext`; none exercises `engine.place_context()`.

## 4. THE STRATEGY, as it actually stands
**Do not trust any allocator built on `compmap`.** Three independent attempts produced 10×–190×
the receipt by mining the left tail of ONE snapshot (competing scores of 2–4). Note 48's tables
are that artifact. The receipt is the only anchor: **$71.34, 24 line items, 6 events, $2.22 median.**

Established:
- Reward is blind to price/side/outcome. `credit = pool × share ÷ 2 × presence`, **$1.00 floor
  per market-period, forfeited entirely below.** 43 of 67 rungs forfeited (167 dollar-hours).
- **We were present 10.6% of the time.** Presence is the largest lever and it is free.
- **We cannot pick rungs** — every ex-ante signal tested was anti-predictive.
- **72% of markets never filled.** Fill rate is a MARKET property, not a size property (bigger
  half of rungs had HIGHER fill rate in 1 of 10 events, P=1.1%). λ ≤ 1/W is the selection rule.
- **Matched pairs earned +$39.63 (+6.88¢/pair). The loss is unmatched legs, −$587.42 (90%).**
  Breadth does not touch this; a per-market acquisition cap does.
- **No exit legs.** +2¢ resting sell measured −$40.30: caps winners at 2¢, rides losers to zero.
- **P&L SIGN IS NOT ESTABLISHED**: four methods on our own files give −$650, −$340, −$7.98, +$139.
  `settlements.json` `revenue` is 0 on 40 of 67 rows including 6 winners. **And `revenue` is in
  CENTS.** Fix the accounting before ranking any fix.
- Board capacity: **1,246 events / 295 series with $100+ daily programs**; top suppliers are five
  temperature-hourly series (~1,070 programs each) — a family we have never touched.
- Variance: portfolio constraint is `Σwᵢ²(1−pᵢ)/pᵢ ≤ 0.25` (50% CV), ≈ avg price ≥12¢ at N=30.

## 5. NOT IMPLEMENTED
B17 rungs-per-settle-source (needs plan-side binding like NEW-1c — my attempt corrupted
`alloc.py`, reverted); live-LIP-program filter (**$225 of $477 loss / 47%, zero credit cost —
highest value remaining**); portfolio variance constraint; λ/volume filter (needs a new field in
the classify sweep); remaining-window vs full lifetime.

## 6. WHICH CONCEPT FILE CHANGES
[[49]] holds: every model I built ran hot for the same reason and the receipt is the only thing
that caught it. Add: **"green" means self-consistent, not correct** — 626 tests passed with two
blockers and a deletable guard. That is [[45]]'s thesis arriving inside our own test suite.
