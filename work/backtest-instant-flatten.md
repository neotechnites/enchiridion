# Backtest — HOLD vs INSTANT-FLATTEN on every LIP maker fill

**Question (Ryan).** For every real fill our LIP makers took since Monday evening, compare
**(A) HOLD** — what actually happened, position rides to settlement or is shed/netted — against
**(B) INSTANT-FLATTEN** — cross to the best opposing level the moment the fill printed, paying the
taker fee `0.07·P·(1−P)` per contract on the exit.

**Window:** 2026-07-27T17:00:00Z → 2026-07-28T15:43:18Z (22.3 h wall). First LIP-venue fill of the
Monday session to the last fill on the tape at read time.
**Scope:** 217 fills, 2,782.4 contracts, **$889.65 of cost basis**, across 46 markets in 5 venue classes.
nestor's own strategies (streak BTC/ETH, metals, orphan APRPOTUS — 12 fills) are **excluded**; this is the
LIP book only.

Money-claims doctrine binds throughout. Every figure below is reconstructed from a primary record.
Figures that the tape cannot answer are labelled **UNVERIFIED** and are never mixed into a total.

---

## §0. The measurement that made this possible — audit §5 is now closed

The 2026-07-28 audit recorded, correctly at the time:

> *Spread paid on fills (drift) — **UNVERIFIED** — not computable — no historical top-of-book snapshot
> per fill timestamp is retained.*

That is true of the on-disk captures. `~/nestor/data/lip_books.jsonl` holds full ladders for **4 gas rungs
only** (374 samples, 18:36Z Mon → 03:58Z Tue); `~/nestor/data/lip/ws_raw_frames.jsonl` holds **95 frames
spanning 126 seconds**; the 132 MB `v4_ledger.jsonl` carries no book ladders at all (its `snapshot` rows
carry derived `scores`, not depth). None of that covers 217 fills.

**The exchange retains it.** `GET /series/{series}/markets/{ticker}/candlesticks?period_interval=1`
returns per-minute `yes_bid` and `yes_ask` OHLC for the full history of every market we touched.
20,877 one-minute candles were pulled across the 46 tickers. **This is a primary exchange record, not a
model.** Audit §5's UNVERIFIED line is retired: spread-at-fill is now computable, and it is computed below.

### Staleness and accuracy of the book estimate — measured, not assumed

| check | result |
|---|---|
| Book staleness vs. fill timestamp | **median 14 s, p90 28 s, max 30 s** (n=217) — a fill is priced off the 1-minute candle containing it |
| Candle L1 vs. `lip_books.jsonl` raw ladder best-bid, where both exist | **median \|diff\| 1.00¢**, p90 2.00¢, max 11.00¢ (n=42) |
| Depth: size walked past L1 | **10 of 42** measurable fills; worst case 4.41¢ below L1 |
| Depth: opposing ladder could not absorb the size at all | **0 of 42** |
| Measured depth-walk penalty vs. the L1 estimate | **−0.34 ¢/contract (−0.9%)** |

The 1¢ median cross-check is the tick size and is consistent with 14 s of staleness. The depth penalty of
0.34 ¢/contract is **applied to every class below** as a uniform correction (−$9.46 total), which makes
policy B slightly *more* expensive than the naive L1 read. That correction is measured on gas rungs and
**extrapolated** to UST/DXY/mention/index — flag it as the one modelled quantity in the B column.

---

## §1. How each policy is priced

**Entry.** Per the audit's Fact 1, every fill in this window is an *acquisition*: `(buy,yes)` acquires YES
at `yes_price`, `(sell,no)` acquires NO at `no_price`. (The 11 `(buy,no)`/`(sell,yes)` rows on the tape are
all June, all in nestor's own markets, all outside this window — Fact 1 holds here without exception.)

**Policy B exit.** To flatten a YES you buy NO, and the exchange nets the pair, releasing $1.00. Net
proceeds are therefore exactly `yes_bid` at fill time. Symmetrically, flattening a NO returns
`no_bid = 1 − yes_ask`. Fee `0.07·P·(1−P)·q` on the exit leg; the formula is symmetric in `P ↔ 1−P` so the
side convention does not matter. Entry fees are **common to both policies** and are excluded from the delta.

**Policy A outcome — FIFO netting, not mark-to-bid.** A first pass valued every contract at settlement or
current bid and was **wrong**: it produced $27.66 on the DXY rung against the exchange's $28.59. The reason
is that a fully-netted position has *already realized* $1.00 per matched pair as cash — it is not an open
position to be marked. Policy A is therefore built as an explicit FIFO netting simulation: when a new
acquisition offsets an older opposing lot, the **older lot** closes at `1 − (price of the closing fill)`
and the **closing slice** returns its own cost at zero hold time. Residual naked lots are valued at
settlement payoff (if settled) or at the current bid (if open).

### Validation of policy A — the load-bearing test

> **Model policy-A P&L reproduces the exchange's own `realized_pnl_dollars` (plus mark-to-bid on residual
> exposure) on 30 of 30 markets. Mismatches: 0. Aggregate gap: $0.0000.**

Policy A is not an estimate. It is the tape.

**Policy B feasibility.** 7 of 217 fills are **infeasible under B** — the opposing side of the book was
empty at fill time, so there was nothing to cross to. They are excluded from both columns, listed in §5.

---

## §2. Reward rate — derived, and it is not one number

Ryan's brief quoted ~0.2 %/hr. The only **VERIFIED** reward receipt in existence is the audit's
**$7.482**, credited 2026-07-28T05:46:16Z for KXAAAGASD-26JUL28 (17 rung programs). Everything else
($65.53 of modelled accrual) is v4 model output with a documented 4–8× optimism history — **UNVERIFIED,
and not used here.**

Dividing that one receipt by three different denominators, all computed from the FIFO capital track:

| denominator | $-hours | implied r | reading |
|---|---|---|---|
| Capital on the rewarded gas ladder only, to program close 03:59Z | 279 | **2.68 %/hr** | what the winning rung actually paid per dollar-hour |
| All LIP position-capital-hours, whole 22.3 h window | 2,079 | **0.36 %/hr** | all verified rewards ÷ all capital deployed — **the primary rate used below** |
| Whole open book ($268.29 basis, audit §4) over a day | ~6,400 | ~0.12 %/hr | the fully-blended floor |

**I use r = 0.36 %/hr as primary**, because freed capital does not get to choose to land in the one rung
that paid — it re-enters the general quoting pool, most of which paid nothing this window. Ryan's
**0.20 %/hr** and the optimistic **2.68 %/hr** are both carried as sensitivities in every crossover column.

**Caveat, stated plainly:** if the $65.53 of modelled accrual ever pays anything near model, r rises by up
to ~9× and the arithmetic below moves materially. It has not paid. The next `paid_out` flip is the test.

---

## §3. The table

Dollars are proceeds, not P&L, except where labelled. `spread` = `Σ q·(entry − exit)`, the cost of crossing.
`depth $` = the 0.34 ¢/contract correction. `A hold P&L` = what riding the position earned vs. its own basis.
`H*` = **crossover horizon**: hours a position must be held before the freed capital pays for the round trip
(policy-neutral form, `round-trip cost ÷ (r × capital freed)`).

| venue class | fills | B infeas | contracts | basis $ | **A: hold** $ | **B: flatten** $ | **B − A** $ | spread $ | taker fee $ | depth $ | A hold P&L $ | med hold h | freed $-hr | reward @0.36% | **net B − A** | H* @0.36% | H* @0.20% | H* @2.68% |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| GAS dailies | 127 | 6 | 1492.6 | 467.48 | 496.24 | 405.19 | **−91.05** | 42.58 | 14.63 | 5.07 | +28.76 | 0.96 | 702 | +2.53 | **−88.52** | 42.7 h | 76.9 h | 5.7 h |
| UST dailies | 56 | 1 | 670.8 | 257.79 | 199.30 | 213.66 | **+14.35** | 35.69 | 6.16 | 2.28 | −58.49 | 1.58 | 613 | +2.21 | **+16.56** | 57.4 h | 103.3 h | 7.7 h |
| DXY | 18 | 0 | 231.0 | 84.13 | 117.71 | 69.77 | **−47.94** | 10.52 | 3.04 | 0.79 | +33.58 | 0.15 | 71 | +0.26 | **−47.68** | 57.1 h | 102.9 h | 7.7 h |
| Mention markets | 14 | 0 | 194.0 | 60.31 | 49.58 | 49.60 | **+0.02** | 7.75 | 2.30 | 0.66 | −10.73 | 2.35 | 245 | +0.88 | **+0.90** | 60.0 h | 108.0 h | 8.1 h |
| Index hourlies | 2 | 0 | 194.0 | 19.95 | 0.00 | 1.15 | **+1.15** | 18.01 | 0.13 | 0.66 | −19.95 | 0.20 | 0 | +0.00 | **+1.15** | 4558 h | 8205 h | 612 h |
| **TOTAL** | **217** | **7** | **2782.4** | **889.65** | **862.83** | **739.36** | **−123.47** | **114.56** | **26.27** | **9.46** | **−26.82** | **1.01** | **1631** | **+5.87** | **−117.60** | **56.5 h** | **101.6 h** | **7.6 h** |
| *strict "Monday evening" (≥00:00Z)* | 160 | 6 | 2296.5 | 665.12 | 638.26 | 535.59 | −102.67 | 101.35 | 20.37 | 7.81 | −26.86 | 1.39 | 1362 | +4.90 | −97.76 | 67.2 h | 120.9 h | 9.0 h |
| *held > 6 min only (drops pure closing slices)* | 166 | 7 | 2233.7 | 663.13 | 635.74 | 534.53 | −101.21 | 100.37 | 20.63 | 7.59 | −27.39 | 1.52 | 1629 | +5.86 | −95.35 | 66.8 h | 120.3 h | 9.0 h |

### Round-trip cost per contract

| class | spread | taker fee | depth | **total** | median contract price |
|---|---|---|---|---|---|
| GAS dailies | 2.85¢ | 0.98¢ | 0.34¢ | **4.17¢** | $0.40 |
| UST dailies | 5.32¢ | 0.92¢ | 0.34¢ | **6.58¢** | $0.56 |
| DXY | 4.56¢ | 1.32¢ | 0.34¢ | **6.21¢** | $0.42 |
| Mention markets | 3.99¢ | 1.19¢ | 0.34¢ | **5.52¢** | $0.39 |
| Index hourlies | 9.28¢ | 0.07¢ | 0.34¢ | **9.69¢** | $0.10 |
| **ALL** | **4.12¢** | **0.94¢** | **0.34¢** | **5.40¢** | **$0.42** |

**5.40 ¢ to round-trip a contract whose median price is 42 ¢ — a 12.9% haircut on notional, per flatten.**
The spread is 4.4× the fee. Instant-flatten is a spread problem, not a fee problem.

---

## §4. Verdict on Ryan's hypothesis

> *"Net EV ≈ −(spread+fees), compensated by freed capital."*

### First half: **CONFIRMED**, and it is an exact identity, not an approximation

```
B − A  =  −(spread + fee + depth)  −  (A's hold P&L)
−123.47 =        −150.29           −      (−26.82)
```

The round-trip cost is **−$150.29** and it is the dominant term. The realized delta came in $26.82 *better*
than that only because holding the inventory was itself mildly unprofitable this window (−$26.82 against
basis). That residual is the directional coinflip — it has no sign you can rely on. **Strip it and Ryan's
formulation is exactly right: instant-flatten costs the spread plus the fee, $150.29, 5.40 ¢/contract.**

### Second half: **REFUTED at every measured rate**

| freed-capital valuation | credit | net B − A | fraction of the $150.29 cost recovered |
|---|---|---|---|
| Ryan's 0.20 %/hr | **$3.26** | −$120.21 | **2.2 %** |
| Measured blended 0.36 %/hr (primary) | **$5.87** | **−$117.60** | **3.9 %** |
| Optimistic targeted 2.68 %/hr | $43.70 | −$79.77 | 29.1 % |

Policy B frees **1,631 dollar-hours** of capital across the whole book. At the only reward rate the tape can
actually verify, that is worth **$5.87** against a **$150.29** bill. The compensation mechanism is real but
it is **an order of magnitude too small** — it covers about 4 cents on the dollar. Even the deliberately
optimistic rate, which assumes every freed dollar lands in the single rung that paid out, recovers under a
third.

**The reason is a rate mismatch, and it is structural.** Round-tripping costs ~12.9% of notional
*instantly*. Earning it back at 0.36 %/hr takes ~36 hours of redeployment. Nothing about the reward
programme moves that fast.

### Where B does win, and why it is not an argument for B

Three classes show positive B−A: **UST +$14.35**, index hourlies **+$1.15**, mention markets **+$0.02**.
In all three, the reason is identical and visible in the `A hold P&L` column — those positions *bled*
(UST −$58.49, index −$19.95, mention −$10.73). Flattening would have avoided losses that holding incurred.
That is **hindsight on this window's direction**, not an edge of the policy: the same column shows gas
+$28.76 and DXY +$33.58, where holding won and flattening would have destroyed $139 of realized profit.
Net across all five, holding beat flattening by $123.47. **B is a bet against your own inventory, priced at
5.4 ¢/contract to place.**

The index-hourly row is worth naming separately: 194 contracts bought at ~10 ¢ against a ~1 ¢ bid — a 9.28 ¢
spread, 93% of notional. Policy B recovers **$1.15 on $19.95 of basis**. That is not a flatten, it is a
donation. It is also the R176 penny-farming exposure showing up as a liquidation cost: **quoting into a book
with no opposing side means you cannot flatten at any price.** The correct lesson from that row is about
entry, not exit.

---

## §5. Crossover horizons — the decisive number

`H*` is the hold time above which freed capital pays for the round trip. Set against how long these
instruments actually exist:

| venue class | H* @0.20%/hr | H* @0.36%/hr | H* @2.68%/hr | actual median hold | instrument lifetime | verdict |
|---|---|---|---|---|---|---|
| GAS dailies | 76.9 h | **42.7 h** | 5.7 h | 0.96 h | ~24 h (closes 03:59Z) | **B never wins** — H* > market life |
| UST dailies | 103.3 h | **57.4 h** | 7.7 h | 1.58 h | ~24 h (closes 19:30Z) | **B never wins** |
| DXY | 102.9 h | **57.1 h** | 7.7 h | 0.15 h | ~24 h (closes 17:58Z) | **B never wins** |
| Mention markets | 108.0 h | **60.0 h** | 8.1 h | 2.35 h | multi-day | B wins only past ~2.5 days |
| Index hourlies | 8205 h | **4558 h** | 612 h | 0.20 h | **1 h** | **B never wins** — by 4 orders of magnitude |
| **TOTAL** | **101.6 h** | **56.5 h** | 7.6 h | **1.01 h** (wavg 2.21 h) | — | **B never wins** |

This is the finding that settles it. **In four of five venue classes the crossover horizon exceeds the
entire lifetime of the instrument.** A gas daily needs to be held 42.7 hours for instant-flatten to break
even, and the market ceases to exist after ~24. You cannot reach the crossover; the contract settles first.

Even at Ryan's own 0.2 %/hr the total-book crossover is **101.6 hours** against a **1.01 hour** median
actual hold — a **100× gap**. The policy would have to be wrong by two orders of magnitude in the
freed-capital rate for the conclusion to flip.

---

## §6. Policy B infeasible — the 7 fills with no opposing side

| time (Z) | ticker | side | size | reason |
|---|---|---|---|---|
| 07-27 17:39:17 | KXAAAGASD-26JUL28-4.085 | NO | 10.00 | yes_ask = 1.00, NO bid empty |
| 07-28 00:09:18 | KXAAAGASD-26JUL28-4.075 | NO | 10.00 | yes_ask = 1.00 |
| 07-28 00:09:24 | KXAAAGASD-26JUL28-4.080 | NO | 10.00 | yes_ask = 1.00 |
| 07-28 01:43:23 | KXAAAGASD-26JUL28-4.085 | NO | 10.00 | yes_ask = 1.00 |
| 07-28 02:27:30 | KXAAAGASD-26JUL28-4.090 | NO | 10.00 | yes_ask = 1.00 |
| 07-28 13:31:21 | KXAAAGASD-26JUL29-4.065 | NO | **999.00** | yes_ask = 1.00 |
| 07-28 14:13:19 | KXUST5AD-26JUL28-T4.48 | YES | 100.00 | yes_bid = 0.00, YES bid empty |

All 7 are deep out-of-the-money rungs where the book is one-sided. The 999-contract gas 26JUL29 fill is the
largest single position on the tape and **policy B could not have exited it at any price.** This matters
beyond the arithmetic: instant-flatten is not merely expensive at the tails, it is **unavailable exactly
where inventory risk is largest.** A policy that cannot be executed on your biggest position is not a risk
control.

---

## §7. Limitations — what this backtest does not establish

| item | status | why |
|---|---|---|
| Depth beyond L1 for UST / DXY / mention / index | **UNVERIFIED**, modelled | measured at −0.34 ¢/contract on gas ladders and extrapolated; candlesticks carry price, not size |
| Path dependence | **acknowledged limitation** | 60 of 217 fills were themselves closing/netting trades under A. Under B those positions were already flat, so the subsequent fill sequence would have differed. The comparison is applied mechanically per fill as briefed; the `held > 6 min` row (which drops pure closing slices) moves the total only from −$123.47 to −$101.21, so path dependence does not change the sign or the order of magnitude |
| Accrued-unpaid rewards ($65.53 modelled) | **UNVERIFIED**, excluded | v4 model output, 4–8× optimism history; if it pays near model, r rises up to ~9× and H* falls proportionally — still above actual hold times in every class |
| Sub-minute book movement | not captured | candles are 1-minute; staleness median 14 s, max 30 s |
| Open positions | marked at **bid**, not mid | the honest liquidation number, consistent with audit §4 |

---

## §8. Headline

1. **217 LIP fills, $889.65 basis, 22.3 hours.** Policy A (hold) returned **$862.83**. Policy B
   (instant-flatten) would have returned **$739.36**. **B − A = −$123.47.**
2. **Round-trip cost of B: $150.29** = $114.56 spread + $26.27 taker fee + $9.46 depth, or
   **5.40 ¢/contract on a 42 ¢ median contract — 12.9% of notional.** Spread is 4.4× the fee.
3. **Ryan's hypothesis is half right.** `net EV ≈ −(spread+fees)` is **confirmed as an exact identity**;
   the only deviation is the held inventory's own directional P&L (−$26.82 this window), which is variance.
4. **The freed-capital compensation is refuted.** 1,631 freed dollar-hours are worth **$5.87** at the
   measured blended rate (**$3.26** at Ryan's 0.2 %/hr) against a $150.29 cost — **~4% of it.**
5. **Crossover: 56.5 hours** at the measured rate, **101.6 hours** at 0.2 %/hr, against an actual median
   hold of **1.01 hours**. **In four of five venue classes H* exceeds the instrument's entire lifetime** —
   the crossover is unreachable, not merely distant.
6. **Policy B is infeasible on 7 fills**, including the single largest position on the tape (999 contracts).
7. **Audit §5's UNVERIFIED spread line is now closed** by the exchange's per-minute candlestick history —
   a primary record, cross-checked to 1 ¢ median against the raw ladder captures. Recommend the LIP maker
   record top-of-book at fill time going forward so this never needs reconstructing again.

**Verdict: do not adopt instant-flatten.** It costs 12.9% of notional to buy protection against inventory
that, over this window, was worth holding. The freed capital does not come close to paying for it, and in
the venues where the makers actually operate the break-even horizon is longer than the contracts live.

---

*Analyst: Fable. Read-only throughout — nothing written to the VPS beyond `/tmp` scratch scripts, no orders
placed, no state modified. Primary sources: `/portfolio/fills` (260 fills, 217 in scope),
`/portfolio/settlements`, `/portfolio/positions`, `/series/{s}/markets/{t}/candlesticks` (20,877 one-minute
candles), `~/nestor/data/lip_books.jsonl`, `~/nestor/data/lip/v4_ledger.jsonl`, and
`work/audit-2026-07-28.md`. Policy A validated against exchange realized P&L on 30/30 markets, gap $0.0000.*
