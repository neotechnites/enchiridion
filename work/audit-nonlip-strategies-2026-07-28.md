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

---

# APPENDIX B — Is the LIP subsidy worth chasing, and what else? (2026-07-28 night)

Constraint: *whichever meets the goal or makes more money.* Same validated pipeline. New data:
1-minute candlesticks (yes_bid/yes_ask) for all 66 settled markets we traded — 27,181 bars —
so every exit below is priced at a **real touch**, plus Kalshi's 7%·p·(1−p) taker fee. The
hold-to-expiry replay reproduces −$74.524 exactly, so the exit sims share one denominator.

## B0. The prize, measured

**$7.482/day.** That is the entire verified reward receipt: one payout, 17 gas rung programs,
KXAAAGASD-26JUL28, credited 05:46:16Z, confirmed against the account. It implies
**$0.440 per rung-program per day**. Independent cross-check: note 44's measured 0.36%/hr on
deployed × 7h × ~$300 = $7.56/day. **The two agree.** (Everything else — the $65.53 modelled
accrual — is UNVERIFIED and historically 4–8× optimistic.)

**The position book, measured, same period:**

| settlement day | realized |
|---|---|
| 06-19 / 06-21 / 06-24 | −$1.43 / −$2.56 / −$21.46 |
| 07-24 / 07-25 / 07-26 / 07-27 / 07-28 | +$0.71 / +$6.04 / −$12.94 / +$14.17 / **−$57.04** |

n=8 days, **mean −$9.32/day, sd $22.15, t=−1.19**. Nestor+LIP era only (≥07-23): n=5,
−$9.81/day, sd $28.18.

**This is the whole finding in two numbers.** The subsidy is $7.48/day. The book it rides on
has $22.15/day of noise and a −$9.32/day point estimate. **The prize is one third of the
noise.** To distinguish a $7.48/day reward from zero at t=2 against sd $22.15 requires
**35 trading days**. We have 8, and the book is losing faster than the subsidy pays.

## B1. The price floor, costed net-of-everything

Reward model: $0.440/rung/day × rungs surviving the floor. **UNVERIFIED extrapolation** — it
is one receipt, one venue, one day, and it assumes reward scales with rung count rather than
with capital. Stated so you can discount it.

| floor | position P&L (settled tape) | deployed | contracts | rungs kept | reward/day (modelled) |
|---|---|---|---|---|---|
| 0¢ (actual) | −$74.52 | $928.70 | 2999 | 66 | $7.48 |
| 10¢ | **−$81.32** | $884.65 | 1919 | 50 | $5.67 |
| 15¢ | **−$49.08** | $852.41 | 1643 | 46 | $5.21 |
| 20¢ | −$65.75 | $816.84 | 1435 | 39 | $4.42 |
| 25¢ | −$58.20 | $799.54 | 1352 | 38 | $4.31 |
| 30¢ | −$77.82 | $774.18 | 1256 | 35 | $3.97 |

**This is not a policy, it is a knob.** The P&L swings $32 across settings with no monotone
structure — 10¢ is *worse* than no floor at all, 15¢ is the best, 20¢ worse again, 30¢ worse
than 15¢ by $29. Best case (15¢) recovers $25.44 over a 6-day tape = **+$4.24/day** while
giving up **$2.27/day** of modelled reward. **Net +$1.97/day, inside a book with sd $22.15/day.**

It is only monotone once the three dominant markets are removed (Appendix A5: −$113.34 → −$20.01
across 63 markets), which is real evidence that the *body* of the distribution improves — but a
policy that must have its three biggest trades removed before it behaves is not yet a policy.

**VERDICT: CONDITIONAL.** The gate to hunt is a rung floor, and it must clear the overfit bar
before it is trusted — the full-tape instability is exactly what an overfit knob looks like.

## B2. What actually nets — and whether spread capture is real at all

Appendix A's "+$39.63 spread capture" was an artifact of average-cost per-market splitting.
Re-run as **chronological FIFO pair matching** — did this contract actually round-trip? — the
number changes and the conclusion inverts.

**114 pair-events, 885.7 paired contracts, total capture +$14.08.**

| by combined pair cost (yes price + no price paid) | pairs | mkts | contracts | capture | ¢/ct |
|---|---|---|---|---|---|
| < $0.90 | 9 | 3 | 84.0 | **+$31.30** | +37.25 |
| $0.90–0.99 | 46 | 15 | 267.6 | +$9.82 | +3.67 |
| ≈ $1.00 | 7 | 6 | 116.4 | $0.00 | 0.00 |
| **> $1.00 (we paid up)** | **52** | **19** | **417.7** | **−$27.03** | **−6.47** |

| by hold-to-netting | pairs | capture | ¢/ct |
|---|---|---|---|
| <1 min | 3 | +$0.96 | +2.16 |
| 1–10 min | 24 | +$0.86 | +0.45 |
| **10–60 min** | **51** | **+$22.39** | **+6.76** |
| 1–6 h | 26 | −$8.46 | −3.37 |
| >6 h | 10 | −$1.67 | −2.45 |

Three facts kill the market-making claim:

1. **Only 28.1% of contracts we acquired ever netted.** 1,771.5 of 6,300.7. The other 4,529.2
   were held to expiry or are still open. A book that offsets 28% of its inventory is not
   making markets; it is accumulating.
2. **47% of the paired contracts were netted at a structural loss** — 417.7 contracts bought
   at a combined yes+no cost *above $1.00*, capture −6.47¢/contract, −$27.03. We crossed the
   spread against ourselves in 52 of 114 round trips.
3. **Per market: n=30, sum +$14.08, mean +$0.469, sd $5.56, t=+0.46. Ex the top 3 markets:
   n=27, sum −$18.32, mean −$0.679, sd $1.573, t=−2.24 — significantly NEGATIVE.**

**VERDICT: STRUCTURAL kill of "we capture spread by resting on both sides."** Artifact named:
the strategy's return requires netting, and 72% of inventory never nets while half of what does
net is bought for more than a dollar. The +7.4% figure was one market (KXDXYDUD, +$28.31 of
+$14.08 total — i.e. everything else combined is negative). Recorded so it stays dead. The only
positive regime — combined cost <$0.90, 10–60 minute holds — is **9 pair-events in 3 markets**
and is the DXY market already killed in §5c. There is no regime where inventory reliably nets.

## B3. The exit we never use

Flatten all residual at X% of the market window elapsed, at the real touch, taker fee charged:

| flatten at | NET | vs hold | exits | mean/mkt | t | ex-top-3 NET | ex-3 vs hold | ex-3 t |
|---|---|---|---|---|---|---|---|---|
| 25% | −$84.30 | −$9.77 | 13 | −0.148 | −0.56 | −$123.11 | −$11.42 | −0.56 |
| 50% | −$74.35 | +$0.18 | 26 | +0.003 | +0.01 | −$102.40 | +$9.30 | +0.38 |
| 60% | −$65.91 | +$8.61 | 32 | +0.131 | +0.40 | −$99.44 | +$12.26 | +0.62 |
| 70% | −$58.42 | +$16.10 | 36 | +0.244 | +0.67 | −$86.50 | +$25.19 | +1.14 |
| **80%** | **+$4.78** | **+$79.30** | 43 | +1.202 | **+1.81** | −$62.01 | **+$49.69** | **+1.43** |
| 90% | −$51.58 | +$22.94 | 47 | +0.348 | +0.50 | −$87.75 | +$23.94 | +0.65 |
| 95% | −$85.28 | −$10.76 | 52 | −0.163 | −0.13 | −$93.49 | +$18.21 | +0.73 |

**Does the book go flat? At one hand-picked setting, yes — and that setting is a spike.**
The 80% row is the only one that turns the tape positive, and **41% of its gain is one market**
(KXAAAGASD-4.100, +$53.54) with the top three markets supplying 84%. Its neighbours at 70% and
90% give a quarter of it. **No setting reaches significance** (best t=+1.81 full-tape, +1.43
ex-top-3), and 25% and 95% are worse than doing nothing.

What *is* mildly encouraging and honest to say: the sign is stable — every setting from 50% to
95% is positive both full-tape and ex-top-3. That is weak, consistent evidence pointing the
same direction as the rung floor: **stop carrying un-nettable inventory into expiry.** It is
not yet a number to size on.

**VERDICT: CONDITIONAL.** n=66 markets, best t=1.43. Insufficient. The gate is real (exit
before expiry) but the knob is unproven and must clear the overfit bar.

## B4. The honest alternative

### The mirror — are the longshot buyers the ones being harvested?

We *were* the longshot buyer: 15/15 markets, −100%, $62.47 gone (Appendix A). The other side of
that seat is acquiring at ≥85¢. Every such acquisition on the tape:

**n=20 markets, 20/20 won, 290.8 contracts, cost $271.39, value $290.83, P&L +$19.44 (+7.2%).
Zero contracts expired worthless. Per-market mean +$0.972, sd $1.138, t=+3.82.**

**That t is the wrong test and I will not report it as evidence.** The per-market P&L is bounded
above by the small premium and unbounded below; the left tail is simply unsampled. The right
test is binomial: **P(all 20 markets win | market's price is fair) = 0.199 — a 1-in-5 event.**
Not significant. And this trade is not new: 7 of the 20 markets are volbook, so "take the other
side" *is* volbook, already verdicted CONDITIONAL at n=2 session-days with a 7–8:1 payoff ratio.

**VERDICT: CONDITIONAL, same gate as volbook.** It is the most sign-consistent thing on the
whole tape (35/35 favourable observations across the ≥85¢ bucket and volbook) and it is still
a 1-in-5 coincidence under the null. Do not scale it on 20 markets.

### Expected daily P&L at $1k and $2k

| option | E[P&L]/day @$1k | @$2k | capital at risk | daily sd | n behind it |
|---|---|---|---|---|---|
| LIP as run today | rewards +$25 − book −$31 = **−$6** | +$50 − $62 = **−$12** | full $1–2k | **$74 / $148** | 8 days, t=−1.19 |
| LIP + 15¢ rung floor | +$17 − $24 = **−$7** | +$35 − $48 = **−$13** | ~$0.9–1.8k | ~$70 / $140 | knob unstable, $32 range |
| LIP + 80% flatten | **UNVERIFIED** | UNVERIFIED | full | — | t=+1.43, one market = 41% |
| Mirror / volbook-style | **+$13** (13% on $100 at risk) | +$26 | $100–200 only | ±$100 tail | 20 mkts, P(null)=0.199 |
| Volume-incentive program | **UNVERIFIED — no data pulled** | — | — | — | n=0 |
| **Do nothing** | **$0** | **$0** | **$0** | **$0** | exact |

Scaling notes, because the naive multiplication is wrong twice:
- **Rewards scale with breadth, not depth** (concept §7 — pools saturate per market). At the
  verified $0.440/rung/day, **$200/day requires ~455 rung programs quoted simultaneously**;
  $50/day requires 114; $25/day requires 57. We ran 17. Pouring $2k into the same 17 rungs
  earns close to the same $7.48.
- **The book's variance scales with capital, the subsidy does not.** At $2k the position book
  swings ±$148/day against a $50/day subsidy. That is a coin flip wearing a rebate.

### Is $200/day on $1–2k supportable?

**No. Nothing measured on this tape supports 10–20%/day, and most of it does not support 0%.**
The largest verified positive on the entire account is the $7.482 reward receipt. The largest
strategy P&L is volbook at +$3.78 over two days with P(null)=0.20. The position book's point
estimate is −$9.32/day. A target of $200/day is **27× the entire measured subsidy** and would
require an edge that no measurement here has detected at any significance.

**What IS supportable, stated plainly:** the reward *mechanism* is real, verified by receipt,
and worth **≈2.5%/day on deployed capital** — which is an enormous rate *if and only if* the
position book can be held near zero. Three independent tests (rung floor, disciplined exit,
netting regime) all point at the same fix and **none of them reaches significance.** So the
honest answer to the coordinator's framing is:

**The LIP subsidy is not currently worth chasing at our scale — not because the subsidy is
small relative to capital, but because it is small relative to the noise of the book required
to earn it ($7.48 vs sd $22.15/day). We cannot even measure it for 35 days.**

## B5. The cheapest decisive test, and what to do tomorrow

Do not deploy $1–2k into any of the above. The binding constraint is n, and n is cheap here.

**The test (≈$300 at risk, 10 sessions, ~2 weeks):** quote **gas only**, **rungs ≥15¢ only**,
**flatten all residual at 80% of window at the touch**, and record the reward receipt and the
position P&L as **two separate ledgers**. That is the one experiment that measures the only
number that matters — *reward minus book* — with all three candidate gates applied at once. It
costs at most ~$100 in expectation if the hypothesis is wrong, and it either produces a
signable number in 10 days or it kills LIP for good.

Alongside it, keep the two cheap n-builders already running unchanged: volbook at $14.50/day
(session-capped) and streak at $4/market. Neither has earned capital; both are buying n at ~$0.

If the test comes back negative, the honest allocation for $1–2k is **cash** — $0/day beats
−$12/day, and the option to deploy later costs nothing.

## B6. Which CONCEPT file changes

**[[43 - THE MONEY GAME]] §7**, and it is the sharpest correction this file has taken.

§7 says *"Rewards are payment for BEING the book"* and *"reward earning = capital × time ×
proximity."* Both true, and both incomplete in the way that cost the money. Add:

**The subsidy must be measured against the VARIANCE of the book that earns it, not against the
capital.** Our verified rate is 2.5%/day on deployed — a spectacular number — attached to a
book with 3× that in daily noise. A subsidy smaller than the noise of its own carrier is
undetectable and unbankable no matter how large the rate looks. The operative statistic is
**reward per unit of position-book standard deviation**, and ours is 7.48 / 22.15 = **0.34**.
Below 1.0, the rebate cannot be distinguished from the book's noise within any horizon a
small account can survive.

And the scaling law, measured: **reward scales with BREADTH (rung programs), the book's
variance scales with DEPTH (capital).** §7 already says breadth beats depth once share
saturates; what it does not say is the consequence — **adding capital to a saturated rung buys
100% of the variance and 0% of the subsidy.** That is precisely what "$1–2k into LIP" would do.

Also fold into §2: *"fills are the cost of goods"* holds only where inventory can net. Measured
here: **28.1% of acquired contracts ever netted**, and 47% of those that did were bought for
more than $1.00 combined. Where the netting rate is that low, fills are not cost of goods —
they are the product being purchased, and the strategy is long inventory wearing a maker's
name.

**No change to [[07 - Overfitting & Validation Discipline]]** — every knob in this appendix
(rung floor, exit fraction) behaved exactly as §07 predicts an overfit knob behaves:
non-monotone across settings, dominated by 2–3 observations, significant nowhere. §07 caught
all of them. It is working.

---

# APPENDIX C — BACKTEST of the five-designer converged design (2026-07-28 night)

## HEADLINE, STATED FIRST

**The design as specified LOSES MONEY on our own tape: −$23.70 over 48 eligible markets,
t = −2.24, and it gets WORSE with the top 3 markets removed (−$28.70, t = −2.92).**

**The single rule causing the loss is RULE 7** — the +2¢ resting exit. Deleting rule 7 and
letting inventory net instead flips the tape from **−$23.70 to +$16.60**. Rule 7 was intended
to be "simultaneously the exit and the netting leg"; on this tape it is neither, because it
fires *before* the opposite side can fill and thereby destroys the netting it was meant to
provide. Netted contracts: **0 with rule 7, 230 without it.**

And even the fixed variant is not a green light: **+$16.60 is three markets. Ex-top-3 it is
−$9.20 (t = −0.55).**

## C0. Method and its honest limits

- Universe: the 66 settled markets we actually traded, with 27,181 real 1-minute bid/ask bars.
  Rule 4 (window ≤ 24h) leaves **48**. Rule 4 excludes DXY (38h), all metals dailies
  (25.8–36h — **volbook's markets fail rule 4**), all mentions, PGA, ALBUM.
- **FILL MODEL (a model, not truth — the main soft joint in this backtest).** Quotes are formed
  from bar *t*'s closing touch and tested against bar *t+1*'s trade high/low. A resting bid
  fills only when the tape trades **strictly through** it, which is the conservative queue
  assumption for a joiner. Sensitivity reported throughout: non-strict (fill on touch) moves
  the full design from −$23.70 to −$5.60 and the fixed variant from +$16.60 to +$28.30.
  **The design is never profitable under the conservative model and never significant under the
  generous one.**
- Rule 5 ("free-ride the 1000-contract qualification") is proxied by **open interest ≥ 1000** at
  the bar — candlesticks carry no resting depth. **UNVERIFIED mapping** to the real rule.
- Rules 2 (join best), 3 (one rung/side), 8 (never take) are *baked into the simulator's
  construction* and cannot be ablated from this tape — they are assumptions, not results.
- Size fixed at 10 contracts/leg so per-contract economics are readable; rule 9's sizing is a
  scale parameter that does not change them.

## C1. (A) THE LAST 24 HOURS, ORDER BY ORDER

31 rule-4-eligible markets settled on 2026-07-28. **6 of 31 ever filled.**

**Full design (with rule 7):** day total **−$12.60**

| market | settles | orders | P&L |
|---|---|---|---|
| KXAAAGASD-4.100 | NO | buy NO 10@26¢ → sell 28¢; buy NO 10@27¢ → sell 29¢ | +$0.40 |
| KXAAAGASD-4.105 | NO | 4 round trips, all exited at +2¢ | +$0.80 |
| KXAAAGASD-4.110 | NO | **8 round trips at +2¢ (+$1.60), then buy YES 10@39¢ 21:59 → settles 0** | **−$2.30** |
| KXBTC15M-26JUL281915 | NO | 1 exit +2¢, then buy YES 10@28¢ → settles 0 | −$2.60 |
| KXNDQHUD-26JUL281100 | YES | buy NO 10@48¢ 08:41 → settles 0, never exited | −$4.80 |
| KXUST10AD-T4.61 | NO | buy YES 10@41¢ 09:32 → settles 0, never exited | −$4.10 |

The 4.110 line is the design's whole pathology in one market: **eight consecutive +2¢ wins
(+$1.60) erased by one un-exited fill (−$3.90).**

**Same day without rule 7 (hold / let it net):** day total **−$0.20**

KXAAAGASD-4.105 shows the netting path working exactly as rules 1/3/6 intend — three pairs at
combined costs of 97¢, 95¢ and 87¢ (captures +3¢, +5¢, +13¢), then a residual NO@42¢ settling
at 100 for +$5.80. That is the mechanism the design was built for, and rule 7 prevents it.

## C2. (B) FULL 48-MARKET REPLAY

| | full design | without rule 7 |
|---|---|---|
| **net P&L** | **−$23.70** | **+$16.60** |
| per-market mean / sd / t | −0.494 / 1.53 / **−2.24** | +0.346 / 3.22 / **+0.74** |
| **ex-top-3** | **−$28.70 (t = −2.92)** | **−$9.20 (t = −0.55)** |
| fills | 95 | 63 |
| exits at +2¢ | 83 (87.4%) | 0 |
| **contracts netted** | **0** | **230** |
| contracts held to settlement | 120 | 170 |
| **mean pair cost** | n/a (no pairs) | **80.3¢** |
| **pairs costing > $1.00** | 0 | **0 of 23** |
| markets that ever filled | 21 of 48 | — |
| capital at risk (concurrent) | **~$3.50–$10** | ~$3.50–$10 |

**The crux arithmetic.** 83 of 95 fills exited at +2¢ = +$16.60. The 12 that did not settled
for **−$40.30**, a mean loss of **33.6¢ per un-exited contract**. Breakeven therefore needs an
exit rate of **94.4%**; the tape delivered **87.4%**. **A 2¢ target is too small for the tail it
leaves behind** — this is rule 7's defect stated as a number, and it is not a tuning problem
(+1¢ → −$23.20, +3¢ → −$21.70, +5¢ → −$13.30; all negative).

**Two structural findings that no ablation can fix:**

1. **Rule 1's 20–50¢ band makes two-sided quoting nearly unreachable.** our_yes_leg +
   our_no_leg = 100 − spread ≈ 98–99¢. For both to sit at ≤50¢ the mid must be within ~1¢ of
   50. Measured: **both legs quotable in 2.16% of bar-minutes**; exactly one leg 36.5%; neither
   61.3%. The design is therefore **not a two-sided market-making book** — it is a one-sided
   "buy the side that is cheaper than 50¢" book, which is a directional strategy with a
   rebate.
2. **Rule 9's "20–30 markets simultaneously" is not available.** Simultaneously-quotable
   markets in our 48-market universe: **median 1, p90 2, max 3.** Capital deployable at 10
   contracts/leg is therefore **~$3.50–$10 concurrent, not $300** — and not $1–2k by three
   orders of magnitude. The reward assumption cannot be reached because the capital cannot be
   made to rest.

**Reward side under the band.** The design rests at ~35¢/contract; the losing book rested at
5.6¢ on its cheap residual. If reward score ∝ contract count (rule 2's own premise, score =
size × 0.5^ticks), the same capital buys **6.25× fewer contracts** ⇒ **$37/day → $5.92/day**.
If reward ∝ capital, $37/day is unchanged. Truth is between and is **UNVERIFIED**.

**A flag on the input.** $37/day is a popover estimate. The only verified reward receipt on
this account is **$7.482/day**. $37 / $7.482 = **4.9×** — squarely inside the **4–8× optimism
band the audit already documented for modelled accruals** (`work/audit-2026-07-28.md` §2a).
I would not plan on $37.

## C3. (C) WHICH RULES CARRY, WHICH ARE DECORATION

Ablated one at a time from the full design (48 markets):

| rule removed / changed | P&L | Δ vs full design | verdict |
|---|---|---|---|
| **R7 +2¢ exit → hold** | **+$16.60** | **+$40.30** | **the rule is the loss — delete it** |
| R5 free-ride (OI filter off) | −$75.10 | **−$51.40** | **carries the most genuine improvement** |
| R1 band → 20–80 | −$39.00 | −$15.30 | carries |
| R1 band → 15–85 | −$36.00 | −$12.30 | carries |
| R1 band → 5–95 (none) | −$32.50 | −$8.80 | carries |
| R4 dailies (all 66 mkts) | −$29.50 | −$5.80 | carries, mildly |
| **R6 joint sum guard off** | −$28.50 | −$4.80 | **near-decoration** |
| R2, R3, R8, R9 | — | — | **untestable from this tape** |

**Rule 6 is the surprise, and it matters for what you deploy.** With netting live (rule 7 off),
guard 98¢ gives +$16.60 and guard 200¢ (i.e. no guard) gives +$13.30 — a $3.30 difference —
and **zero pairs cost more than $1.00 under either setting.** The 47%-of-pairs-above-$1.00
disaster in Appendix B was **not** caused by the absence of a joint-sum guard; it was caused by
**ladders repricing independently** (many rungs per market, each chasing best). **Rules 2 and 3
already kill it.** Rule 6 is a cheap belt-and-braces, not the fix it was billed as. If rule 6
alone were expected to carry the improvement — it does not.

So the honest ordering of what carries: **R5 ≫ R1 > R4 > R6**, with **R7 actively harmful**,
and R2/R3/R8/R9 unfalsifiable here.

## C4. (D) THE HONEST POWER STATEMENT

**What this backtest can legitimately claim:**
- The design **as specified loses** on our tape, conservatively modelled, at t = −2.24, and the
  loss **strengthens** ex-top-3 (t = −2.92). A losing result that survives outlier removal is
  the one direction where n=48 is adequate. **This is a real result.**
- Rule 7's mechanism is refuted arithmetically, not statistically: 87.4% realised exit rate
  against a 94.4% breakeven, and negative at every tick setting from +1¢ to +5¢.
- Rule 1's band and rule 9's concurrency assumption are **structurally** incompatible with the
  order books we actually face (2.16% two-sided availability; median 1 concurrent market).
  These are measurements of the venue, not of the strategy, and n is ample.

**What it cannot claim:**
- **That the fixed variant (drop rule 7) makes money.** +$16.60 at t = +0.74 is not
  significant, and **ex-top-3 it is −$9.20 (t = −0.55)** — the entire positive is
  KXBTC15M-26JUL232345 (+$9.10), KXBTC15M-26JUN241545 (+$8.80), KXAAAGASD-4.105 (+$7.90).
  Per note 07 that is a null result, not a strategy.
- Anything about rules 2, 3, 8, 9 — they are simulator assumptions here.
- Anything about the reward number. One estimated input ($37/day), one verified receipt
  ($7.482/day), 4.9× apart, and a band restriction whose reward cost ranges from 0% to 84%
  depending on an unresolved scoring model. **The reward side of this backtest is UNVERIFIED
  end to end.**
- Out-of-sample anything: this is a 48-market in-sample replay on the same tape that generated
  the design's premises. It is a **placebo test that the design failed**, which is informative;
  it is not a validation that anything passes.

**Net-of-everything, best case, stated so it cannot be misread:** fixed variant +$16.60 over a
~6-day tape = **+$2.77/day** of position P&L (not significant, three markets), plus rewards of
**$5.92–$37/day** (unverified, and the verified analogue is $7.48). Against a book whose
measured daily sd is $22.15 (Appendix B). **Nothing here clears the bar for deploying $1–2k.**

## C5. RECOMMENDATION

**Do not deploy this design tonight.** Two changes are required before it is even testable:

1. **Delete rule 7.** It is the loss, measured at +$40.30 of damage on 48 markets. If an exit is
   wanted, it must clear the 94.4% hurdle its own tail sets — a 2¢ target does not.
2. **Fix rule 1 or abandon two-sidedness honestly.** A 20–50¢ band on both legs is
   self-contradictory at real spreads (2.16% availability). Either widen to 20–80¢ on *our*
   leg — which is what actually permits both sides — or state plainly that this is a one-sided
   directional book with a rebate and size it as such.

Then run it as the **$300 / 10-session gated experiment from B5**, with reward and position
P&L in two separate ledgers. That remains the cheapest decisive test, and it is now cheaper
because C4 has already eliminated one candidate design for $0.

Keep rule 5 (free-ride) in every variant — it is the strongest single positive on the tape
(+$51.40) and it is the one rule whose logic does not depend on any contested model.

## C6. Which CONCEPT file changes

**[[43 - THE MONEY GAME]] §2 and §3.**

**§2** ("capital has states… the exit's price of impatience is spread + taker fee; its price of
patience is carry") needs the term this backtest paid for: **an exit target is a bet on the
distribution of the path, and it must be priced against the tail it leaves behind.** A +2¢
resting exit converts a symmetric position into a 2¢-up / 33.6¢-down payoff and therefore
requires an 94.4% hit rate to break even. **A small profit target on an un-hedged position is
not risk reduction — it is selling a deep option for two cents.** Any exit rule must state its
breakeven hit rate before it is deployed; the arithmetic is one line and it would have killed
rule 7 on paper.

**§3** (correlation / the settle source is the position) gains the geometric identity that
killed rule 1: **on a binary, your two legs are not independent instruments — their prices sum
to 100 minus the spread.** A band applied symmetrically to "our leg" on both sides is therefore
a constraint on the *mid*, not on the legs, and a 20–50¢ two-sided band silently means "quote
only when the mid is 50¢." Any two-sided quoting rule must be stated as a constraint on the mid
and the spread, never as a constraint on each leg independently.

**No change to [[07 - Overfitting & Validation Discipline]]** — again it did the work. Every
positive in this appendix (+$16.60, +$28.30 non-strict) dissolved on ex-top-3 exactly as §07
predicts, and the only durable finding was the negative one that *strengthened* under outlier
removal. That asymmetry — losses that survive outlier removal are real, gains that don't are
not — is worth naming explicitly in §07 if it is not already there.
