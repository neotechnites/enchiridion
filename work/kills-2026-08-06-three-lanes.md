# Three lanes killed on data — 2026-08-06 (MT afternoon)

All three were run with the six guards the vault has paid for: executable prices only,
two-sided-book requirement, `yes_bid+no_bid<=100` staleness filter, event-clustered
t-stats, fixed-close anchoring, and no conditioning on the trade being possible.

## 1. DERIBIT CROSS-VENUE GATE — DEAD (and it was negative, not merely zero)
Source: `~/kalshi_data/deribit_gate_hourly.jsonl`, **forward-logged since 07-22** by
`scripts/deribit_gate_hourly.py` — pre-registered, so no backtest lookahead is possible.
28,881 resolved rows, **662 distinct events**, BTC+ETH hourly ladders. Rule as logged:
flag rungs where |Kalshi mid − Deribit-implied| − fee > 2c, fade the gap.

| cut | clustered EV/contract | t |
|---|---|---|
| ALL | **−0.54c** | **−4.21** |
| btc | −0.88c | −4.93 |
| eth | −0.20c | −1.09 |
| first half / second half (1c haircut) | −1.67c / −1.37c | −8.67 / −8.71 |

**Fading the professional options market's disagreement LOSES money, significantly, in
both halves.** 80% of rows sit at kmid<5c, where it is −1.03c with t=−47: the gate keeps
telling us to BUY the cheap wing because Deribit says it is underpriced, and reality says
Kalshi's 3c is still too dear. That is LIP STATEMENTS 9a replicating on an independent,
forward-collected universe. **Do not rebuild a cross-venue fair-value gate on this venue.**

## 2. SELLING THE CHEAP WINGS AT EXECUTABLE PRICES — DEAD (9a is inside the spread)
Source: `cwing_books_KX{BRENT,GOLD,SILVER,COPPER,NATGAS}D + KXWTIH.jsonl`, 3-min full-ladder
capture since 07-26. **703,544 two-sided snapshots, 5,413 rungs, 5,007 settled (92%).**
Trade tested: **sell YES by hitting the best YES bid** (immediately executable, no fill
model, no phantom quotes), hold to settlement, net of the real Kalshi taker fee.

At lead>=0.5h (178 events) and lead>=1h (69 events) — the well-populated samples — **every
band is noise**: clustered t between −2.37 and +0.94, signs flipping between leads.
The 1-3c band prices 1.25% and realizes 1.01%: the mispricing is REAL and it is 0.24c wide,
against a 1-4c spread. **9a's "harvestable mirror" is confirmed to live entirely inside the
spread. It is not takeable, at any lead, in any commodity family.**

**TRAP RECORDED:** the lead>=2h and lead>=4h rows show +1.1c to +8.5c at **t=+34 to +44**
with realized rates of **exactly 0.00%** across every band including 12-20c. That is the
note-43 §5b leak signature — a cell that never loses. It is sample collapse: only ~25 events
survive the lead filter, all deep wings of long-dated dailies on quiet days. **Anyone who
reads those rows as an edge will fund a −100% residue book.**

## 3. QUEUE PROTECTION — REAL BUT WEAK (1.6x, not the free lunch it looks like)
Hypothesis worth testing: rest at the touch of a CROWDED tick-floor book — full score
(scoring is per contract at the best price, queue-position-blind) but no fills, because
20-28k contracts sit ahead of us. Measured over **698,506 ticker-hours** of the same capture:

| depth at touch | P(best bid drops below our level within 1h) |
|---|---|
| 0-50 ct | 28.6% |
| 200-1,000 ct | 26.8% |
| 1,000-5,000 ct | 20.2% |
| 5,000+ ct | **18.0%** (n=1,298) |

Depth cuts level-loss from 29% to 18% per hour. **Directionally right, magnitude far too
small to build on** — and measured on fast-underlying commodity books, so it does not
transfer to the slow families without re-measurement.

**Method note (my own error, recorded so it is not repeated):** the first version of this
test asked "P(level fully consumed while the price is unchanged)" and returned 0.0% in every
bucket on n=295,491. It conditioned away the event it was measuring — a level that clears
IS a price move. Censored sample, same defect class as the settled-cohort problem.

## WHAT THE THREE KILLS ADD UP TO
Every edge measured today is 0.2-3c gross against a 1-4c spread. **The venue is efficient to
within the spread**, so the only way to be paid here is to BE the spread — and the only
mechanism that pays for that without requiring a prediction is LIP.

Which puts everything on one unresolved fact: **does resting collateral actually get PAID,
and in which families?** Every estimate we hold is unreliable — K_eff varies 0.20-0.56 per
market, and LIP STATEMENTS §40 is ratified that $34.01 of accrual paid $0.00. Right now
$27.49 of $113.51 of live accrual (146 of 184 programs) sits under the $1 floor.
**No one has ever reconciled a Kalshi credits statement against `est_history.jsonl`.**
There is no credits endpoint in the API (seven paths probed, all 404) — it needs the
statement export from the web UI. Until that exists, the income side of this business is
unmeasured, and every allocation decision including "run at all" is being made blind.

## LIVE BOARD, measured today (context for that reconciliation)
$43,611/day of total pool active; $35,385/day of it in slow-underlying (non-continuously-
observable) families. Account captures **$25.43/day gross** — 0.06%. The self-consistent
reading of the books: seats that pay are already stuffed with 20-28k contracts at the 1c
tick floor; thin mid-priced seats are thin because the target-size gate means they pay
nobody. That reading is a HYPOTHESIS and the receipts are what test it.

---

# 4. THE GRID — one cell survived, and here is its honest size

Ran every (side x series x lead x price-band) cell over the same 703k-snapshot capture at
EXECUTABLE prices, event-clustered, net of real taker fees, spread capped at 10c.
**32 cells with >=8 events. 10 exceeded |t|>1.96 where 1.6 were expected by chance.**
Bonferroni 5% threshold for 32 cells = 3.16.

**Survivor: SELL YES at the bid, KXWTIH (WTI hourly), mid 1-5c, 15-45 min before close.
+1.48c/contract, t=+31.9, 123 events, 439 markets. Both date halves agree (+1.54c / +1.44c).**

## Why the t-stat is FAKE even though the edge may not be
**0 of 439 markets settled YES.** Every observation is nearly identical (+1.55c minus fee),
so the dispersion is ~0 and t explodes. **You cannot estimate a rare-event rate from zero
events.** t=+31.9 measures "the thing never happened in our sample", not significance.
The defensible statement uses the rule of three: 95% upper bound on true P(yes) = **0.683%**,
at which **EV = +0.87c/contract**. Positive under the pessimistic bound — that part is real.

## What it is worth at $991 — the arithmetic that decides it
Selling YES at 1.55c posts **98.45c of NO collateral per contract**. Return per cycle =
1.55/98.45 = **1.57% of deployed collateral**.
- $991 fully deployed = 1,007 contracts = **$14.90 per hourly cycle**.
- Capital is free again at settlement (<=1h), so ~8-12 cycles/day => **$120-180/day gross**.
- Book capacity is NOT the binding constraint: 513,674 contracts rested at the bid across
  the 11-day window (median 2,000/market), i.e. $691/day of gross if we could fund it.
  **Capital is the constraint, not liquidity.**

## Why fully deployed is RUIN, and what survivable size does to the number
One YES outcome while fully deployed = **-$991, the entire bank**. At the rule-of-three
p=0.683% per exposure and ~10 exposures/day, **P(ruin before Sept 1) ~ 83%**. Even at
p=0.2% it is ~40%. Kelly on p=0.683% says 56% of bank, but Kelly computed off ZERO observed
events is not a size, it is a guess; at a merely-pessimistic p=1.5% Kelly collapses to 3%.
**Survivable sizing is 3-10% of bank per cycle, which turns $120-180/day into $5-15/day.**

## THE THING THAT WOULD KILL IT, and it is not in the sample
11 days, one regime, zero realizations of the only event that matters. WTI wings do not fail
independently: an inventory print or an OPEC headline moves oil through **many strikes at
once**, so the tail is CORRELATED across the very positions we would hold simultaneously.
The sample cannot see this and no amount of re-slicing it will.
**Gate before any capital: (i) reconstruct WTI hourly wing outcomes across a shock window
(EIA Wednesdays, OPEC meetings) from the 2-year WTI history already on disk; (ii) measure
how many strikes go YES together on those days. If simultaneous hits are common, the
correlated tail eats years of 1.57%-per-cycle and the lane is dead.**

**Status: NOT FUNDABLE. It is the first thing in six weeks that survived an executable-price
grid with a multiple-testing correction, and its honest size at this bank is $5-15/day.**

---

# 5. THE SHOCK GATE — NOT ANSWERED, and the band curve is the better finding

`wing_obs_KXWTIH.jsonl` holds 1,492 rung outcomes but over only **5 dates**. Cheap wings
(<=5c): **157 observations, 4 dates, 0 YES**. The correlated-shock question is therefore
STILL OPEN — 4 days cannot contain an oil shock, so the tail remains unobserved and the
rule-of-three bound stands as the only defensible statement.

Weak counter-evidence against extreme clustering: in the 5-20c band, 11 YES hits fall across
**3 of 5 dates at 3-5% of each day's wings** rather than all on one day. n=3 days. Worth
nothing on its own; recorded so it is not re-derived.

## THE BAND CURVE — a coherent bias, not a lucky cell
| YES price band | n | YES | realized | sell-YES EV |
|---|---|---|---|---|
| 0-3c | 63 | 0 | 0.00% | **+1.52c** |
| 3-5c | 67 | 0 | 0.00% | **+3.37c** |
| 5-10c | 99 | 1 | 1.01% | **+5.79c** |
| 10-20c | 160 | 10 | 6.25% | **+8.33c** |
| 20-40c | 146 | 31 | 21.23% | **+6.33c** |

And from the executable grid, the mirror: **sell-YES is NEGATIVE at 60-80c (-7.5c) and
80-95c (-6.0c)** — i.e. the favorite side is UNDER-priced by about as much as the longshot
side is over-priced. **That is textbook favorite-longshot bias**, it is symmetric, both
halves confirm each other, and it has a known cause (retail lottery preference — the same
mechanism Bartlett & O'Hara measured across 41.6M Kalshi trades).

**This reframes finding 4.** The 1-5c cell is the WORST place to express it: zero observed
tail events, so its t-stat is empty and its size is capped at 1.57% of collateral per cycle.
**The 10-20c band is strictly better**: the tail is actually OBSERVED (10 YES events), and
selling at ~14.6c against 85.4c of collateral returns **9.75% per cycle** — 6x the capital
efficiency, with a tail you can estimate instead of bound.

## STATUS: STILL NOT FUNDABLE, and the blocker is DAYS, not cleverness
Everything above rests on 5-12 trading days of one regime. The captures
(`capture_cwing_books.py`, 3-min cadence) are running and accumulate automatically.
What is needed before any capital: (i) enough dates to contain at least one EIA/OPEC shock;
(ii) the simultaneous-hit count on those dates; (iii) the same band curve re-measured
out-of-sample on the new dates. **No new idea is required. The lane needs calendar time.**

---

# 6. THE GATE, ANSWERED — from WTI's history, not from waiting

"Wait 3 more weeks of capture" was the same stall the energy-wings lane has sat in since
07-25. It was unnecessary: **the tail question is about WTI, not about Kalshi**, and two
years of WTI already exists.

**Step 1 — calibrate price to distance** (201 KXWTIH hourly events with a T-30m book):
a rung priced 1-5c sits **1.137% of spot** away; 5-10c = 0.867%; 10-20c = **0.627%**;
20-40c = 0.373%. (median spot $81.38)

**Step 2 — ask WTI how often it actually moves that far in 30 minutes.**
CL=F, 13,397 hourly bars 2024-03-14 -> 2026-08-06 (30-min equivalent = hourly/sqrt2), plus
1,153 exact 30-min terminal moves rebuilt from 5-minute bars over the last 60 days.

| band | distance | Kalshi implied | realized 2yr | realized 60d (exact) | sell-YES EV |
|---|---|---|---|---|---|
| 1-5c | 1.137% | 3.0% | 1.88% | 1.47% | **+1.12c** |
| 5-10c | 0.867% | 7.5% | 3.52% | 3.99% | **+3.98c** |
| 10-20c | 0.627% | 14.6% | **7.23%** | 8.50% | **+7.37c** |
| 20-40c | 0.373% | 27.5% | 18.20% | 22.90% | **+9.30c** |

**Every band is overpriced against TWO YEARS of the underlying, shocks included.** This is no
longer an 11-quiet-day artifact. And it cross-validates: Kalshi's own settled outcomes put
the 10-20c band at **6.25% realized** (10 of 160) against 7.23% from WTI history. Two
independent measurements, different data, same answer.

## THE CORRELATED TAIL — REAL, MEASURED, AND IT IS THE BINDING CONSTRAINT
At the 10-20c distance over 2 years: **969 breaches in 13,397 hours (7.23%)**.
- Breaches occur on **51.5% of days** — they are NOT confined to rare shock days.
- **The worst 5% of days carry 31% of all breaches.** Clustering is real but not total.
- **Worst days: 2026-03-10 breached 17 of 23 hours (74%). And it ran three days:
  Mar 9 (57%), Mar 10 (74%), Mar 11 (61%).** Also 2025-04-09 (52%), 2026-04-07 (52%).

**Per-cycle economics (10-20c):** sell at 14.6c against 85.4c collateral. Win 92.8% for
+17.1% of collateral, lose 7.2% for -100%. **EV = +8.6% of collateral per cycle.**
Independent-Kelly says 50% of bank. **Independent-Kelly is WRONG here and would be fatal.**

**Sizing against the measured cluster, not against independence:** deploying f of bank per
hourly cycle, a Mar-10 day (17 losses, 6 wins) costs **-16f of bank in one day**, and those
days come in threes.
- f = 3%: +5.9%/day normal (**~$58/day on $991**), but -48% on a bad day and a 3-day run
  leaves **14% of bank** — effectively ruin.
- f = 1%: +2.0%/day (**~$20/day on $991**), -16% on a bad day, 3-day run leaves 59%. Survives.
Capacity is irrelevant at these sizes: median 2,000 contracts rest at the bid; f=1% is ~11.

## WHAT IS STILL UNPROVEN (the honest weak point)
The price->distance mapping comes from **one 12-day quiet regime**; the realized rates come
from 2 years spanning all regimes. If Kalshi re-prices distance when vol rises, the gap in a
high-vol regime is not measured. **Next test, and it needs no new data: recompute the
implied-vs-realized gap within matched vol regimes** using the 60-day 5m series to bucket
days by realized vol, and check the gap holds in the top vol quartile. If it collapses there,
the strategy must stand down when vol rises — which is exactly when it would otherwise
be sized largest.
