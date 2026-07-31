# 47 - THE LIP PROBLEM — everything known, 2026-07-29 ~6:30am MT

> The measured foundation of the LIP lane. Read order: [[56]] → [[55]] → [[54]] → this;
> concepts [[43 - THE MONEY GAME]] and [[45 - CONTACT]] first if unread. (Old refs to notes
> 23/07 point to git history; their load-bearing rules are inlined in [[49]] and `briefs/`.)
> Everything below is measured unless marked UNVERIFIED. Do not re-derive it; argue with it.
> **§§1–6, 8, 10 (measurements) STAND. §7 and §9 are HISTORICAL** — that v5 was rebuilt into
> the law machine ([[54]]/[[55]]); read them as the defect record, not the present.

---

## 1. THE CENTRAL FACT — the trap, confirmed from both sides

**The rungs that earn the most reward are the same rungs that lose the most capital.**

Ryan's direct observation, 2026-07-29, treated as authoritative: **the two best-paying gas rungs
were LONGSHOTS, worth $15+ of credits between them.** The largest single credits on 2026-07-28
were $8.69, $7.69 and $5.31 — all gas, all at the cheap end of the ladder.

And the same ladder's cheap end is where the money died: gas 4.110 **−$32.70 (−81%)**, 4.115
**−$11.15 (−100%)**, 4.120 **−$1.00 (−100%)**.

This is [[43]] §7's two-denominator hazard in its purest observed form: **scoring is denominated
in CONTRACT COUNT, capital is denominated in DOLLARS, and count is cheapest exactly where
contracts are worthless.** Any honest optimisation of reward capture walks the capital into the
fire. Every design this program has produced — v3, v4, v5 — did precisely that, and so did five
independent fresh agents asked to design one.

**Do not "fix" this by simply banning cheap rungs.** That deletes the income line. It is the
central tension and the fix must price both sides, not amputate one.

---

## 1b. THE RUNG ATTRIBUTION — what the $71.34 actually bought (Appendix I)

**Net of position P&L, by acquisition price, across the six credited events:**

| bucket | capital | position P&L | credits | **net** |
|---|---|---|---|---|
| **≤5¢** | $62.70 | −$40.63 (−64.8%) | **+$44.51** | **+$3.88** |
| 6–20¢ | $95.54 | −$82.13 | +$17.18 | **−$64.95** |
| 21–50¢ | $51.66 | −$9.10 | +$1.87 | −$7.23 |
| 51–80¢ | $164.95 | −$70.79 | +$3.28 | **−$67.51** |
| **>80¢** | $73.52 | **+$7.45** | +$4.50 | **+$11.95** |
| total | $448.37 | −$195.20 | +$71.34 | **−$123.86** |

**ONLY THE TWO ENDS ARE NET POSITIVE. The middle of the book destroyed $132**, and 51–80¢ was
the day's single largest capital sink ($165 deployed, $3.28 collected). **Every price-floor
design this program has produced would have moved capital INTO the losing zone.** ≤5¢ is the
worst bucket per contract-second (1.50 $/M) but the **best per dollar-hour by 15×** (0.243 vs
0.0165) — reward is scored in contracts, risk is priced in dollars.

### ⚠ WHAT IS MEASURED vs WHAT IS INFERRED — read before acting on the table above

**Assumption-free (defend these):**
- **57% FORFEITURE.** 24 line items against **56 rungs rested** ⇒ **32 rungs earned under $1.00
  and got nothing**, burning 2.46M contract-seconds and **167.4 dollar-hours for zero**.
  (Including UST2AD/30AD: 49 of 73 rungs, 67%.) This is arithmetic.
- **SCORE SATURATES, ~√(size × time).** Our biggest gas rung held **59.8%** of that event's
  contract-seconds; the biggest of 12 gas credits was **22.4%**. No assignment can reconcile
  those, so score is NOT linear in size. Shape fit: MAD 1.9pp for √ vs 6.3pp for linear.
  **Doubling size does not double income. Breadth beats depth — settled.**
- **The $1.00 floor is visible in the receipt** (smallest line item is exactly $1.00).
- **The credit day is the ET day keyed to each program's END**, not the calendar. Explains why
  KXAAAGASD-26JUL28 is absent (closed 23:59 ET Jul 27) and why UST2AD/30AD are absent (every
  rung under $1.00).

**INFERRED, with wide bounds (do NOT size on these):**
- The amount→rung mapping is a **√(size×time) shape fit**, not a measurement. Credits are labelled
  **by EVENT only**.
- ≤5¢ share of credits: central **62%**, bounds **11%–76%**. In the worst-case assignment the
  ≤5¢ bucket flips from **+$3.88 to −$32.46**. Verdict is **CONDITIONAL, not a green light.**

## 1c. THE TWO LEVERS THAT DWARF PRICE

1. **VENUE DISPERSION IS 250×; PRICE DISPERSION IS 5×.** Choosing gas over mentions cost more
   than any rung choice *inside* gas could recover. **Venue selection is the first-order decision
   and we have never optimised it.**
2. **STOP PAYING THE FLOOR TAX.** 32 rungs funded below the $1.00 threshold earned exactly
   nothing while carrying full inventory risk. Sizing to clear the floor, or not funding the rung
   at all, is worth more than any price band.

**And the mention ban is wrong on two axes.** The $16 loss was
`KXEARNINGSMENTIONPYPL-26JUL28-PERP, 81 YES at 19.9¢` — an **earnings** mention at **20¢**, not a
sports mention and not cheap. All $31.31 of mention losses are `KXEARNINGSMENTION*`. The CLECIN
**sports** rungs that earned $6.76 took **ZERO fills, held ZERO position**, at **$6.96 per
dollar-hour — 80× gas.** Deny `KXEARNINGSMENTION*`; permit `KX*MENTION` sports at ≤5¢ with a
fill-rate >5% kill trigger.

## 2. THE MONEY — 2026-07-28, the only full day with both halves measured

| | amount |
|---|---|
| **LIP credits paid** (24 line items, Credits page) | **+$71.34** |
| Settled position P&L | ≈ **−$51** |
| Gas 26JUL29 book, closed and pending settlement | **−$39.32** |
| **NET** | **≈ −$19** |

**By family (position P&L):**

| family | P&L | note |
|---|---|---|
| **Treasury (5Y/7Y/10Y)** | **−$106.17** | the disaster: −60.27 / −36.67 / −9.23 |
| Gas 26JUL28 (settled) | **+$53.81** (+156%) | the winner |
| Gas 26JUL29 (pending) | −$39.32 | damage all at 4.105–4.120 |
| DXY | +$27.86 | one lucky trade, n=1 — see §5 |
| Index hourlies (NDQ/INX) | −$19.95 | 100% losses both |
| PayPal earnings mentions | −$12.87 | the $16 lesson |
| Metals / copper / BTC / misc | ≈ +$8 | |

**Credits by event:** gas 12 items $38.80 · UST7 5 items $12.38 · UST10 3 items $8.29 ·
**MLB mention 1 item $6.76** · UST5 2 items $2.91 · TRUEV 1 item $2.20.

**CRITICAL CORRECTION to earlier work:** a $7.482 receipt was long treated as the daily rate. It
was one event, not a day. The real rate is ~**10× that**. Every conclusion in
`work/audit-nonlip-strategies-2026-07-28.md` Appendices A–H that used $7.48/day as the income
line is **understated by an order of magnitude** and must be recomputed before it is trusted.

**Half the loss is NOT longshots.** The single biggest losing position was
`10Y 4.62% or above — 98 contracts, cost $53.73, worth $7, −$55.29`, i.e. **~55¢/contract**.
A price floor would not have touched it. That was ordinary directional loss on a normally-priced
position, held to settlement. Roughly half the damage is this, half is the −100% cheap cohort.

---

## 3. CALIBRATION — cheap contracts are overpriced ~8× (n = 8,240 settled markets)

Data: `~/calib2.json` on the VPS. One observation per market, real two-sided quotes at close−60min.

| price | n | posted | **realised** | EV per $1 |
|---|---|---|---|---|
| 1¢ | 3,205 | 0.60% | **0.03%** | **−94.8%** |
| 2¢ | 765 | 1.57% | **0.00%** (0 of 765) | **−100%** |
| 3–5¢ | 333 | 3.39% | 1.20% | **−64.6%** |
| 6–20¢ | 591 | — | — | −13 to −19% (n.s.) |
| 41–60¢ | 345 | 50.4% | **57.1%** | **+13.3%** |
| 61–80¢ | 707 | 71.7% | **84.9%** | **+18.4%** |
| 81–99¢ | 1,852 | — | — | +2.1 to +5.6% |

Consequences:
- Our 15-of-15 wipeout was **~98% likely**, not variance. It was the price of the trade.
- **On Kalshi a resting cheap BID IS A BUY.** We were not selling lottery tickets; we were holding
  them, on the same side as the retail flow that overpays for them.
- **Mentions are FAIRLY priced at the cheap end** (1.55% realised vs 1.69% posted). Gas, metals
  and index hourlies — the venues we farmed — are the overpriced ones.
- ⚠ **The 41–80¢ "edge" is NOT actionable.** Note 07 (git history) records that systematically
  buying 80–95¢ favourites was already tested and **collapsed out-of-period**.
  It is the mechanism explaining the cheap side's losses, not a trade. Needs a fresh out-of-period
  test before a dollar moves.
- ⚠ **CORRECTION (2026-07-30, the g-table — same calib2, side-split + PAVA):** the gap does
  NOT close by ~15¢. 10–28¢ shows **~35% bleed per $ filled (7σ)** and the gap persists to
  ~50¢; toxicity is **SIDE-split** (cheap NO ≈ 0.5–0.58 loss per $ filled, cheap YES far
  less — the funded-book natural average is ~19.7¢). The "6–20¢ n.s." row above was
  underpowered, not clean. The machine prices this via `bleed.py`'s g(side, price-band);
  never size from this table alone.

---

## 4. STRUCTURE OF THE VENUE (measured, `~/compmap.json`, `~/progs.json`)

- **Ladders are BARBELLS.** Our presence: 76% below 20¢, **0.3% between 20–80¢**, 23% above 80¢.
  Strikes sit deep-OTM or deep-ITM; almost nothing near 50¢. **A 20–80¢ band does not filter a
  ladder — it deletes it.**
- **Competition is U-shaped:** median competing score **6,618 at 1¢**, **403 at 11–20¢**,
  **2,877 at 96–99¢**. Everyone runs the same score-per-dollar arithmetic and crowds the extremes.
  **11–20¢ is where nobody is standing** and where clearing the $1 floor is cheapest ($3.68 median
  vs $6.36 at 1–5¢ and $244 at 96–99¢).
- **317 rungs at 7–12% cost ≤$20 to clear $1, across 177 independent clusters.**
- **Breadth:** 2,983 live programs, **358 independent settle sources** — but **all 75 dailies are
  treasury rungs, i.e. ONE source.** A dailies-only rule concentrates rather than diversifies.
- **81–99¢ is the worst reward real estate on the board** (0.16–0.95%/day) and held **95.3% of our
  capital** on the paying gas ladder.
- Two independent projections (22%/day at $1k; $851/window on $300) contradict receipts by 10–20×.
  **Treat any modelled accrual as 4–8× hot until it is a receipt.**

---

## 5. THE SIZING ALGEBRA (verified in closed form — do not re-derive)

Contracts needed to clear the $1 floor: `Q = rival_score / (P − 1)` — **contains no price term.**
Therefore cost per rung `= Q·p`, rungs affordable `N = B/(Q·p)`, and:

> **E[hits] = N·p = B/Q. THE PRICE CANCELS.** P(zero hits) = exp(−B/Q).

**The probability band is irrelevant to survivability.** It matters only through the *bias*
(§3) and through how many **independent settle sources** you can spread across. Survivability is
governed by independence, not by price. `p_min = k/bankroll` is WRONG; do not rediscover it.

---

## 6. WHAT HAS BEEN TESTED AND REFUTED (all on our own tape — do not rebuild these)

| idea | verdict |
|---|---|
| **+2¢ resting sell leg on fill** | **−$40.30 vs doing nothing.** Nets ZERO contracts; fires before the opposing side can fill. Breakeven exit rate 94.4%, realised 87.4%. The most elegant idea produced; the worst. |
| **Box / joint ≤98¢ sum guard** | Near-decoration. Zero pairs exceeded $1.00 with or without it. The real cause of >$1.00 pairs was **ladders repricing independently** — one-rung-per-side already prevents it. |
| **20–50¢ two-sided band** | Geometrically impossible: on a binary the legs sum to 100−spread, so it constrains the MID, not the legs. Available **2.16%** of minutes. |
| **`p_min = k/bankroll`** | The price cancels (§5). |
| **Instant-flatten on fill** | Refuted earlier: −$123 vs riding (`work/backtest-instant-flatten.md`). |
| **Free-ride-only rule** | Actively harmful — a covert instruction to quote the cheap crowded side. Only 144 of 536 mid-priced sides clear target_size, and those are the crowded ones. |
| **Deny by `MENTION` substring** | **WRONG AS WRITTEN.** It bans sports mentions (MLB earned **$6.76**, the 2nd-largest single credit, and mentions are fairly priced) along with EARNINGS mentions (PYPL/BA, measured toxic, −$12.87). Ban `EARNINGSMENTION`, not `MENTION`. |

---

## 7. WHAT v5 WAS AND WHAT WAS WRONG WITH IT (HISTORICAL — every item below was fixed or superseded by the law builds, [[54]]/[[55]])

**Repo:** `/Users/ryanwhitehead/Documents/senate/nestor-wt-lipv5`, branch `lip-v5-build`,
package `tools/lip_v5/`. **601 tests green.** Deployed to VPS at `~/kalshi_data/v5/`, unit
`lip-v5.service`, armed by hand-written `~/nestor/data/lip/v5_go.json`.

**Deploy without cancelling the book:** `systemctl kill -s KILL lip-v5` then `start` — the
recovery sweep re-adopts resting orders. A normal `stop` runs cancel-all and destroys the book.
(I restarted ~12× on 2026-07-28 and wiped accrual each time. Don't.)

**What works:** the requoter reaches the wire; fills book in ~16s; never crosses the spread;
recovery/adoption/handback/rollback all correct; the cash feed keeps nestor unhalted; cluster
worst-case caps; the burst breaker (caught a real loop at 3 orders instead of 130).

**WHAT IS WRONG, in priority order:**

1. **`LAND_GRAB_PRICE_C = 1`.** Still live. Posts at the precise geometry measured at −94.8%/−100%.
   A staged `ACQUIRE_FLOOR_C = 6` exists in `config.py` and a `min(p,1−p)` filter is written into
   `scan.build_slots` **but was never deployed** — verify state before touching.
2. **Sizing is INVERTED.** `n_cap = floor(slot_cap/price)` buys MORE contracts as price falls —
   it deliberately buys the most of the least likely thing. This is the trap expressed in code.
3. **No fate derivation.** The fate doctrine (briefs/implementor.md; note 23 in git history) requires a FATE SENTENCE before an acquiring system
   ships. v5's is committed as **UNDERIVED** and still blank. It acquires today anyway.
4. **Directional inventory accumulates unmanaged.** 71.9% of contracts are held to expiry;
   treasury gave us 8–9 rungs on one yield curve all pointing the same way and the curve moved
   (−$106). Only 28.1% of inventory ever nets. **This is half the loss and no price floor touches it.**
5. **Deployment throttle.** v5 deploys $5–30 of a $300 ceiling. Root causes fixed on 2026-07-28
   (venue cap sized for one rung; admission counting permissions as spend; cliff-pruned capital
   never re-allocated) but the last fix regressed and was reverted — **it is NOT solved.**
6. **Any floor must be `min(p, 1−p)`, never `p`.** A 98¢ YES *is* a 2¢ NO. Every band anyone has
   proposed was written on `p` alone and left the deep-ITM door open — where 95% of our capital sat.

---

## 8. THE OPEN QUESTIONS, in order of value

1. **Reward per rung by RUNG PRICE.** Credits are labelled **by EVENT, not by market** — there is
   no API for them (13 endpoints probed, all 404) and `recon.jsonl` has `paid_usd` NULL on all 446
   rows. **Fix the capture: tag every accrual row with `resting_price_c` and `resting_size`.**
   Until then, attribution is by count-matching line items to rungs we rested in.
2. **Does reward share SATURATE or is it CONTESTED?** If few rivals rest at a rung, share ≈ 1
   regardless of size and price barely matters for income. If share ∝ our contracts, the optimum
   is always the cheapest rung — the shredder. **~$10 test:** one reward period, same markets, at
   **⅕ size**. Payout falls <20% ⇒ saturating. Falls ~5× ⇒ contested.
3. **Fill probability on resting cheap bids** — converts "overpriced 8×" into what it costs *us*.
   Computable from `v4_ledger.jsonl` order lifetimes joined to fills. Not started.
4. **Can directional inventory be managed at all on this venue?** 0.3% of our presence was ever in
   the band where two-way flow might exist. **We have never tested market making — we tested
   longshot farming.**

---

## 9. STATE AS OF 2026-07-29 ~6:30am MT (HISTORICAL — current state lives in [[53]])

- **v5 LIVE**, armed, quoting today's gas window (opened 6:00am, closes 9:59pm MT). **No floor
  deployed.** It is currently free to buy 1¢ rungs.
- **nestor** live, unhalted, bankroll ~$92.74. Its breaker reads v5's cash feed; **when v5 stops,
  the feed goes stale and nestor halts ~2h later** — repair is an `external_cash.jsonl` entry.
- **Account** ~$412 cash. Gas 26JUL29 book pending settlement at −$39.32. Ryan has $1–2k available.
- **Datasets on VPS (do not re-pull):** `~/calib2.json` (8,240 calibration obs),
  `~/compmap.json` (2,983 live books), `~/progs.json` (121,151 programs),
  `/tmp/credits_jul28.json` (the 24 credit line items).
- Ledger of all analysis: `work/audit-nonlip-strategies-2026-07-28.md`, Appendices A–H.

## 10. THE STANDING WARNING

Every elegant idea this program has produced lost money on its own tape. The two that survived
measurement are boring: **don't buy what you can't sell**, and **don't let fifty tickets be one
draw**. Before shipping anything, write the fate sentence (briefs/implementor.md) and check the
denominator ([[43]] §7) — the objective is in contracts, the capital is in dollars, and every
failure in this program traces to optimising one while spending the other.
