# Per-strategy audit: nestor NON-LIP + the KXDXYDUD question (2026-07-28 night MT)

Researcher lane. Every dollar below is reconstructed from primary records: Kalshi
`/portfolio/fills`, `/portfolio/settlements`, `/portfolio/positions`, `/portfolio/balance`,
pulled 2026-07-28 ~21:05 MT (03:05Z). Nestor's own ledgers used ONLY for attribution
(which fill belongs to which strategy) and for signal/skip counts — never for dollars.

## 0. PIPELINE VALIDATION (done first, per doctrine)

Two sign traps found and killed before any aggregate was trusted:

1. **`action` is a label, `side` is the acquisition.** A fill with `action:"sell", side:"no"`
   ACQUIRES 17 NO contracts — it does not sell them. Verified on three tickers against
   `position_fp`. The naive read (`sell` = cash in) inverts the sign of ~50% of all fills.
2. **`settlements.yes_total_cost_dollars + no_total_cost_dollars` is cumulative gross cost,
   not a cost basis.** Using it directly gives an account realized P&L of −$643.30 — off by
   ~$560 — because intra-market netting is invisible to it. DO NOT USE THAT FIELD FOR P&L.

Correct model, matching Kalshi exactly: every fill acquires `side` at that side's price;
Kalshi immediately nets min(yes,no) pairs at **average cost**, crediting $1/pair; settlement
pays $1 per residual winning contract.

**Validation result:** replaying all 320 fills reproduces the exchange's own
`realized_pnl_dollars` on **24/24 positions with $0.000 total error**, and matches
`position_fp` and `total_traded_dollars` exactly on all 24. FIFO netting mismatches 5/24
($1.91 err), LIFO 5/24 ($1.15). **Average-cost is the exchange's convention.**

Script: `/private/tmp/.../scratchpad/ledger.py` (+ `pull.py`, `mr.py`, `mk.py`; copies on VPS
at `/tmp/pull_all.py`, `/tmp/mk.py`, `/tmp/mr.py`).

## 1. ACCOUNT TRUTH (03:05Z)

cash $400.9022 · exchange mark of open positions $124.13 · open cost basis $177.97
(⇒ −$53.84 unrealized) · realized net-of-fee since first fill 2026-06-17: **−$85.558**
· rewards credited $7.482 (verified receipt, external_cash.jsonl).

UNVERIFIED: the deposit side. external_cash.jsonl claims $531.94 of operator deposits;
cash + cost basis + realized − rewards implies ~$656.95. ~$125 gap, un-reconciled. Not this
lane; flagged. Per-strategy figures below are exact per-ticker reconstructions and do not
depend on it.

## 2. STREAK (streak killer)

Rule: on a run of k same-direction 15-min crypto closes, buy the reversal side. Entry gate
ask ≤ 46¢; rest a maker order at 40¢ for **15 seconds**; if unfilled, backstop as taker.
Size 10 contracts (~$4/market).

n = **13 real settled markets** (2026-07-25 → 07-28; the 6 "filled=True" rows from Jul 23-24
are paper/simulated — no exchange fills exist; the 1-contract KXBTC15M-26JUL232345 taker fill
is unmatched to any signal and excluded).

| | n | wins | stake | net |
|---|---|---|---|---|
| maker fills @40¢ | 5 | 2 (40%) | $20.00 | **$0.000** |
| taker backstop | 8 | 3 (38%) | $27.58 | **+$1.197** |
| total | 13 | 5 (38.5%) | $47.58 | **+$1.197** |

Contract level: 128 contracts, 50 won = 39.1%. Breakeven (cost $47.58 + $1.22 fees over 128)
= **38.1%**. Edge = **+1.0 percentage point**. SE on the market win-rate at n=13 = **13.5pp**.
95% CI on cumulative P&L: **−$33.58 to +$35.97**.

Today's "no fills": 14 distinct signals, **1 fill** (KXBTC15M-26JUL281915 @40¢ maker, LOST
−$4.00). Named mechanisms, from streak_week1.jsonl:
- 7 × `streak_pre_t0_declined / outcome:risk_rejected / detail:"Halted"` (15:44–15:59Z) — the
  engine's global risk halt, not the strategy.
- 16 × `streak_skip not_entry_window(ttc≈839s)` — signal fires outside the entry window.
- `price_above_gate` (35 lifetime) — ask above the 46¢ ceiling.
- The maker leg's resting window is `expiration_ts − backstop_at` = **15 seconds**. All 24
  logged `streak_maker_rest` events show `fill_count: 0.00` at log time; 5 of 24 eventually
  filled ⇒ ~21% maker fill rate on a 15-second quote.

**VERDICT: DECAY.** Not a structural kill — no defect found, the rule is coherent and the
one-day fill drought is an engine halt plus a price gate, not a broken signal. But the
measured edge (+1.0pp, se 13.5pp) is statistically empty and cumulative P&L is +$1.20 on
$47.58 staked over 6 days. Bench at current size. **Re-entry trigger: n ≥ 50 settled markets
with contract win-rate ≥ 45%** (≈7pp over breakeven, ~2σ at n=50), OR a conditioning gate
that itself clears an overfit bar.

## 3. VOLBOOK (metals dailies)

Rule: at 17:30Z on metals dailies (KXGOLDD/KXSILVERD/KXCOPPERD), estimate realized-vol
implied probability; where the model's implied% sits below the market's, buy NO under a price
ceiling keyed to implied% (97¢ ≤7%, 95¢ ~11%, 90¢ ~18%, 81¢ ~24%, 68¢ ~34%). Size by EV.

n = **10 fills across 2 session-days** (Jul 27, Jul 28). **10/10 won.** Net **+$3.782** on
$28.98 staked (13.0% on stake). Zero opens.

Contract economics: 33 contracts, weighted entry **$0.878**. Each win pays 12.2¢; each loss
costs 87.8¢. **Breakeven loss rate = 12.2%.**

**The null test.** Under "the market's price is fair" the probability of 10/10 wins is
∏(no_price) = 0.760·0.910·0.930·0.905·0.790·0.890·0.890·0.890·0.940·0.670 = **0.204**.
A 1-in-4.9 event. **Not evidence of edge.**

And n=10 overstates independence badly. Both days' rungs settle off the same afternoon metals
move, and gold/silver/copper co-move on a dollar shock (concept 43 §3: the settle source is
the position). Effective independent n ≈ **2 session-days**. Day economics:

| day | rungs | stake | pnl | loss if the day breaks | payoff ratio |
|---|---|---|---|---|---|
| Jul 27 | 5 | $14.81 | +$2.06 | −$14.81 | 7.2 : 1 |
| Jul 28 | 5 | $14.17 | +$1.72 | −$14.17 | 8.2 : 1 |

One bad day erases ~7–8 good days. Ryan's "it wins consistently" is literally true and
literally uninformative: it has traded two days and both were quiet.

**VERDICT: CONDITIONAL.** No defect; the mechanism is sound (sell the tail when your realized-
vol estimate beats the market's implied). It cannot be killed and must not be scaled. The gate
to hunt is the one that distinguishes a quiet metals day from a break day — and that gate must
itself clear the overfit bar. **Decision point: n ≥ 20 session-days.**

## 4. HOUSE / POTUS probe AND inflation scalping — these are ONE strategy

The CPI positions Ryan is asking about (KXCPIYOY-26AUG-T3.4/T3.5) are house-probe fills,
adopted into state.json as `orphan-adopted`. There is no separate inflation strategy.

Complete lifetime record, from house_probe.jsonl (92,927 rows; 7 fills, 6 markouts, 2 halts):

**POTUS book** (KXAPRPOTUS-26JUL31-40.9), 2 fills, 1 ct each:
No@63 → 60s markout **0.0¢**, no gap-through · Yes@36 → **+1.0¢**, no gap-through.
Netted, realized **+$0.010**.

**CPI book** (KXCPIYOY-26AUG), 5 fills, 1 ct each, all maker:
| fill | entry | mid at fill | 60s markout | gap-through |
|---|---|---|---|---|
| No | 55¢ | 55 | **−10.0¢** | YES → halt 19:30Z |
| No | 48¢ | 58 | −2.0¢ | no |
| No | 51¢ | 61 | **−13.0¢** | YES |
| No | 38¢ | 62 | (no markout logged) | — |
| Yes | 57¢ | 54 | **−3.0¢** | YES |

Mean 60s markout on CPI = **−7.0¢/fill** on ~50¢ entries (−14% per fill), **4/4 negative**,
**3/4 gap-through**. The probe's own defense halted it **twice today** (19:30Z, 20:39Z) on its
"−5¢ gap-through markout at the 60s horizon" rule. Realized: **−$0.101**; open cost $1.94.

Two books, two different answers. On POTUS the markouts are 0.0 and +1.0 — a quiet book where
presence is the product. On CPI every fill met information: a monthly-CPI market has no flow,
so the only counterparty who lifts a resting 1-contract quote is someone repricing on news.
That is concept 43 §4 exactly, and §5's toxicity test resolved it in **under two hours**.

**VERDICT (CPI book): STRUCTURAL kill.** Artifact named: the CPI book has no uninformed flow,
so 100% of fills are adverse selection; the spread cannot be earned because the only fills are
the ones you don't want. Numbers recorded above so it stays dead. Impact is separately trivial
(−$0.10) — the probe was correctly sized at 1 contract and its own markout guard worked.

**VERDICT (POTUS book): CONDITIONAL — insufficient data.** n=2 fills. Markouts 0.0 / +1.0 are
the right sign but two observations decide nothing.

## 5. THE KXDXYDUD QUESTION

### 5a. What the fills actually say

Two DXY markets have ever been traded, one settled.

- **KXDXYDUD-26JUL27-T101.4640**, 15 fills (11 maker, 4 taker), $64.41 traded, settled `yes`.
  Realized **+$27.856**.
- **KXDXYDUD-26JUL28-T101.3594**, 3 fills, open, −$0.280 realized, 17 NO @32¢ ($5.44) live.

**n = 1 settled market.** "We consistently win DXY" rests on one market.

And that one market was not a mean-reversion harvest. The tape (all 02:45–03:59Z 07-28):
acquired NO at 45¢/56¢/57¢/61¢/71¢/59¢, acquired YES at 41/40/39/43/50¢, then **34 contracts
of YES at 26¢**, then closed the whole book by taking NO at **16¢** (i.e. YES ≈ 84¢). The
money is one directional round trip, 26¢ → 84¢, in 23 minutes. Held-to-settlement markout on
the same fills is +$27.85 — identical, because it settled `yes` anyway. That is a single
lucky path, not a repeated capture.

### 5b. Ryan's premise has a factual error

**KXDXYDUD is not an hourly.** Open 04:00Z, close 17:58Z the *next* day — a ~38-hour daily
up/down. Only 22 have ever settled. The true index hourlies are KXINXHUD / KXNDQHUD
(open 19:00Z, close 20:00Z). The horizon difference matters: a 38-hour hold rents capital
~38× longer than the strategy Ryan described.

### 5c. The hypothesis, tested directly, at real n

Hypothesis: index up/downs mean-revert, so a maker filled on a spike and held to close is
systematically right.

The test has a sharp null. If price is a martingale w.r.t. settlement, then E[100·V | F_t] =
mid_t, so for ANY conditioning measurable at t — including the sign of the last move — the
expected markout is **exactly zero**. Statistic: `edge = sign(Δmid_t) · (mid_t − 100·V)`,
positive ⇒ overshoot ⇒ maker edge. Data: 1-minute candlesticks (yes_bid/yes_ask close) over
every settled market in each series, two-sided books only, spread ≤ 10¢, one aggregated
observation per market.

- **A (idealized)**: maker fills at the post-spike mid. Null = 0.
- **B (realistic)**: maker was resting at the prior best quote and got run over — on an
  up-spike it acquires NO at (100 − ask_{t−1}); on a down-spike, YES at bid_{t−1}. This is the
  actual maker's ledger, half-spread included.
- **reversal**: `sign(Δmid_t) · −(mid_{t+5} − mid_t)`, positive ⇒ price came back.

Per-market means, ±1 se, |move| ≥ 2¢:

| series | markets | spikes | A idealized | B realistic | 5-min reversal |
|---|---|---|---|---|---|
| KXDXYDUD (daily) | 19 | 343 | +5.24¢ (t=1.06) | **−0.09¢ (t=−0.02)** | −2.74¢ (t=−1.09) |
| KXINXHUD (hourly) | 72 | 1077 | −2.64¢ (t=−1.63) | **−5.41¢ (t=−3.31)** | +0.26¢ (t=0.62) |
| KXNDQHUD (hourly) | 59 | 476 | +2.80¢ (t=0.95) | **−1.04¢ (t=−0.36)** | −0.17¢ (t=−0.36) |

At |move| ≥ 4¢ the realistic maker markout worsens monotonically: DXY −1.71¢, INX **−10.91¢
(t=−4.58)**, NDQ −6.29¢ (t=−1.68).

**The 5-minute reversal term is zero in all three series at every threshold.** There is no
mean reversion to harvest. The prices are martingales, as efficiency predicts.

**VERDICT: STRUCTURAL kill of the mechanism.** n = 150 markets, ~2,900 spike observations.
The artifact that produced the belief is named: **one market, one 26¢→84¢ path, +$27.86.**
The hypothesised mechanism does not exist; where the maker's markout is measurable with power
(INX, n=72) it is significantly **negative** — the maker filled on a spike is systematically
*wrong*, and the half-spread does not pay for it. DXY's own markout is −0.09¢ ± 4.83 — not
distinguishable from zero, and certainly not from positive.

Supporting evidence from our own tape: the other index up/downs we actually traded lost
everything staked — KXNDQHUD-26JUL281100 −$9.99 (111 NO @9¢, 100% loss), KXINXHUD-26JUL281100
−$9.96 (83 NO @12¢, 100% loss), KXBTCD-26JUN2412 −$10.00 (312 NO @3¢, 100% loss). As a class,
index up/downs are **+$27.86 − $29.95 = −$2.09 over 4 settled markets.**

**This is not a genuine edge. Treat every dollar of LIP earning on DXY as rewards subsidy
until a positive markout is measured at n ≥ 30 markets.**

## 6. LIP maker context (for feasibility, not a verdict — not this lane)

LIP-class (gas, UST, TRUEV, mentions, hourlies, ballot): 36 settled markets, 11 wins (31%),
$650.71 staked, **−$54.757 realized**, plus 21 open markets at $176.03 cost basis marked
~$124. Hold-to-settlement markout on LIP **maker** fills: **−$84.64 on $588.05 staked
(−14.4%, 142 fills)**. On LIP **taker** fills: +$29.88 on $62.66 (18 fills) — the exits, not
an edge. Worst: KXUST7AD maker −63.7%, KXUST10AD maker −39.4%.

Note also: **lip-v5.service is RUNNING LIVE** (`--live --mode shared --allow-fresh`, started
02:45Z / 8:45pm MT) despite note 44 recording v5 as STAGED-INERT pending Ryan's gate sequence.
It has taken 6 fills on KXGENERICBALLOTVOTEHUB-26JUL31-T5.7 tonight, realized **−$6.95**
($84.95 traded, $1.24 of taker fees on the exit). Reported, not acted on.

## 7. Capital recommendations (bankroll $95.02 nestor; account $400.90 cash)

| strategy | now | recommend | reasoning |
|---|---|---|---|
| STREAK | ~$4/mkt | **hold $4/mkt** | +1.0pp edge ± 13.5pp. Adding capital multiplies a number we cannot sign. Cost of waiting is ~$0.09/market of forgone EV; cost of being wrong is 10× that. |
| VOLBOOK | ~$14.50/day | **hold $14.50/day, cap the DAY not the rung** | The 7–8:1 payoff means the correct risk unit is the session, not the ticker. n=2 days. Revisit at 20. |
| HOUSE — CPI book | 1 ct | **$0 — stop quoting CPI** | Structural kill. 4/4 negative markouts, 3/4 gap-through, mean −7¢/fill. |
| HOUSE — POTUS book | 1 ct | **hold 1 ct** | Only markouts in the book that are non-negative. Cheapest possible way to buy n. |
| DXY / index up-downs | ~$5 open | **$0 incremental** | Mechanism structurally refuted at n=150 markets. |

Total non-LIP working capital: **~$25–30/day**, well inside the $95 bankroll. **No strategy
here has earned an increase.** The binding constraint is not capital — it is n. Every one of
these lanes is between 1 and 13 independent observations.

The one action that would change this: STREAK and VOLBOOK are both n-starved and both cheap.
Running them unchanged for 3 more weeks costs ~$0 in expectation and buys the n that makes a
sizing decision possible. That is the purchase to make, not a capital increase.

## 8. Which CONCEPT file changes

**[[43 - THE MONEY GAME (execution concepts)]]** — §4 (the maker's ledger) and §5 (toxicity).
Two additions, both paid for in dollars:

1. **§1 needs the acquisition rule stated as an API fact, not just an economic one.** "Every
   fill is an acquisition" is already there; what is missing is that the venue's own record
   *labels* half of them `sell`, and that its settlement rows carry gross cumulative cost, not
   a basis. Any mind reading our tape without this inverts the sign of half the fills and
   mis-states the account by ~$560. Netting is **average-cost** — proven against 24/24
   positions.
2. **§4 needs its converse.** The file says adverse selection concentrates where books are
   fast and informed. It does not say the mirror: **a market with no flow is not a quiet book,
   it is a pure adverse-selection book** — zero uninformed volume means 100% of fills are
   informed. The CPI result (−7¢/fill, 3/4 gap-through, two self-halts in one afternoon)
   against the POTUS result (0.0¢, +1.0¢) is the discriminator, and §5's toxicity test found
   it in under two hours. "Quiet books invert it, the absent market-maker is the fish" is
   true only where someone still trades; §5's own mirror ("zero fills forever...") needs to
   extend to *almost*-zero fills.
3. **§6 gains a named failure**: horizon read off a series' nickname rather than its
   `open_time`/`close_time`. KXDXYDUD was operated as an "hourly" and is a 38-hour daily.

**No change to [[07 - Overfitting & Validation Discipline]]** — it already prescribes the
one-obs-per-market and sharp-null discipline used here; it was followed, and it worked.

---

# APPENDIX A — Is the loss longshot decay? (2026-07-28, coordinator's question)

Same validated pipeline (24/24 positions, $0.000 error). All figures from fills + settlements.

## A0. First, the denominator in the question is wrong

−$85.558 is **not** a loss on $177.97 of cost basis; those are two different books.

- **−$74.524** realized on **66 settled markets**, against **$928.70 of capital actually
  deployed into them** (Σ count × acquisition price). That is **−8.02%**, not −48%.
- **−$11.034** of netting-realized P&L booked inside 21 markets that are still open.
- $177.97 is the residual cost basis of those still-open markets (marked $124.13 by the
  exchange ⇒ −$53.84 unrealized, not yet realized and not in the −$85.56).

The identity closes exactly: settled fill-level Σ count·(V_side − price) = −$67.590 gross,
−$6.934 fees, = **−$74.524**, matching the per-ticker ledger to the cent.

The coordinator's structural instinct is still right, and for a better reason than magnitude:
a 60-second markout cannot see this loss **because these positions were never marked out —
they were held to expiry.** The measurement window and the loss window do not overlap.

## A1. Settled fills by acquisition price

| bucket | fills | contracts | cost | settle value | P&L | % of cost that expired worthless | return |
|---|---|---|---|---|---|---|---|
| 0–5¢ | 16 | 624.6 | $13.27 | $0.00 | **−$13.99** | 100% | −105.4% |
| 5–15¢ | 21 | 731.8 | $63.02 | $52.00 | −$11.45 | 95% | −18.2% |
| 15–40¢ | 56 | 634.8 | $164.45 | $272.41 | +$104.81 | 52% | +63.7% |
| 40–60¢ | 40 | 392.3 | $188.67 | $132.47 | −$57.52 | 68% | −30.5% |
| 60–85¢ | 41 | 324.6 | $227.90 | $113.40 | **−$115.10** | 65% | −50.5% |
| 85–99¢ | 33 | 290.8 | $271.39 | $290.83 | +$18.73 | 0% | +6.9% |
| **total** | 207 | 2999.0 | $928.70 | $861.11 | **−$74.52** | | −8.0% |

**Read this table with care — it is confounded.** A closing leg lands in a bucket too. The
+$52.00 of settle-value in 5–15¢ is one trade: 52 contracts of NO at 6¢ bought to close the
gas 4.100 book. Bucketing by price mixes opening inventory with exits. The next section is the
honest cut.

## A2. THE DECISIVE SPLIT — one-sided or two-sided?

**54 of 66 settled markets were one-sided** (<10% of the market's cost on the minority axis).
We were not running a two-sided book in 82% of the markets we traded.

Exact decomposition of the −$74.524, per market, netting at average cost:

| component | amount | capital | return |
|---|---|---|---|
| **spread capture on netted inventory** | **+$39.629** | $536.08 paired cost, 576 paired contracts | +7.4% (6.88¢/pair) |
| **directional P&L on un-netted residual** | **−$107.219** | $392.62 | **−27.3%** |
| fees | −$6.934 | — | — |
| **total** | **−$74.524** | $928.70 | −8.0% |

When inventory nets, we make money. When it does not, we lose 27% of it. That is the whole
story of the tape.

## A3. Attribution — and the significance test that decides it

Splitting the directional residual by the price at which it was acquired:

| component | n markets | sum | mean/mkt | sd | se | **t** |
|---|---|---|---|---|---|---|
| spread capture | 18 | +$39.63 | +2.202 | 7.14 | 1.68 | +1.31 |
| — same, ex top-2 markets | 16 | **+$0.79** | +0.049 | 1.44 | 0.36 | **+0.14** |
| directional, residual ≥15¢ | 46 | −$44.75 | −0.973 | 13.45 | 1.98 | **−0.49** |
| **directional, residual <15¢** | **15** | **−$62.47** | **−4.165** | 4.48 | 1.16 | **−3.60** |
| fees | — | −$6.93 | — | — | — | exact |

**The cheap-residual term is the only component of the loss that survives a significance test.**

- **15 of 15 markets with cheap un-netted residual lost 100.0% of it.** Not 95%, not 98% —
  1,123.4 contracts, $62.47 of cost, $0.00 of settlement value. Zero survivors.
- That $62.47 is **6.7% of the $928.70 deployed** and **84% of the −$74.52 settled loss**.
- Those 1,123.4 contracts are **37% of all 2,999 contracts we ever held**, bought with 6.7% of
  the money — average price **5.6¢** against a whole-tape average of 31¢. That is the
  signature the hypothesis predicted: contract count purchased at the cheapest available rung.
- The mid-price story does **not** survive: residual ≥15¢ is −$44.75 at **t=−0.49**, with
  27 of 46 markets positive, and it is dominated by one market — KXUST10AD-26JUL28-T4.61,
  −$59.22 directional (the documented B1 correlated-treasury run-over). Ex that market the
  entire directional term is −$48.00 at t=−0.66. **Insufficient evidence of any mid-price
  edge or anti-edge.**
- Spread capture does **not** survive either: +$39.63 collapses to **+$0.79 (t=+0.14)** once
  KXDXYDUD-T101.4640 (+$28.59) and KXAAAGASD-4.100 (+$10.25) are removed. We have **not**
  demonstrated we can capture spread. Two markets are the entire claim.
- Fees are negligible: −$6.93, 9% of the loss.

The individual cheap positions, every one a total loss:

| market | residual | price | cost | P&L |
|---|---|---|---|---|
| KXAAAGASD-26JUL28-4.115 | 161.2 YES | 6.9¢ | $11.15 | −$11.15 |
| KXUST10AD-26JUL28-T4.59 | 76.0 NO | 13.0¢ | $9.88 | −$9.88 |
| KXNDQHUD-26JUL281100 | 111.0 NO | 9.0¢ | $9.99 | −$9.99 |
| KXINXHUD-26JUL281100 | 83.0 NO | 12.0¢ | $9.96 | −$9.96 |
| KXBTCD-26JUN2412 | 312.0 NO | 3.0¢ | $9.36 | −$9.36 |
| KXUST7AD-26JUL28-T4.56 | 44.0 YES | 10.0¢ | $4.40 | −$4.40 |
| KXUST5AD-26JUL28-T4.46 | 28.0 YES | 11.0¢ | $3.08 | −$3.08 |
| KXUST10AD-26JUL28-T4.69 | 110.0 YES | 1.0¢ | $1.10 | −$1.10 |
| KXUST5AD-26JUL28-T4.48 | 100.0 YES | 1.0¢ | $1.00 | −$1.00 |
| KXAAAGASD-26JUL28-4.120 | 30.0 YES | 3.3¢ | $1.00 | −$1.00 |
| + 5 gas rungs at 1.0–8.4¢ | 68.2 | | $1.56 | −$1.56 |
| **total** | **1123.4** | **5.6¢ avg** | **$62.47** | **−$62.47** |

## A4. Does the deny list already fix it?

| venue class | markets | cost | spread | directional | net | return |
|---|---|---|---|---|---|---|
| treasury (**denied on paper, breached in practice**) | 18 | $258.83 | +$1.19 | −$107.01 | **−$106.16** | −41.0% |
| index hourly (denied) | 2 | $19.95 | 0 | −$19.95 | −$19.95 | −100% |
| mention (denied) | 5 | $54.31 | +$1.63 | −$11.94 | −$10.31 | −19.0% |
| streak (nestor) | 17 | $108.59 | +$0.82 | −$7.86 | −$9.56 | −8.8% |
| entertainment/sport (denied, June) | 2 | $131.06 | −$2.90 | 0 | −$3.99 | −3.0% |
| volbook (nestor) | 10 | $28.98 | 0 | +$4.02 | +$3.78 | +13.0% |
| index daily / DXY (denied) | 2 | $73.77 | +$28.59 | −$9.36 | +$17.86 | +24.2% |
| **gas (still quoted)** | 10 | $253.21 | +$10.31 | +$44.89 | **+$53.81** | +21.3% |

Aggregates: **denied venues −$16.39 on $279.09 (−5.9%); still-quoted venues −$52.35 on
$512.04 (−10.2%).** The loss does *not* concentrate in what we have denied.

The cheap-residual $62.47 by venue:
- **index up/downs (now denied): $29.31 — 47%.** Fixed by the deny list.
- **treasury: $19.46 — 31%.** Denied per note 44, and note 44 records the deny list was
  breached ("UST orders posted despite deny list"). Not fixed until enforcement is.
- **gas: $13.71 — 22%, in a venue we still actively quote**, and it holds the single largest
  cheap position on the tape (KXAAAGASD-4.115, 161.2 contracts at 6.9¢, −$11.15).

**Answer: no. The deny list closes 47% of it.** 53% sits in venues that are live or where the
deny list did not hold — and gas, our single most profitable venue (+$53.81), is also where
the largest live longshot sits. The right guard is not a venue ban; it is a rung ban.

## A5. What would a book have to do differently — and what would it have done to this tape?

**Enforced two-sided quoting is NOT the fix, and the data says why.** The cheap rungs are
structurally un-nettable: at a 3¢ rung, "two-sided" means quoting 3¢ bid against a 97¢ ask
that never fills. 15 of 15 markets with cheap residual had that residual *never* offset — not
by our exits, not by anyone. A quote that can only ever fill on one side is not a market-making
quote; it is a purchase. Enforcing two-sidedness on a rung where only one side trades produces
no pairs, only latency.

**The fix indicated is a rung floor: do not quote, and never hold to expiry, below X¢.**

Replayed on this exact tape (drop every fill on a rung priced below X — i.e. never quote it):

| floor | spread | directional | fees | **NET** | deployed |
|---|---|---|---|---|---|
| 0¢ (actual) | +$39.63 | −$107.22 | −$6.93 | **−$74.52** | $928.70 |
| 5¢ | +$38.90 | −$93.22 | −$6.21 | −$60.53 | $915.43 |
| 10¢ | +$30.24 | −$105.79 | −$5.78 | −$81.32 | $884.65 |
| 15¢ | +$30.81 | −$74.11 | −$5.78 | −$49.08 | $852.41 |
| 20¢ | +$0.06 | −$60.61 | −$5.20 | −$65.75 | $816.84 |

**This is non-monotonic, and I will not claim a number from it.** 10¢ is *worse* than doing
nothing. The reason is that three markets (UST10-T4.61 −$55.29, gas 4.100 +$66.25, DXY
+$27.86) dominate the whole tape and their fills straddle the thresholds — the counterfactual
is measuring which giant survives the cut, not the guard.

Removing those three and re-running on the remaining **63 markets** gives a clean monotone
answer:

| floor | NET | deployed | markets |
|---|---|---|---|
| 0¢ | −$113.34 | $656.05 | 63 |
| 5¢ | −$99.35 | $642.78 | 57 |
| 10¢ | −$71.26 | $615.13 | 55 |
| **15¢** | **−$39.01** | $582.88 | 51 |
| **20¢** | **−$20.01** | $563.99 | 51 |

**On the body of the distribution a rung floor recovers $93 of $113 in loss while giving up
14% of deployed capital.** That is the robust result: monotone, 51–63 markets, no dependence
on any single trade.

**UNVERIFIED and stated as such:** what enforced two-sided quoting would have earned on the
$392.62 of directional cost cannot be computed — it requires fill prices on an axis that never
traded. The naive ceiling (apply the measured +7.39% paired return) is +$29.02, a +$136 swing.
That number is a ceiling, not a forecast, and it rests on a spread-capture rate that is itself
+$0.79 (t=+0.14) once two markets are removed. **Do not size on it.**

## A6. Plain answers

1. **Is the −48% explained by longshot decay?** The −48% is an artifact of dividing realized
   loss by open cost basis; the real figure is −8.0% on $928.70 deployed. **But yes — longshot
   decay explains 84% of the realized loss**, and it is the only component that survives a
   significance test (n=15, t=−3.60, 15/15 markets at exactly −100%). 6.7% of the capital,
   37% of the contracts, 84% of the loss.
2. **Are we running a market-making book?** No. 54 of 66 markets one-sided. And our
   demonstrated ability to capture spread is +$0.79 ± $1.44 per market once two lucky markets
   are removed — statistically nothing.
3. **Does it need enforced two-sided quoting?** No — that is the wrong instrument. A cheap
   rung has no second side to enforce. It needs a **minimum rung price**, applied to the
   quoting universe (never open there), not as a mid-tape floor.
4. **Has the deny list fixed it?** 47% of it. 31% sits in treasury where the deny list was
   breached, 22% in gas which we still quote.
5. **n caveats.** Cheap-residual: n=15 markets, conclusive. Spread capture: n=18, and 2
   markets are 98% of it — **insufficient**. Directional ≥15¢: n=46, t=−0.49 — **insufficient**,
   and one market is 55% of it. Every counterfactual in A5 is dominated by 3 markets on the
   full tape and is only meaningful on the 63-market subset.

## A7. Which CONCEPT file changes (revised)

**[[43 - THE MONEY GAME]] §1 and §7** — this supersedes the softer version in §8 above.

§1 currently says *"the cheap side of any book is cheap to hold and cheap to be wrong about."*
**That sentence is now the most expensive line in the file.** It is true per contract and
catastrophically false per dollar: cheap is where you buy the most contracts per dollar, and
each one that dies takes 100% of what is in it. On this tape the cheap side was wrong 15 times
out of 15. Replace it with the measured form: *a rung's price is the fraction of your capital
it destroys when it loses; cheap rungs buy contract count, and contract count is the reward
metric, so the rung that maximises subsidy is the rung that maximises capital destruction —
these are the same rung by construction.*

§7 currently says *"breadth beats depth once share saturates"* and *"reward earning = capital
× time × proximity."* It does not say the mirror it needs: **the cheapest way to buy proximity
is the rung nobody wants, and a fill there is not a cost of goods — it is a write-off.** §2's
"fills are the cost of goods" framing holds only where the position can be exited or offset;
below ~15¢ neither is available, so the fill converts capital directly to zero at expiry.

Add to §5 (toxicity): **presence-seconds-per-dollar-hour is not sufficient.** A 1¢ rung scores
beautifully on it — huge size, near best, long survival — and returns −100%. The missing
statistic is **fraction of acquired inventory that ever nets.** On this tape: paired inventory
+7.4%, un-netted −27.3%, un-netted-and-cheap −100.0%.
