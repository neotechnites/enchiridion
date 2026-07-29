# 46 - MORNING BRIEF — 2026-07-29, 5:45am MT

> Written 2026-07-28 ~11pm MT for Ryan waking at 5:45. Read this first, then [[42 - SPIN-UP]].
> Nothing is trading that can lose money. Nestor is live and healthy. v5 is stopped and disarmed.

## THE ONE THING TO DO AT 6:00am
Open the Kalshi **Credits** page. For every line item record **ticker · dollars · PAID or ESTIMATED**.
Sum ONLY the PAID column. Also check **Settings → Documents/Statements** and any portfolio
**Export/CSV** for a per-MARKET breakdown — the credits page shows per-EVENT only, which is not
enough fidelity to tell which rung earned.

**Then compare the PAID total against these thresholds:**

| PAID credits/day | what it means | action |
|---|---|---|
| **< $4.40** | below breakeven even after the price floor | LIP is not a business at our scale — stop |
| **$4.40 – $23** | floor wins: it buys ~$23/day of position improvement, costs most of the reward | run gas-only, 20/80, $10/rung, 10 sessions, hard stop at −$50 |
| **$23 – $29** | the tape cannot distinguish | neither — measure another day |
| **> $29** | the barbell IS the business; the floor would delete the income | do NOT impose the floor; needs a different guard for the −100% tail |

The one verified receipt is **$7.482/day**. Operator estimate is $34–37/day. Estimates on this
venue have run **4–8× hot**, and the $8+$7 recollection alone exceeds the entire verified
receipt — so those were almost certainly ESTIMATES, not payments. That gap is the whole question.

## STATE
- **nestor** — live, unhalted, bankroll $92.74, divergence Δ$0.46 on the $2.00 band. It halted at
  ~10:20pm because v5's cash feed went stale after v5 stopped; repaired with a −$12.44 external-cash
  entry. **Design gap: v5's shutdown zeroes the feed but nestor reads the last value for 2h before
  anyone notices.**
- **v5** — STOPPED, gate artifact deleted, cannot re-arm. 601 tests green, nothing behavioural
  shipped tonight. **Do not restart as-is:** `LAND_GRAB_PRICE_C = 1` is live and is the exact
  geometry of the cohort that returned −100%.
- **Account** — ~$375. Positions ride to settlement. Gas 26JUL29 closed 9:59pm; treasury 26JUL29
  closes 1:30pm today.
- **Capital** — Ryan added $225 (recorded). Total available $1–2k, not yet deployed.

## WHAT WAS MEASURED (all validated against exchange truth to $0.000)
- Position P&L **−$74.52 on $928.70** deployed / 66 settled markets = **−8.0%** (NOT −48%; that
  was my error comparing realised losses to the open book's basis).
- **84% of losses from 15 markets** holding cheap residual at ~5.6¢ → **exactly −100.0%**,
  1,123 contracts in, $0.00 out. 6.7% of capital, 37% of all contracts ever held.
- **Only 28.1% of contracts ever netted.** Of pairs that formed, **47% cost >$1.00 combined**.
- Mid-priced directional P&L **not significant** (t = −0.49).
- **Spread capture never demonstrated:** +$14.08 total, ex-top-3 **−$18.32 (t = −2.24)**.
- v5's own 397 orders: **51% at ≤2% implied win probability, 83% at ≤5%.** Mean 6.2%, median 2.0%.
- Ladders are **barbells**: our presence 76% below 20¢, **0.3% between 20–80¢**, 23% above 80¢.
- Family split at 20/80: crypto 15-min **−$27.43**, treasury −$1.11, **gas +$4.40/day**.

## WHAT WAS TESTED AND REFUTED (all on our own tape)
- **+2¢ resting exit leg** — costs **$40.30** vs doing nothing; nets ZERO contracts (fires before
  the opposing side can fill). The most elegant idea of the night, and the worst.
- **Box / joint ≤98¢ sum guard** — near-decoration; zero pairs exceeded $1.00 with or without it.
  The 47% pathology came from LADDERS repricing independently, not from missing a constraint.
- **20–50¢ two-sided band** — geometrically impossible: on a binary the two legs sum to 100 minus
  the spread, so it constrains the MID, not the legs. Available 2.16% of minutes.
- **Bankroll-driven probability band** (`p_min = k/bankroll`) — the price **cancels**:
  cost/rung scales with price, so expected hits = bankroll ÷ contracts-per-rung, independent of
  band. Survivability is governed by **independent settle sources**, not price.

## IN FLIGHT (results due before 5:45)
1. **Calibration** — are cheap Kalshi contracts fairly priced? Thousands of settled markets,
   realised win rate vs price by bucket. Decides whether longshots are EV-0 (rewards are pure
   profit, need bankroll) or overpriced (nothing works).
2. **Competition map + breadth** — pool dollars per unit of COMPETING score by price band, and
   how many independent settle sources have live windows at once. The missing denominator: our
   whole barbell logic ignored that everyone else crowds the cheap rungs too.
3. **Price-corrected sizing backtest** at $300 / $1k / $2k.
4. **Two fresh derivations** — one on whether LIP is the right business at all, one from
   microstructure on the premise that we have never actually been market making (72% held to expiry).

## THE HONEST SUMMARY
Every elegant idea generated tonight lost money on our own tape. The only survivors are boring:
**don't buy what you can't sell**, and **don't let fifty tickets be one draw**. The enchiridion now
carries [[23 - Derivation First]] **Part V (derive the losing path)** — verified working by a fresh
agent that independently refused the trap — and [[43]] §1/§5/§7 corrected, including the line that
caused this: *"the cheap side is cheap to be wrong about"* is true per contract and false per dollar.

**Biggest open risk:** the rungs that earn the rewards may be the same rungs that return −100%.
If credits concentrate in longshots, there may be no profitable configuration of this program for
us. The calibration run is the test.
