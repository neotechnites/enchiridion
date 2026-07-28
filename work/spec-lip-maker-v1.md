# SPEC: production LIP maker v1 (`lipmaker`) — reward-per-collateral-dollar optimizer

Date 2026-07-28. Sources: `work/charter-lip-maker-v1.md` (ground truth, not re-litigated), `work/probe-lip-gas.md`, `work/verify-lip-gas.md` §0/§3/§4, note 23 §II/§III, v3 lessons (charter §7).

> **Implementor stanza (note 23 §II):** you are implementing an INTENT; this spec is a colleague's claim, not truth. Enumerate every constant/cadence/default you write, derive each from the goal, flag UNDERIVED. Where spec and first principles diverge, STOP and surface. Answer the note-23 §III five (cash / breaker / schedule / collisions / alerts) in the binary's header before first launch.

## 0. GOAL AND THE ONE OBJECTIVE FUNCTION
0.1 Maximize Σ_slots [payable LIP reward − drift − fees − adverse settlement EV] per day per dollar of collateral, across ALL LIP events, without anti-gaming revocation.
0.2 Scoring law (CFTC filing 2026-02-11, binding): `Score(bid) = DF^(RefPrice − Price) × Size`, DF from `discount_factor_bps` (5000 = 0.50 on 100% of live programs), distance in ticks from the **same-side best**. Per-side normalize over the qualifying set; `SnapshotScore(u) = norm_yes + norm_no` (≤2.0; total across all users on an included snapshot is exactly 2.0). Snapshot EXCLUDED unless both sides hold ≥ `target_size_fp` resting size. Payout = day-share × `period_reward`, **paid iff ≥$1.00**, rounded down to the cent. A YES ask IS a NO bid — both score.
0.3 **Master inequality.** Slot `(market m, side s)`, our size `q` at same-side best, rival qualifying score `S`, pool rate `ρ = period_reward / window_hours` ($/h), collateral price `p` ($/contract):
```
reward_rate(q)     = (ρ/2) · q/(q+S)                                  $/h
marginal_$/collat$ = d(reward_rate)/dq / p = ρ·S / (2·p·(q+S)²)       $/h per collateral-$
```
Everything below is a corollary. **The `1/p` term is the whole edge**: the same score costs 34× less at p=$0.02 than at p=$0.68. Second cheap-side gift: max loss on a filled bid is exactly `p`/contract, so cheap sides are cheaper on capital AND bounded on downside.

## 1. SLOT TABLE (rebuilt every cycle)
1.1 **Correction to the charter's "no API exposes pools — UI-only."** `GET /trade-api/v2/incentive_programs` (cursor-paged, no auth) carries `period_reward`, `target_size_fp`, `discount_factor_bps`, start/end and market ticker for all 119,615 programs. **Pools are machine-readable.** UI-only are (a) the estimated-earnings popover and (b) the Rewards-table Competition tag. This removes pool discovery from the human critical path; §7 keeps the ritual for reconciliation only. SURFACED per note 23, not silently changed.
1.2 Per market per side, from the live book via the exact filing algorithm (`score_side`, §11.5): `ref` (same-side best), `S` (Σ over qualifying set), `qualifies` (cumulative size reached target before bids ran out), `top_size`, `next_level_gap`, `p = ref`.
1.3 **PINNED** (permanently unscoreable, never quote) iff no legal resting price exists on the missing side: opposing best bid = 99¢, or opposing best ask = 1¢. 10 of 17 gas rungs are pinned.
1.4 **REVIVABLE** = one side `qualifies=false` but a legal price exists. Highest return on the board (§6.2).
1.5 **Q5 hedge:** compute `S` twice — `S_cents` (exponent = cent distance, our reading) and `S_levels` (exponent = book-level index). **Use `S_levels` (always ≥ `S_cents` ⇒ conservative on our share) for every ENTRY decision; `S_cents` for the reconciliation model.** At distance 0 and 1 — the only places we ever quote — the readings are identical, so placement is unaffected by Q5's answer.

## 2. ALLOCATION (cheap-side-first = marginal-rate water-filling)
2.1 Cheapest-first is a *special case* of sorting by `marginal_$/collat$`; implement the general form, because at equal `p` the thinner-`S` slot must win.
2.2 **Hurdle** — marginal capital must beat its own expected fill cost, per slot, from our own tape: `fillcost_rate(m,s) = f [fills/h] · d [$ drift/contract, capped at p]`; `hurdle(m,s) = fillcost_rate / (q·p)`. Seeds until 200 own fills exist per (series, side-band): `d = $0.07` (charter: 5-9¢/cross-cycle pair, midpoint); `f = 8/h` for mid-priced sides (R174: 7-10 ct/h on hot sides), `f = 1/h` for p<0.05 (deep-OTM strikes trade in blocks, not flow). These give 22%/window drag on a mid slot (q=100,p=0.40) and 16% on a cheap one.
2.3 **Global floor `λ_min = 0.10`** reward-$ per collateral-$ per 16h window: below 10%/window a $1,000 book grosses <$100/day, under the §2.2 measured drag plus ops. **UNDERIVED at the third digit** (§15.1) — a round number above the two computed drags. Never let `λ_min` exceed the achieved portfolio rate (that refuses the business).
```
ALLOCATE(slots, budget_usd, caps) -> {slot: qty}
 1  slots := [s for s in slots if not s.pinned and not s.denied and s.legal_price_exists]
 2  alloc := {s: 0}; spent := 0
 3  loop:
 4    for s in slots: rate[s] := ρ_s·S_s / (2·p_s·(alloc[s]+S_s)²)                       # §0.3
 5      if alloc[s]+1 breaches inv_cap(s) or per-market cap:      rate[s] := -inf        # §8.1/8.2
 6      if rate[s] < max(λ_min/16, hurdle(s)):                    rate[s] := -inf        # $/h units
 7    best := argmax rate (tie-break: ticker, then side); if rate[best] == -inf: break
 8    step := max(1, round(0.02·budget / p_best))
 9    if spent + step·p_best > budget: step := floor((budget-spent)/p_best); if step<1: break
10    alloc[best] += step; spent += step·p_best
11  for m in markets:                    # forfeit gate is per-MARKET, so it runs after water-filling
12    if projected_payout(m, alloc) < ENTRY_FLOOR($2.00):
13      if top_up_to_floor(m) affordable and still beats hurdle: alloc[m] := topped
14      else: alloc[m] := 0; spent -= its cost; goto 3      # re-water-fill the freed dollars
15  return alloc
```
2.5 Step = 2% of budget (line 8): coarsest step landing the water level within 2% of optimum (50 steps), keeping the loop O(50·|slots|).
2.6 **Improve-vs-join.** Improving makes us the reference price and halves every incumbent's score (`S → S/2`). Improve iff `(ρ/2)·[q/(q+S/2) − q/(q+S)] > q·0.01·r*`. Consequence with real numbers: 1¢→2¢ is +100% collateral for the measured +9% reward ⇒ **never improve on p<0.05**; 68¢→69¢ is +1.5% for +9% ⇒ improve. Evaluate the inequality; do not hardcode the conclusion.
2.7 **Wall indifference size.** Against wall `W` at best: `q* = sqrt(ρ·(W+S)/(2·p·r*)) − (W+S)`. **If `(W+S) > ρ/(2·p·r*)` then `q* < 0` ⇒ do not quote that slot at all.** Gas mid rung (ρ=$6.25/h, p=$0.40, r*=0.00625/h): skip if `W+S > 1250`. At p=$0.02: skip if `W+S > 25,000` — cheap sides tolerate walls 20× larger. Same `1/p` law from the competition side.

## 3. FORFEIT FLOOR (entry, rescue, abandon)
3.1 **`ENTRY_FLOOR = $2.00`** projected day payout per market. Derivation: the payout cliff is $1.00 and the system's own declared tolerable model error is 2× (charter §8 stand-down). Setting the entry floor equal to the tolerated error bound makes the two self-consistent — a market entered at projection $2.00 still pays iff the model is inside its own stated tolerance. Cost of not doing this, measured: 22% of last night ($6.94 earned → $5.40 payable; rungs at $0.95/$0.33/$0.17/$0.08/$0.01 all burned).
3.2 **PARTIALLY DERIVED:** the distribution of `actual/projected` is unmeasured. After 5 reconciled days set `ENTRY_FLOOR = $1.00 / q05(actual/projected)`.
3.3 **`RESCUE_TARGET = $1.10`** = $1.00 boundary (Q2) + 1¢ round-down-to-cent + 9¢ model buffer. Safe under both readings of "≥$1.00 vs >$1.00".
3.4 **Checkpoints at T+2h, T+8h, T+13h**, final sweep T+15h. With 1 snapshot/s, sampling noise is negligible after ~1h (3,600 samples); the binding uncertainty is competition drift, so checkpoints are spaced to leave ≥2h of runway each. The last lands at 01:00Z with 3h left — ample, since max attainable rate is `ρ/2 = $3.13/h`, so $0.50 of rescue needs ≥10 min of dominant share.
3.5 Per market at each checkpoint, `A` = accrued projected payout, `h` = hours left, `C` = current collateral:
```
proj := A + rate_now·h
if proj >= RESCUE_TARGET:                                                        KEEP
elif ∃Δq: A + rate(q+Δq)·h >= RESCUE_TARGET
     and  A + rate(q+Δq)·h  >  (C+Δq·p)·r*·h + fillcost(Δq)·h:                   TOP_UP(Δq)
else:                                                                            ABANDON
```
3.6 The rescue term counts `A` at **full** value because abandoning yields 0 here — accrued score is not sunk, it is *conditional* on clearing $1.00. This is why rescue beats redeploy far more often than intuition says.
3.7 ABANDON only has value if another live pool can absorb the freed capital **now**. Gas dailies close 03:59Z and the next opens 12:00Z, so late abandonment inside a single-event deployment frees capital into nothing. **Breadth (§7) is therefore a precondition for the abandon rule to be worth anything**; with one live event ABANDON degrades to "cancel to stop fill risk only."

## 4. REQUOTE POLICY, CADENCE, AT-BEST TRACKING
4.1 Cadence is only a tradeoff if you cancel first. **Make-before-break:** POST the new order, confirm `order_id`, THEN cancel the old. Same side, non-crossing ⇒ no self-trade. Cost = a ~0.5-1.5s overlap of double size (double collateral; ~0.2% chance of a double fill at 8 fills/h ≈ 0.002q extra contracts). Benefit = coverage gap → 0, versus v3's cancel-first 60s cycle costing ~2% of score = $2-4/rung/day. **Strictly dominant.**
4.2 Cancel-first optimum (fallback path only, if the exchange rejects overlapping same-side orders): `E(T) = [1 − g/T]·(1 − e^{−aT})/(aT)`, `g` = seconds lost per requote (round-trip + 1, because a partially-rested second does not count ⇒ 1.2s), `a = ½·λ_eff`, `λ_eff` = rate at which someone gets ahead of us ≈ ½ the measured best-change rate (20%/45s ⇒ 1/450/s ⇒ a = 1/900/s). `T* = sqrt(2g/a) = sqrt(2·1.2·900) =` **46s**, E=94.9%; flat (94.8% at 60s, 92.7% at 120s). Use **45s** in the fallback only.
4.3 **Triggers**, evaluated at 1 Hz per market off the book stream, not a timer: (a) our price ≠ same-side best → requote to best; (b) remaining size < 50% of target `q` → top up; (c) `S` moved >25% → re-run ALLOCATE for that market; (d) `qualifies` flipped on either side → immediate re-evaluate (revival/exit); (e) safety re-sync every **60s** regardless (catches missed stream events).
4.4 **Minimum resting life 30s** before a *voluntary* requote while still at best — anti-gaming P1 (§10) and it kills flicker. Trigger (a) overrides it (a genuine price improvement is not a dodge).
4.5 **At-best tracking.** Per slot maintain `at_best_seconds`, `resting_seconds`, `gap_seconds`; `coverage = at_best_seconds / window_seconds`, logged every second. Target **≥95%** (probe §4). Coverage is the best leading indicator of payout; alert if a slot is <90% for 10 min.
4.6 **Rate budget.** 1 Hz REST book polls × N markets against a ~10 req/s shared budget ⇒ **max 6 markets on REST**. Breadth past 6 REQUIRES the websocket `orderbook_delta` subscription — implement WS first-class; REST 1 Hz is the degraded fallback with an automatic clamp to 6 markets.
4.7 Order body (v3-proven): `POST /portfolio/events/orders`, side bid/ask, `count` "N.00", price 4-dp dollar string, `good_till_canceled` + `expiration_ts` = window_close − 4 min, `taker_at_cross` STP, coid dot-free by construction. **Cancel path is `DELETE /portfolio/events/orders/{id}` ONLY** — `/portfolio/orders/{id}` returns 410 and v1 logged those as success, leaving phantom stacks.

## 5. INVENTORY RECYCLING
5.1 Terms, all measurable: `n` = net contracts held on (m,s); `p_bid` = best bid on the side we hold (what a taker exit receives); `p_mid` = mark; `F` = taker fee = `ceil(0.07·n·p·(1−p))` cents (maker side is fee-exempt — universal, permanent, prod-proven); `h` = hours until the last live program in the deployable set ends; `r*` = current water level ($/h per collateral-$); `R_blocked = (ρ/2)·q/(q+S)` = the $/h of the quoting slot this inventory blocks (the per-rung cap stops that side until flat).
5.2 **The inequality:**
```
TAKER-EXIT iff   n·(p_mid − p_bid) + F   <   h · [ r*·n·p_bid  +  R_blocked ]
                 └─── exit cost ─────┘         └ freed capital ┘  └ unblocked slot ┘
```
5.3 `R_blocked` usually dominates 10-20×. Worked (n=40 @ p=$0.40, 2¢ spread, h=8): LHS = $0.40 + $0.67 = $1.07; RHS = 8·[0.00625·$16 + $1.87] = $15.8 ⇒ exit wins 15×. **Inventory is expensive because it blocks the slot, not because of the capital** — the correct reading of "inventory = ceiling poison."
5.4 **Maker shed is strictly preferred and is not a separate action.** Holding YES, resting a YES ask at `a` IS resting a NO bid at `100−a`: it **scores on the NO side, consumes zero incremental collateral (the position covers it), and unwinds the inventory.** Rule: any (m,s) at/over cap converts that side's quote into a shed quote at the opposing side's best. Escalate to a taker exit only when (i) 5.2 holds AND (ii) the shed quote has not filled in 30 min AND (iii) `h < 2` or a global cap is breached.
5.5 Two-sided fills are a locked box (pay ~99¢, receive exactly $1.00) ⇒ **cap NET, never gross**.

## 6. PRESENCE SCHEDULE (UTC; gas daily window 12:00Z→03:59Z = 16.0h)
6.1 **T0 LAND-GRAB 11:58Z–12:30Z.** Books measured near-empty at open. With `S≈0`, share = `q/(q+S) ≈ 1` for ANY q ⇒ **use the minimum q clearing the forfeit floor and caps; do not size up into an empty book.** Extra size buys nothing when you already own the side — only fill risk.
6.2 **The T0 action that matters is the qualification gate, not size.** With an empty book neither side reaches `target_size` ⇒ every snapshot EXCLUDED ⇒ nobody earns, us included. So at T0, per market, check `qualifies` per side; where short, post `target_size − cum_size` at the cheapest legal price on that side. Sole qualifying bidder ⇒ normalized 1.0 on that side ⇒ **50% of the pool for `target_size · p_min`** ($50/window for $10 at target 1000 @1¢). Capped by §10.3-P7.
6.3 **T1 STEADY 12:30Z–01:00Z.** Water-fill (§2), event-driven requote (§4), checkpoints (§3.4).
6.4 **T2 NEW-MARKET OPEN ~02:00Z.** Re-run the scanner and the T0 procedure on the newly opened next-day market even though its reward window has not started — early presence establishes at-best position before rivals, at the cost of collateral only.
6.5 **T3 PRE-CLOSE 03:00Z–03:55Z.** Final rescue sweep → inventory flatten (§5) → **hard cancel-all at 03:55Z** (4 min before the 03:59Z close; `expiration_ts` is belt-and-braces behind it).
6.6 Window times come from the program object (`start_date`/`end_date`), never hardcoded — Musk/GDP/CPI/Chipotle families run different and sometimes continuous windows.

## 7. SCANNER, POOL TABLE, DAILY RITUAL
7.1 **Machine leg (primary).** 11:30Z daily and on demand: full cursor pull of `incentive_programs` (~120 pages) → filter `end_date > now`, liquidity type, `period_reward > 0` → group by market ticker → join `/markets` (status, close_ts) → orderbooks → §1.2 slot table → ALLOCATE. Cache `~/nestor/data/lip/programs-YYYYMMDD.json`. **Never hardcode $100/rung** (this kills Q4 dead).
7.2 **Operator leg (only what the API lacks).** `~/nestor/data/lip/pools_operator.jsonl`, append-only:
```json
{"ts":"2026-07-28T16:40Z","kind":"competition","event_ticker":"KXAAAGASD-26JUL29","tag":"Medium"}
{"ts":"...","kind":"popover_estimate","market_ticker":"KXAAAGASD-26JUL29-4.105","est_usd":1.42}
{"ts":"...","kind":"credit","program_id":"<id>","paid_usd":5.40,"date":"2026-07-28"}
{"ts":"...","kind":"deny","series":"KXRAIN","reason":"toxic (lane-HOUSE-FEE)"}
```
`kind` ∈ {competition, popover_estimate, credit, deny, allow_override}; unknown kinds ignored with a warn.
7.3 **Refresh ritual (≤5 min, once daily ~16:30Z, after the previous window scores).** (1) Rewards → Current month → reward details: append one `credit` row per program that flipped `paid_out:true`; (2) event Rewards table: append `competition` tags for the day's targets; (3) open 2-3 live popovers: append `popover_estimate` rows. **Nothing in the trading path blocks on this file** — its absence degrades reconciliation, never execution. Competition tags act only as a tie-break at equal marginal rate (prefer Low), never as a gate.
7.4 Seed deny: `KXRAIN` (measured toxic, 40 markets wide). Allow: everything else the machine leg finds. **Do not implement a separate breadth rule** — the charter's "$300-600/event then breadth" falls out of ALLOCATE automatically, because a saturated event's marginal rate drops below a fresh event's first dollar. The water level IS the breadth policy.

## 8. RISK CAPS
8.1 **Per-slot inventory cap `n_cap = floor($10 / p)`, on NET (§5.5).** A slot's maximum earning is `ρ·H/2 = $50/window`; capping worst-case one-sided loss at $10 (20% of that) keeps the slot EV-positive even if the position goes to zero. It scales as `1/p` — 25 contracts at 40¢, 500 at 2¢ — the cheap-side law arriving from the risk side.
8.2 **Per-market cap:** collateral ≤ `min(4·ρ·H, 0.25·budget)`. First term: never risk 4× a market's own maximum prize on it. Second: no single-market concentration.
8.3 **Global collateral ceiling:** config, default **$300** (R168 ladder $300 Tue → $1k Wed, each rung funded by the previous window's observed print, never by the model). Hard refuse to exceed.
8.4 **Global day stop:** `−max($20, 0.35 × projected_day_reward)`, capped at −$150. 35% is the largest drag leaving the day net-positive under the §2.2 22%/window fill drag plus margin; $20 floor is the probe convention; $150 cap because a larger single-day loss invalidates the ladder regardless of ratio. On breach: cancel-all → flatten (§5.4) → alert → exit.
8.5 **Poison rules (v3-inherited, non-negotiable):** any non-200/404 cancel ⇒ order "may be live" ⇒ stop posting that market. 3 consecutive cancel anomalies on a market ⇒ poison for the day. 6 post errors in 5 min globally ⇒ cancel-all + alert + exit. 409, or a placement response with no `order_id` ⇒ poison that market immediately.
8.6 **Never trust the resting-orders or positions indexes** (R169 — eventually consistent; v1 stacked 13 orders on one rung by trusting them). All self-knowledge from synchronous truths (§9). The single exception: the **fills** endpoint scoped by `order_id`/time — fills are immutable historical facts, not an index of live state — used only in §9.4's 404 disambiguation.
8.7 Post-size and refill-cap are decoupled knobs (v3 lesson): `q_target` per slot from ALLOCATE; `refill_cap` = contracts a slot may re-post per window, default `4·n_cap` (four turnovers; beyond that the slot is a flow magnet, not a maker).
8.8 Abort immediately on: a fill at a price we did not intend, any self-trade, or `our_bid ≥ our_ask` on one market in our own book.

## 9. LEDGER, RESTART, RECOVERY (v3 loses filled_cum AND collateral on restart — this is the fix)
9.1 Append-only JSONL `~/nestor/data/lip/ledger.jsonl`, fsync'd before the next wire call:
```
{"t":"place_req","coid","ticker","side","price_c","size","ts"}
{"t":"place_resp","coid","order_id","fill_count","remaining_count","ts"}   # or {"err":...}
{"t":"cancel_req","order_id","ts"} / {"t":"cancel_resp","order_id","reduced_by","http","ts"}
{"t":"fill_obs","order_id","count","price_c","ts","src":"fills_api"}       # 404 disambiguation only
{"t":"snapshot","positions":{…},"collateral_usd":N,"orders":{…},"ts"}      # every 60s, advisory
```
9.2 **Filled invariant, per order:** `filled = fill_count + (remaining_count − reduced_by)`; `filled_cum(m,s) = Σ` over that slot's orders.
9.3 **Collateral:** `Σ_live_orders(remaining · price) + Σ_positions(n · entry_p)` — both from ledger replay, never from an exchange index.
9.4 **Restart procedure (ordered, mandatory):** (1) replay ledger → per-order state, `filled_cum`, positions, collateral; (2) any order with a `place_resp` and no terminal `cancel_resp` is UNKNOWN; (3) for each UNKNOWN issue `DELETE /portfolio/events/orders/{id}` — **200** ⇒ close with `reduced_by`; **404 is ambiguous** (fully filled OR expired) ⇒ query the fills endpoint for that `order_id`, write `fill_obs`, and if fills exist `filled = fill_count + fills_since`, else treat as expired (`filled = fill_count`); **if the fills query fails, assume fully filled** (conservative on inventory) and poison the market until a human clears it; (4) sweep-cancel every resting order carrying our stable coid prefix; (5) re-derive positions from `filled_cum`; any market that hit the assume-filled branch is refused for the session.
9.5 **Coid scheme, stable across restarts:** `lipm-{ticker_sanitized}-{y|n}-{seq}`, `seq` from a persisted counter, dot-free by construction (engine sanitizes `.`→`_` at the wire since 40d4a18). **A run-id in the prefix would make step 4 blind to the previous process's orders — that is exactly v3's loss. Do not.**
9.6 Every exit path, signals included, cancels all owned orders first.

## 10. ANTI-GAMING (OPEN QUESTION 1) — policy and priced risk
10.1 **The price.** Program dies 2026-09-01 (verbatim in the filing) ≈ 34 days. Achievable $100-300/day at $300-1k ⇒ remaining program EV ≈ **$3.4k-$10k, call it $8k**. Each +10 percentage points of revocation probability costs **~$800**. The revival trade's incremental value is ~$100/day × 34 = **$3.4k** ⇒ *a revival policy is negative-EV above ~40% revocation risk.* Print this number in the run log so the tradeoff is never made implicitly.
10.2 **The line, derived from the program's stated purpose (liquidity that helps takers):** would a taker be better off because our order exists? A 1¢ bid on a dead side passes — real collateral at risk, hittable, and it converts a market where *no snapshot can be included and nobody is paid* into one where everyone is. That is the Target-Size gate working as designed.
10.3 **Enforceable policy — all in code, all logged.**
 **P1 Honorable quotes.** Real collateral; no cancel-on-approach; minimum resting life 30s (§4.4); make-before-break means we never withdraw liquidity to dodge a taker.
 **P2 No snapshot timing games.** Cadence is book-driven only; never modulate size or presence on any inferred snapshot schedule (nonpublic anyway); no sub-second flicker.
 **P3 Two-sided by default.** Never quote one side alone except where the other is legally impossible (pinned) or we are shedding. One-sided quoting is the pattern that reads as farming.
 **P4 Fill-honoring metric**, published daily: `fills_taken / (fills_taken + cancels_within_2s_of_touch)`; target ≥0.95, investigate below 0.90 for a day.
 **P5 Cheap-side share cap:** ≤60% of daily SCORE from sides priced <5¢. **UNDERIVED constant (§15.3).**
 **P6 Revealed-usefulness test — the rule that actually settles Q1.** Any slot with **zero taker fills over 5 consecutive days** is by revealed behavior not liquidity anyone wants: drop it, independent of anti-gaming. Conversely a cheap slot that *is* hit is demonstrably useful and P5 may be relaxed for it. This converts an unanswerable intent question into a measured one.
 **P7 Revival caps:** ≤3 concurrent revival markets; never >90% of a qualifying side for more than 5 consecutive days on the same market (the state where appearance risk peaks).
 **P8 One account, ever.** Multiple accounts is the one behavior indefensible on its face.
 **P9 Sentence test:** if our behavior cannot be described in one sentence matching the program's stated purpose, it does not ship.
10.4 **Disclosure, priced.** Emailing Kalshi converts unknown risk into a known answer at the cost of attention. At $300-$1k we are 1-4% of a $23.8k/day program — below notice; at $5k+ we are ~20% and get noticed anyway. **Rule: do not disclose below $1k deployed; disclose before any deployment above $2k.**
10.5 **Safe under either answer.** Every item above is a *restriction*. If Q1 is permissive we forgo ≤10% of modeled reward (P5 is the only binding one, and only under extreme cheap-side tilt). If Q1 is strict, P1-P9 are already the compliant posture.

## 11. THE OTHER FOUR OPEN QUESTIONS — position / evidence / behavior under uncertainty
11.1 **Q2 ($1.00 exactly).** Position: **pays** — the filing reads "if the result is ≥ $1.00, the result is paid out." Evidence: last night's $1.00 popover rung vs its credit, readable today. Spec: `ENTRY_FLOOR $2.00` and `RESCUE_TARGET $1.10` sit clear of the boundary on both readings and absorb the round-down-to-cent. **No code path depends on the answer.**
11.2 **Q3 (tie-splitting at best).** Position: **there is no tie-break — the formula is already pro-rata by size** (`Score = size·DF^0` for everyone at best, normalized by the sum); time priority appears nowhere in the filing. Evidence: our credit vs a same-price rival of known book size, one window. Spec: `share = q/(q+W+S)` is the pro-rata model and the conservative one; if time priority secretly existed, the T0 land-grab (§6.1) is exactly the hedge that captures it. Safe both ways.
11.3 **Q4 (pool uniformity).** Position: **the question dissolves** — `period_reward` is per-program in the public feed, so the spec reads the real number per rung and never assumes uniformity (§7.1). Evidence already in hand (17 × 1,000,000 on gas). Spec: hardcoding $100 anywhere is a review blocker.
11.4 **Q5 (cents vs price levels).** Position: **cents** — the filing writes `DF^(RefPrice−Price)`, an arithmetic price difference. Evidence: one deliberate order 6 ticks / 1 level behind best on a gapped book — cents predicts 1.6% credit, levels predicts 50%, a 32× separation settled in one window for ~$1. Spec: §1.5 — quote only at distance 0 or 1 (readings identical), compute both `S`, use conservative `S_levels` for entry and `S_cents` for reconciliation. Safe both ways.

## 12. DAILY RECONCILIATION LEDGER
12.1 `~/nestor/data/lip/recon.jsonl`, one row per (date, program_id): `date, program_id, market_ticker, series, pool_usd, window_start, window_end, model_share, model_usd, popover_est_usd, popover_ts, paid_usd, paid_out_flag, credit_ts, collateral_avg_usd, collateral_peak_usd, coverage_pct, at_best_pct, fills_ct, fill_notional_usd, drift_usd, taker_fees_usd, net_usd, cheap_side_score_pct, fill_honor_ratio`. The last two are §10 P5/P4 telemetry; `popover_est_usd` and `paid_usd` come from the operator leg (§7.2) and may be absent.
12.2 Day summary row adds: total paid, total model, `ratio = paid/model`, the list of forfeited markets with their projections, and the achieved water level `r*`.
12.3 **Stand-down:** `|log2(paid/model)| > 1` (worse than 2×) on the day aggregate for **2 consecutive days** ⇒ halt deployment and re-derive the model against the captured book tape. Same 2× constant as `ENTRY_FLOOR` (§3.1), by construction.
12.4 The model projection must be computed on the window's **own** captured book snapshots (sample every 30s, store), never a pre-window snapshot — otherwise reconciliation grades the model on data it never saw.

## 13. NESTOR INTEGRATION (charter §9 — decision, derived)
13.1 The charter offers (A) nestor strategy crate, breaker-visible, external_cash dies; or (B) standalone + operator-ledger bridge. **Both are dominated.** The bridge's entire information content is "cash moved that nestor does not control" — a **separate subaccount makes that quantity structurally zero**, so the bridge's value is zero and it is deleted rather than maintained. And (A) subordinates a **≥95%-uptime business to nestor's drawdown halts** — the halt is the exact event that destroys LIP score, and LIP score is uncorrelated with whatever caused the halt.
13.2 **DECISION: standalone binary, separate Kalshi subaccount, restricted trade-only key, no bridge.** Own capital sleeve, own uptime, own deploy cadence, zero self-trade surface with nestor's takers.
13.3 **Gate:** the restricted subaccount key MUST support `good_till_canceled` + `expiration_ts` and coid-scoped cancels (probe §7's unpriced question). **Test with one $1 order before any capital moves.**
13.4 **Interim, until the subaccount exists (~Wed):** run standalone in the main account with a **fixed, pre-declared** external-cash reservation — a constant, written once, never tuned. R172's contamination came from tuning the allowance against a then-corrupt moving bankroll; a constant reservation cannot be corrupted by timing because it carries no timing. Reservation = the §8.3 ceiling ($300).
13.5 Deploy: native aarch64 built on the VPS from `~/nestor-src` (R174 cross-compile trap; the box is aarch64 Ampere). `file` the artifact against `uname -m` before shipping. No hand-deploys.
13.6 Alerts (ntfy `senate-nestor-2732e947`): halt, poison, stop-loss, coverage <90% for 10 min, §12.3 stand-down.

## 14. TEST PLAN — the money rules as pure functions (no network in any test)
14.1 **`allocate(slots, budget, caps, λ_min) -> {slot: qty}`**
 T1 *Cheap-side-first:* two slots, `ρ=6.25`, `S=50`; A `p=0.68`, B `p=0.02`; budget $20 ⇒ **all to B** (rate ratio 34×), B qty ≈1000, A qty 0.
 T2 *Thin-S beats equal-price:* both `p=0.40`, A `S=1000`, B `S=50` ⇒ B first until crossover; assert `S_A/(q_A+S_A)² = S_B/(q_B+S_B)²` at the stopping point.
 T3 *Wall skip (§2.7):* `ρ=6.25, p=0.40, r*=0.00625, W+S=2000 > 1250` ⇒ qty 0 and the budget flows elsewhere.
 T4 *Hurdle bite:* `f=8/h, d=$0.07` ⇒ fillcost $0.56/h on `q·p=$4` ⇒ hurdle 0.14/h ≫ attainable ⇒ qty 0.
 T5 *Budget exact:* `Σ qty·p ≤ budget` and `> budget − max_slot_price` (no lazy under-fill).
 T6 *Caps:* no slot exceeds `n_cap = floor(10/p)`; freed budget re-fills.
 T7 *Determinism:* identical output over 100 runs (tie-break ticker → side).
14.2 **`forfeit_gate(projected)` / `rescue(A, rate, h, ρ, S, q, p, r*, C)`**
 T8 $1.99 ⇒ False; $2.00 ⇒ True (boundary inclusive).
 T9 Last night's burned rungs $0.95/$0.33/$0.17/$0.08/$0.01 ⇒ all False; assert the 22% tax is recovered — the $6.94-earned/$5.40-payable scenario with the gate applied yields payable == earned.
 T10 A=$0.60, rate=$0.05/h, h=3 ⇒ proj $0.75 < $1.10; ∃Δq raising rate to $0.20/h ⇒ proj $1.20 > redeploy value ⇒ **TopUp**.
 T11 Same with h=0.2 (no Δq reaches $1.10 given the `ρ/2` ceiling) ⇒ **Abandon**.
 T12 A=$1.40, rate 0 ⇒ **Keep** (never abandon an already-cleared market).
 T13 Single-live-event world (§3.7): Abandon returns "cancel-only, no redeploy" and reports **no** redeploy benefit.
14.3 **`recycle(n, p_e, p_bid, p_mid, fee_fn, h, r*, R_blocked)`**
 T14 §5.3 numbers ⇒ inequality holds ($1.07 < $15.8) but shed-first policy ⇒ **MakerShed**.
 T15 Same with shed_age > 30 min and h=1.5 ⇒ **TakerExit**.
 T16 h=0.1, 10¢ spread, `R_blocked=$0.10/h` ⇒ LHS $4.67 > RHS $0.03 ⇒ **Hold** (exit destroys value).
 T17 Fee: `F = ceil(0.07·n·p·(1−p))` cents ⇒ n=40, p=0.40 gives 68¢; the maker/shed path charges **zero**.
 T18 n_yes=30, n_no=30 ⇒ net 0 ⇒ **Hold** (locked box, §5.5), not two exits.
14.4 **`at_best` / `coverage` / `requote_triggers`**
 T19 Our bid 40¢, best 40¢ ⇒ at_best True; best → 41¢ ⇒ trigger (a) within one poll.
 T20 At best, 10s old, `S` unchanged ⇒ **no requote** (P1). At best, 10s old, best moved ⇒ **requote** ((a) overrides).
 T21 Coverage: a tape with a 1.2s gap per 60s cycle ⇒ 98.0%; make-before-break tape ⇒ 100.0%. The metered 2% must match v3's measured 2% loss — the model validating itself.
 T22 Partial fill to 40% of target ⇒ trigger (b) fires; to 60% ⇒ does not.
14.5 **`score_side(levels, target_size, df, mode)` — the CFTC algorithm**
 T23 verify §3b 4.100 yes side (ref 68¢, top 35, 3,489 resting) ⇒ **S = 60.5 ± 0.1**; no side (ref 31¢) ⇒ **S = 5.2**. Measured reference values; mismatch means the scorer is wrong.
 T24 Our 100 lots at 68¢ ⇒ share **62.3%**; 100 at 31¢ ⇒ **95.0%**.
 T25 Bids run out before target_size ⇒ `qualifies=False` and the qualifying set **CLEARED**, not partial.
 T26 Pinned: opposing best bid 99¢ ⇒ pinned; opposing best ask 1¢ ⇒ pinned. All 10 pinned gas rungs classify pinned; all 7 qualifying classify not-pinned.
 T27 Q5: gapped book (best 40¢, next level 34¢) ⇒ `S_cents` uses `0.5^6`, `S_levels` uses `0.5^1`; assert `S_levels > S_cents` and that ALLOCATE consumes `S_levels`.
 T28 Size-ladder regression: joining both sides of the 7 qualifying gas rungs at 1/10/100/300/1000 reproduces **$20.14 / $105.16 / $311.42 / $432.43 / $652.03** within 1%.
14.6 **`ledger_replay(records) -> {filled_cum, collateral, positions, unknown_orders}`**
 T29 place(10, fill 2, rem 8) + cancel(reduced_by 5) ⇒ **filled 5**, collateral 0.
 T30 place(10,0,10) + cancel 404 + fills_api shows 10 ⇒ filled 10, position +10.
 T31 place(10,0,10) + cancel 404 + fills_api shows none ⇒ filled 0 (expired), position 0.
 T32 place(10,0,10) + cancel 404 + fills_api **errors** ⇒ filled 10 (conservative) AND market poisoned.
 T33 cancel returns 410 ⇒ anomaly, order "may be live", stop posting that market (v1's exact loss).
 T34 **Restart parity:** replay a 4-hour synthetic tape; reconstructed `filled_cum`, positions and collateral equal the live-process values exactly. This is the test v3 would have failed.
 T35 Coid stability: orders placed by run N are matched by run N+1's prefix sweep (no run-id in prefix).

## 15. UNDERIVED / CALIBRATION-PENDING (flag upward, never silently ship)
15.1 `λ_min = 0.10` per collateral-$ per window (§2.3) — a round number above the computed 16-22% fill drag; recalibrate to the realized marginal-slot rate after 5 days.
15.2 `ENTRY_FLOOR = $2.00` (§3.1) — from the 2× self-consistency argument, not from a measured distribution of `actual/projected`; recalibrate to `$1.00 / q05(ratio)` after 5 reconciled days.
15.3 `P5 cheap-side score cap = 60%` (§10.3) — the genuinely arbitrary anti-gaming constant; P6's revealed-usefulness test is its principled replacement once 5 days of fill data exist.
15.4 Seeded `f` (8/h hot, 1/h cheap) and `d` ($0.07/contract) (§2.2) — single-session observations (R174, charter); replace with own-tape estimates per (series, side-band) at n≥200 fills.
15.5 Minimum resting life 30s (§4.4) — chosen ≫ the ~1s snapshot granularity and ≪ the ~225s best-change interval; no measurement distinguishes 20s from 45s.
15.6 Make-before-break (§4.1) assumes the exchange accepts two same-side orders from one account without margin rejection. **Verify with one $1 pair before relying on it**; if rejected, fall back to §4.2's 45s cancel-first cadence and accept the ~2% coverage loss.
15.7 Day stop at 35% of projected reward (§8.4) — the ratio is judgment; the $20 floor and $150 cap are inherited conventions.
