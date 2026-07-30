# 51 - THE LIP THEORY — Ryan's derivation, 2026-07-29 night

> Ryan wrote the strategy top-down and asked for holes. This records the chain, the corrections,
> **what [[47]] already said better**, and the two live conflicts. Read with [[47]] §3 and §5 —
> those two sections outrank anything derived here from first principles without data.

## 1. THE CHAIN (Ryan's, and it is sound)

`profit = rewards − position P&L`. Rewards = our score share × presence × pool. **You cannot be
present if you are filled** — a fill converts an order into inventory and removes us from the pool
until we re-enter. Therefore fill rate is a *capital requirement*, not merely a presence discount:

> **capital to hold presence for a window = size × price × (1 + λW).**
> At λ=1.5/h over 8h that is **13× the resting notional.** A rung that fills 12×/window cannot be
> held present on $10 no matter how cheap the floor is.

The selection question is therefore **"can $10 clear $1 here *and stay* present"**, not "can $10
clear $1 here." This is sharper than the old `λ ≤ 1/W` rule and it replaces it.

**The four levers on position P&L**, in Ryan's ordering: (1) more capital + more time to reach the
mean — we have neither; (2) sell early to cut variance — measured cost, see [[47]] §6 (+2¢ leg
−$40.30, instant-flatten −$123); (3) smaller orders — **blocked by the $1 floor**; (4) spread
across uncorrelated markets — **the chosen lever.**

## 2. WHAT I HAD WRONG, AND RYAN HAD RIGHT

**Breadth is not an earnings tool.** I said it was; it is not. The earnings argument is
**concavity** — our share `q/(q+S)` has falling marginal return inside a market — plus the
per-market floor. The correct rule that falls out is *equalize marginal reward per dollar across
markets, floor first*, which is exactly what Ryan said.

**And his stronger claim is also right:** a dollar in a market that has ALREADY cleared the $1
floor carries **no forfeit risk**, so its expected marginal return beats the same dollar in a
market that may land under $1 and pay zero. *"If $10 can earn 50¢, it should do it on the one
already earned."* Correct. Breadth is the variance tool and only the variance tool.

## 3. THREE MECHANICAL CORRECTIONS TO THE CHAIN

1. **The denominator is SCORE, not capital.** `Score = DiscountFactor^ticks × size`, size in
   **contracts**. **The price is absent.** $10 buys 500 contracts at 2¢ and 83 at 12¢ — cheap
   rungs are ~6× more score-efficient per dollar. The same knob that buys score efficiency buys
   position toxicity. That tension *is* the problem.
2. **Per-side normalisation** — a one-sided quote earns at most `pool × share ÷ 2`.
3. **The floor is per market per PROGRAM PERIOD**, not per window. On hourly programs $1 is owed
   *each hour*. The five temperature-hourly series (~1,070 programs each, the board's largest
   supplier, never touched) face this bar, not the daily one.

## 4. WHERE MY PRICE FLOOR WAS WRONG — [[47]] §5 ALREADY SETTLED THIS

I derived a **10–12¢ floor** from `P(zero winners) = (1−p)^N` at **N = 30 fixed**. That is the
`p_min = k/bankroll` idea that [[47]] §6 **pre-registered as refuted, "do not rediscover."** I
rediscovered it in a costume. The error: **I held per-rung cost at $10 across all prices.** But
floor-clearing size is a CONTRACT count `Q = S/(P−1)` with no price term, so cost per rung is
`Q·p` and **falls with price** — a 2¢ rung costs a tenth of a 20¢ rung. At fixed budget B you
afford `N = B/(Q·p)` rungs, so `E[hits] = N·p = B/Q` and **the price cancels.**

**The regime that reconciles both:** price cancels *while N is capacity-unconstrained*. It binds
again once N hits the **independence ceiling** — 358 live settle sources, and only **177** carry a
rung at 7–12% clearing $1 for ≤$20 ([[47]] §4). Inside the ceiling, spend the price budget on
bias, not on ruin.

**Because price still matters enormously — through measured BIAS, not variance** ([[47]] §3,
n=8,240): 2¢ realised **0.00% on 765 markets** (EV −100%), 1¢ −94.8%, 3–5¢ −64.6%, 6–20¢ n.s.
Competition is U-shaped — median rival score **6,618 at 1¢**, **403 at 11–20¢**, 2,877 at 96–99¢ —
so **11–20¢ is simultaneously the cheapest floor to clear ($3.68 median), the emptiest side, and
the only band without measured negative bias.** That is a better answer than either a variance
floor or a flat "go cheap."

## 5. WHAT WAS NOT PREVIOUSLY CAPTURED (new here)

- **Pool size was the special thing about the receipt venues.** All six credited events had
  `pool = $100`. Modal pool is **$20**, median **$45**. Credit is *linear* in pool, so the
  $71.34 → "$2 per rung" extrapolation silently assumes the 34.6% tail. At median pools the same
  share and presence gives **$32**; at modal, **$14**. This is the mechanism behind [[47]] §4's
  "treat any modelled accrual as 4–8× hot."
- **Marginal return on an already-cleared rung** (§2) — the forfeit-risk asymmetry.
- **Fill as a capital multiplier** `(1 + λW)` (§1).
- **The null for the positions is not EV-0.** Ryan assumed ±5%. Three drags: maker fees on every
  fill; the measured §3 bias; and **adverse selection** — our fills are not a random draw from the
  price distribution, they are the subset where someone wanted the other side. Our own tape:
  matched pairs **+$39.63**, unmatched legs **−$587.42**.
- **"Uncorrelated" must mean settle source, not ticker.** And on a threshold ladder the cheap leg
  is always the *outside* leg, so buying cheap on every rung is a long butterfly bought at the
  offer — structurally negative. Thirty tickers on three underlyings is N_eff ≈ 3.

## 6. THE TWO LIVE CONFLICTS

**A. FREE_RIDE_ONLY is contested inside the vault, and the build picked a side silently.**
[[46]] and [[47]] §6 call it **"actively harmful — a covert instruction to quote the cheap crowded
side"** (only 144 of 536 mid-priced sides clear target_size, and those are the crowded ones).
[[48]] sets `FREE_RIDE_ONLY = True`. `work/audit-nonlip-strategies-2026-07-28.md` measures it as
**the strongest single rule on the tape** (−$51.40 vs −$75.10). **Both are true:** it is the best
loss-reducer *and* it steers to 1¢ where rival score is 6,618. **Resolution: free-ride is correct
only when paired with a band constraint** — free-ride *within* 7–20¢. Armed today (commit 3 of 4)
**without** that pairing. Unresolved; do not deploy on the strength of [[48]] alone.

**B. λ is still unmeasured.** [[47]] §7 lists "fill probability on resting cheap bids" as OPEN;
[[50]] §5 lists the λ filter as unimplemented (needs a new classify-sweep field). §1 above makes λ
the *binding* input, so this is now the highest-value measurement, ahead of venue work.

## 7. WHAT THIS CHANGES ELSEWHERE
- [[47]] §5's "survivability is governed by independence, not price" gets the regime qualifier in
  §4 above: true inside the independence ceiling, false at it.
- [[49]] gains a case: R3's pre-registered falsifier existed (`p_min = k/bankroll`, "do not
  rediscover") and I walked past it and re-derived it with a different denominator. **A refutation
  filed under one name does not protect against the same idea wearing another.**
