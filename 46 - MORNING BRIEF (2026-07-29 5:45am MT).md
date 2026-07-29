# 46 - MORNING BRIEF — 2026-07-29, 5:45am MT

> Written 2026-07-28 ~11pm MT for Ryan waking at 5:45. Read this first, then [[42 - SPIN-UP]].
> Nothing is trading that can lose money. Nestor is live and healthy. v5 is stopped and disarmed.

## ⚠ READ THIS FIRST — IT SUPERSEDES THE DECISION TABLE BELOW

**Cheap Kalshi contracts are overpriced ~8×. Measured on n = 8,240 settled markets, one
observation each, real two-sided quotes, 11 pre-specified families.**

| price | n | posted | **REALISED** | EV per $1 |
|---|---|---|---|---|
| **1¢** | 3,205 | 0.60% | **0.03%** | **−94.8%** |
| **2¢** | 765 | 1.57% | **0.00%** (zero of 765) | **−100%** |
| **3–5¢** | 333 | 3.39% | 1.20% | **−64.6%** |
| 6–20¢ | 591 | — | — | −13 to −19% (not significant) |
| **41–60¢** | 345 | 50.4% | **57.1%** | **+13.3%** |
| **61–80¢** | 707 | 71.7% | **84.9%** | **+18.4%** |
| 81–99¢ | 1,852 | — | — | +2.1 to +5.6% |

**Our 15-of-15 wipeout was not bad luck — it was the expected outcome.** True rate at those
prices is 0.12%, so expected losers in 15 markets is 14.98 and P(all 15 lose) ≈ **98%**. The
earlier "46% chance" figure was wrong. **That capital was not lost to variance; it was the price
of the trade.**

**THREE THINGS FOLLOW, AND THEY DECIDE EVERYTHING:**

1. **Never quote ≤5¢ again.** Not a risk preference — a measured −65% to −100% expected value.
   And on Kalshi a resting cheap bid is a **BUY**: we were not selling lottery tickets, we were
   holding them.
2. **The sweet spot is 11–20¢.** Competition is U-shaped — median competing score **6,618 at 1¢,
   403 at 11–20¢, 2,877 at 96–99¢** — so the middle is where everyone *isn't*. It is also the
   **cheapest place to clear the $1 floor ($3.68 median**, versus $6.36 at 1–5¢ and $244 at
   96–99¢). Ryan's question answered directly: **yes, 317 rungs at 7–12% cost ≤$20 to clear $1,
   across 177 independent clusters.**
3. **41–80¢ is a measured BUYING EDGE of +13 to +18%** — that is not a subsidy, it is an actual
   trading edge, and it may be worth more than the entire reward program. It also upgrades the
   "take the other side" mirror trade from CONDITIONAL to measured.

**Also:** 81–99¢ is the *worst* reward real estate on the board (0.16–0.95%/day) and held
**95.3% of our capital**. The free-ride rule is actively harmful — it is a covert instruction to
quote the cheap crowded side. And of 2,983 live programs there are **358 independent settle
sources**, but **all 75 dailies are treasury rungs — one source**, so a dailies-only rule
concentrates rather than diversifies.

**Venue note:** mention markets are *fair* at the cheap end (1.55% realised vs 1.69% posted).
Gas, metals and index hourlies — the venues we actually farmed — are the overpriced ones.

**Two numbers reported with alarms and NOT to be acted on:** a portfolio projection of 22%/day at
$1,000, and a sizing model implying $851/window of reward on $300. Both contradict the verified
$7.482 receipt by 10–20×, in the same direction and magnitude as the accrual model already found
4–8× hot. Treat as hypotheses with a receipt-shaped test attached.

### ⚠⚠ DO NOT ACT ON THE +13–18% "EDGE" WITHOUT READING THIS

An independent microstructure derivation flags a direct conflict: **[[07 - Overfitting &
Validation Discipline]] already tested systematically buying 80–95¢ favourites, and it COLLAPSED
out-of-period.** The calibration above is in-sample across 8,240 markets priced at close−60min.
The favourite side being underpriced is the *mechanism* that explains our measured −100% on the
cheap side; it is **not** a licence to trade its complement. Any move onto the favourite side
needs a fresh out-of-period test first, or we will have re-discovered a claim the corpus already
killed and mistaken it for new evidence.

### THE OTHER THING THAT DERIVATION ESTABLISHED

**We never tested market making. We tested longshot farming.** With 0.3% of presence in the
20–80¢ band, our −$74.52 cannot falsify making on this venue — it falsifies *where we stood*.
Resting a bid at 5.6¢ puts us on the **same side as retail** (who overpay for lottery tickets),
competing for the same overpriced ticket, not opposite the flow as a maker is supposed to be.
The correct name for what we ran: **a subsidised retail-side longshot buyer with a one-tick entry
discount.**

Two more corrections worth keeping:
- **Variance per DOLLAR is (1−p)/p — 16.9 at 5.6¢, 1.0 at 50¢, 0.053 at 95¢.** Textbook
  inventory models use per-CONTRACT variance p(1−p), which says cheap is *safe*. Wrong
  denominator, same trap as everything else this week.
- **Binary variance does not decay with time to close** — it is realised in one instant. So
  time-to-close must NEVER relax an inventory limit, which is the opposite of the usual rule.
- **Cheap flow is adverse selection too:** a rung is at 5¢ near close *precisely because* the
  underlying already moved away from it. Every cheap fill is someone selling you a known zero.

**Break-even for a mid-band book: subsidy must exceed 1.39% per turn of capital.** That is the
whole business in one number, and it is the one we have never isolated.

---

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

## TWO CORRECTIONS THAT ARRIVED AFTER THE TABLE ABOVE (both matter more than it does)

**1. The floor must be `min(p, 1−p) ≥ 15¢`, NOT `p ≥ 15¢`.** A 92¢ YES bid *is* an 8¢ NO. The
23% of our presence sitting above 80¢ is the SAME one-way door wearing the other side's clothes —
un-nettable, un-exitable, and it has produced almost no adverse resolutions in 6 days purely by
luck. A floor written on `p` alone leaves it wide open, and every price band anyone proposed
tonight (including mine) was written on `p` alone.

**2. The saturation question is the actual fork, and it costs ~$10 to settle.** Everything depends
on whether reward share SATURATES (if few rivals rest there, our share ≈ 1 regardless of size) or
is CONTESTED (share ∝ our contracts, so the optimum is always the cheapest rung — the shredder):

| regime | reward under a 15¢ floor | position cost | net/day |
|---|---|---|---|
| as run (no floor) | $7.48 | −$12.42 | **−$4.94** |
| **saturating** | $7.48 (just move strikes) | −$2.01 | **+$5.47** |
| **contested** | $1.80 | −$2.01 | **−$0.21** |

Exactly one branch is positive, and we have never measured which one we are in.

**THE $10 TEST — run this before deploying another dollar.** One reward period, same markets,
15¢ floor on `min(p,1−p)`, at **one fifth the size**. Compare the receipt to the $7.482 baseline:
- payout falls **<20%** ⇒ share SATURATES. The floor is free, capital is nearly irrelevant, the
  $928 we deployed was waste, and the business is **minimum-size breadth**.
- payout falls **~5×** ⇒ share is CONTESTED. Then reward-per-dollar is maximised at the cheapest
  rung by construction, the only way to earn is the way that returns −100%, and **the program is
  dead for us.**

Cost: ≤$8 of forgone reward and ~$2 of P&L. It discriminates the two branches directly, which the
credits total alone cannot do.

**Also new: filter on LADDER SHAPE, not pool size.** Only quote series whose strike distribution
actually populates the middle. On a barbell ladder there is no rung where making a market is a
business — cheap end has one fate, rich end is capital-heavy with a fat tail, middle doesn't exist.
Free to check, and if no series has a populated middle the program is dead regardless of anything else.

**Reframe worth holding:** this capital's job is not $200/day. It is to prove a positive,
labour-free per-dollar rate worth adding zeroes to. A verified +1%/day on $1,500 is worth more than
a lucky $200, because it is the only result that scales.

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
