# 52 - THE LIP STRATEGY — settled with Ryan, 2026-07-29 ~21:30 MT

> The strategy, every decision behind it, and the reasoning that produced each one. Written at
> the end of a long alignment dialogue in which Ryan derived the strategy top-down and I was
> corrected repeatedly. **Supersedes the strategy half of [[47]], [[50]] §4 and [[51]].** Where
> this note and any earlier note disagree, this one is newer and was argued through.
>
> Provenance is marked on every number: **MEASURED TONIGHT** = pulled first-hand from Kalshi's
> API on 2026-07-29 between 20:15 and 21:00 MT · **MEASURED (prior)** = a previous session's
> figure, inherited, not re-verified, treat with suspicion per Ryan's standing instruction ·
> **DERIVED** = arithmetic · **OPEN** = not known.

---

## 1. THE STRATEGY, IN ONE PARAGRAPH

Rewards are paid for *being the book*: `credit = pool × our_score_share ÷ 2 × presence`, per
market per program period, forfeited entirely below $1.00. Profit = rewards − position P&L.
Positions are the tax on collecting the subsidy, and on 2026-07-28 the tax ate the whole thing
because $300 sat in two correlated settle sources. So: **~35–40 rungs, one per uncorrelated
settle source, each sized to clear $1 with margin against that rung's own rival score, held
present by re-posting after fills, in markets that settle soon enough that a fill is not a
multi-month capital lock.** Ruin is controlled by breadth, not by a price floor. The price
floor exists only where EV is measurably negative.

---

## 2. THE SETTLED DECISIONS

| # | decision | why |
|---|---|---|
| D1 | **Finish v5; do not rebuild.** | The gap between the code and this strategy is smaller than notes 50/51 imply. Tonight's four commits already produce 24 orders in 24 markets at ~$9.92 (against a fake exchange). Rebuilding discards working fills/recovery/rails machinery. |
| D2 | **Ceiling stays $300.** Raise only after it is proven. | Ryan. Prove the shape before scaling it. |
| D3 | **N ≈ 35–40 settle sources, not 50.** | Supply, MEASURED TONIGHT: only ~38 clusters both settle inside 7 days and can clear $1 at half presence. Beyond ~40 the pool per rung collapses (ρ rank-50 is a third of rank-30). Fifty is reachable on paper and the last dozen are not worth their capital. |
| D4 | **The eligibility filter is the MARKET'S SETTLEMENT DATE (≤7 days), not the program window.** | The two clocks were conflated. Program window = how long we can earn. Market settlement = how long a fill traps the money. `KXGDPYEAR-32` has a beautiful 123.9h program on a market settling in 2032. MEASURED TONIGHT: of the top 40 clusters by ρ, 24 settle >30 days out, median close 94 days. |
| D5 | **Cap per SETTLE SOURCE = ceiling ÷ N ≈ $8.** Not per rung, not per order. | The correlated unit is the settle source. Both legs of a rung and every rung of one event are one bet. This is the cap that was missing when nine gas rungs became one −$80 position. |
| D6 | **Lot = floor-clearing size. Per-rung budget = lot × (1 + refills), refills = 3.** If the budget cannot fund lot + refills, take FEWER RUNGS, never smaller lots. | Below floor-clearing size a rung earns **zero**, not less — so the lot cannot shrink freely. Ryan's construction, and it is better than the alternative I proposed (inflating the order to compensate for lost presence), because a 3× order holds 3× the inventory on its first fill whereas a 3× reserve holds one lot's worth and re-posts. Same presence, a third of the inventory risk. |
| D7 | **Credit target = $1.00 × 1.5–2.0 margin.** | Absorbs pool variation, rival drift, and presence gaps. Sizing to exactly $1.00 is sizing to the cliff edge; a rung landing at $0.95 pays the same as one landing at zero. |
| D8 | **φ is interpreted charitably (low) and then MEASURED.** | Ryan. But charity is not free: if φ is low and reality is high we lose on both sides at once — less presence *and* more inventory. So it pairs with a fast per-venue kill, not an end-of-day review. |
| D9 | **One-sided quoting for now. Two-sided later, with a ≤98¢ joint-sum guard.** | Two-sided is roughly reward-per-risk neutral and its lucky pairings are riskless — but both legs are the same settle source, so at a fixed ceiling funding both halves our breadth, and breadth is the entire ruin control at $300. Revisit when capital is not the binding constraint. |
| D10 | **Price floor comes from measured EV only. Ruin is controlled by breadth.** | A price floor derived from ruin is `p_min = k/bankroll`, refuted ([[47]] §5/§6). DERIVED: reward per unit of position risk ∝ `1/√(p(1−p))`, which is symmetric about 50¢ and nearly flat away from it — so price is not the efficient ruin lever. N is. |
| D11 | **The variance instrument moves INTO the planner.** Today it is a rail only. | The allocator prefers cheap (`gross ∝ 1/p`) so it plans a cheap book; `place()` then refuses it; and `place()` returning False arms no degrade, so the slot re-offers the same refused order forever. This is the plan-⊄-rail deadlock the codebase has already named twice (D2, NEW-1). |
| D12 | **A rung's size is LOCKED for the period it is clearing.** | The allocator re-solves every second and can shrink a rung mid-period, which forfeits the entire $1 rather than a slice. Known-unfixed, biggest single earnings leak. |
| D13 | **Small-and-refill first; per-venue switch to big-and-once when `φ·q·hours_left < 1`.** | See §5 — the two are exactly symmetric in expected earnings and the tiebreaker is the shape of being wrong. |
| D14 | **The φ-by-price-band capture starts before anything else is built.** | It is public data, costs no money, accumulates while the build proceeds, and it is the binding input to D10 and D13. |

---

## 3. WHAT WAS MEASURED TONIGHT (first-hand, `/incentive_programs`, 8,000 rows, 3,566 live and unpaid)

### 3a. The pool question — SETTLED

**`period_reward` is the pot for that program's OWN window, paid once at the window's end.**
Three pieces of evidence:
1. One market can carry **sequential** programs. `KXWNBAMENTION-26JUL28INDSEA-CARE` shows a
   35.7h program with `paid_out: true`, immediately followed by a fresh 9.5h program with its
   own $100. A per-day pot would not need re-issuing at variable window lengths.
2. `KXGDPYEAR-32` — GDP in 2032 — carries a **123.9h** program. Nobody is paying $100 over a
   decade; the market is long-dated, the program is five days. Longest window observed: 502h.
3. `paid_out` flips at the window's end, and our credits were keyed to the program's END day.
   **This explains Ryan's observation that payouts arrive overnight regardless of settlement:**
   our gas and treasury programs end overnight. His observation is predicted by this model, not
   evidence against it.

Consequence: a 124h program's $100 lands five days out, not tomorrow. Real cash-flow cost of
long windows, and what `PAYOUT_HORIZON_H` exists to price.

### 3b. The universe, by the two different clocks

| clusters where… | ≤24h | ≤48h | ≤3d | ≤7d | ≤30d | any |
|---|---|---|---|---|---|---|
| the **program window** is that long | 2 | 11 | 18 | 182 | — | 216 |
| the **market settles** within that | 4 | 16 | 36 | 54 | 67 | 216 |

**At the filter that was live in the code (program window ≤48h) the entire eligible universe was
ELEVEN clusters, and at ≤24h it is TWO — gas and the treasury curve.** Ryan's 30 uncorrelated
settle sources was arithmetically impossible under our own filter. We did not fail to diversify;
the code deleted the board and then diversified across what was left.

Of the settlement-limited sets, clusters that can clear $1 (target ≤25% of one side's remaining
pool): **≤3 days — 26 at full presence, 20 at half. ≤7 days — 44 at full, 38 at half.** That 38
is where D3's number comes from.

### 3c. ρ decay inside the ≤7-day set

| rank | 1 | 10 | 20 | 30 | 40 | 50 |
|---|---|---|---|---|---|---|
| ρ ($/h) | 6.26 | 1.91 | 0.86 | 0.70 | 0.60 | 0.23 |

With no settlement limit the curve is nearly flat (rank-50 is 82% of rank-30) — but those are the
multi-year markets.

### 3d. We were capital-limited, never pool-limited

24-hour-collectable pool: **the four true-daily clusters hold $4,135 across 123 markets.** The top
38 clusters hold $30,644 across 1,593 markets. **We captured $70 — 1.7% of the daily clusters
alone.**

Therefore: concentrating in the dailies bought **no extra reward** and cost $195 of correlated
position loss. Running it longer "using time instead of breadth" would have earned about the same
$70/day at a 60%-per-day chance of losing the whole deployed book (see §4).

**Honest correction to my own instinct: breadth is not free.** Per market per day the daily
clusters average ~$34 of pool against ~$19 across the top 38 — roughly a **40% haircut per rung**
for moving from N_eff ≈ 2 to N_eff ≈ 38. Worth it without hesitation, but it is a real price.

---

## 4. THE RUIN ARITHMETIC (DERIVED)

$300 deployed, all converted to positions, N independent settle sources:

| N | avg price | per rung | CV | P(all lose) | P(lose ≥$150) | median P&L |
|---|---|---|---|---|---|---|
| 30 | 6¢ | $10.00 | 72% | 15.6% | 15.6% | +33 |
| 30 | 12¢ | $10.00 | 49% | 2.2% | 11.0% | −50 |
| 30 | 15¢ | $10.00 | 43% | 0.8% | 15.1% | −33 |
| 30 | 25¢ | $10.00 | 32% | 0.0% | 3.7% | −20 |
| 40 | 15¢ | $7.50 | 38% | 0.2% | 13.0% | 0 |
| 50 | 15¢ | $6.00 | 34% | 0.0% | 4.6% | −20 |

Closed form for the variance tolerance: `V ≤ 0.25` at N equal-weight rungs needs
**average price ≥ 1/(1 + N/4)** — 12¢ at N=30, 9¢ at N=40, 6¢ at N=63.

**Three things this table must be read with:**
- It is the **fully-converted** bound. Resting collateral is not at risk; it returns on cancel.
  If φ is genuinely low, most of the $300 never becomes a position and the tail is much smaller.
- Position P&L is noise around zero; **rewards are the edge.** A typical day is positions between
  −$50 and +$33 plus whatever credits land — net positive. The 2% day is −$300 plus credits.
- The **median day loses money even at fair value.** At N=30/12¢ the median is 3 hits where 3.6
  were expected: −$50. The mean is carried by rare large wins. This is normal for a longshot book
  and it is what a daily loss limit will fire on if it is not set with this in mind.

**Daily-only is not viable (Ryan's question, answered).** Four clusters settle within 24h. To
match the 2.2% all-lose of 30@12¢ with four independent sources needs `(1−p)⁴ = 0.022`, i.e. a
**62¢ average price**, where clearing $1 costs a fortune in contracts. At 12¢ with four sources
the chance all four lose in a day is **60%**. More rungs does not help — thirty gas rungs are one
bet. Time averages across days but the within-day variance is eaten every day, and the drawdown
halt would fire long before the average arrived.

---

## 5. SMALL-AND-REFILL vs BIG-AND-ONCE (Ryan's question, answered)

The answer hinges entirely on whether **fill arrivals scale with our resting size**:

- **Nibbled** (fill rate ∝ our size): a lot of size `q` survives `1/(φq)` hours, so it buys
  `1/φ` contract-hours **regardless of q**. Splitting the budget into (1+n) lots buys (1+n)× the
  presence. **Small-and-refill wins by (1+n).**
- **Swept** (a taker eats the rival depth ahead of us, then all of ours; arrival rate independent
  of our size): contract-hours `= q × T`, linear in size. **Big-and-once wins by (1+n).**

Exactly symmetric. Neither dominates; the regime decides. My prior is **swept** — we rest behind
~400 contracts of rival depth, so only a 400+ contract sweep reaches us and it takes everything.

**We start small-and-refill anyway (D13), for three reasons that override the prior:**
1. The $1 floor pins the minimum lot, so the lot cannot shrink freely — (1+n) comes from the
   budget, not from choice.
2. It is a sequential experiment: the first fill measures φ at the cost of one lot, not the rung.
3. **The two errors are the same size and different in kind.** Small when φ is low = underearn up
   to 4×, recoverable next window. Big when φ is high = the rung converts to inventory on the
   first sweep and we hold principal. That second failure is the one that cost $195.

Switch rule once φ exists, evaluated **per venue**: big-and-once wins when
`φ × q × hours_left < 1`.

---

## 6. CORRECTIONS TO STANDING DOCTRINE

| what the corpus said | status |
|---|---|
| "A treasury daily returns ~11–40× a weekly per capital-hour; dailies are the venue." ([[47]] §4, `config.PAYOUT_HORIZON_H`) | **NOT SUPPORTED.** MEASURED TONIGHT: gas 6.26 $/h on a 16h window, two 166h programs at 6.03 $/h. The variation is **pool size**, not window length. Dailies looked special because the window filter meant dailies were all we could see. |
| `MAX_WINDOW_MULT = 2.0` (program window ≤48h) as a capital-efficiency guard | **It was the cause of the correlation disaster.** Eleven clusters, two at ≤24h. Replace with the settlement-horizon filter (D4). |
| Matched pairs as a capital-efficiency argument for two-sided quoting | **DEAD** (Ryan). Matched capital only returns if the second leg fills, and it usually does not. What survives is only that each side is scored separately. |
| Every floor-clearing / cliff-clearing number in the corpus, including mine | **ALL ASSUME 100% PRESENCE.** `floor_clearing_size` has no presence term; `cliff_clearing_q` assumes we hold the share for every remaining hour. The only headroom is a 1.5× margin, which covers a 33% shortfall, not the 90% we actually ran. This is the sizing gap Ryan has been pointing at for days. |
| "Go cheap and wide" (my suggestion earlier in this same conversation) | **WRONG.** The cheap end is crowded — MEASURED (prior): rival score 6,618 at 1–5¢ against 403 at 11–20¢ — so buying $1 of credit at 5¢ costs ~$10.23 against ~$1.87 at 15¢. Cheap-and-wide fails on cost-to-clear before ruin gets a vote. |
| "Breadth is free in reward terms" (my instinct) | **WRONG, ~40% haircut per rung.** See §3d. Still the right trade. |
| Pools might be per-day rather than per-window (Ryan's suspicion) | **RESOLVED as per-window, paid at end.** See §3a. His overnight-payout observation is explained by it. |

---

## 7. THE OPEN MEASUREMENTS, in order of value

1. **φ AND EV BY PRICE BAND, from the public tape.** Book snapshots + trade tape on LIP-eligible
   markets → fills per hour per unit of resting depth, bucketed by price. Answers three things at
   once: whether the empty 11–20¢ band is empty because the bots are naive (free money), because
   φ is high there (we would be the fish), or because there is no supply there; and which of §5's
   two regimes we are in. **Our own tape cannot answer it — 98.4% of our contracts were at ≤5¢,
   so we have zero observations in the band we are proposing to trade** ([[49]] R4). No money at
   risk. Starts first.
2. **Re-derive the cheap-end EV bias ourselves** from raw Kalshi settlements. [[47]] §3's n=8,240
   table is the single most decision-relevant number in the strategy and it is inherited.
3. **`d` — adverse selection**, mark 60s after fill minus fill price, per venue. Already modelled
   (seeded at 7¢, capped at p) and already collected; never computed. Ryan's position is that
   placement is a dart throw and therefore unbiased — correct about placement. The claim is about
   **acquisition**: we fill when someone chooses to sell to us, so the filled subset is not a fair
   sample of the orders. Magnitude unknown; `d` is exactly that magnitude.
4. **Does Kalshi release collateral when offsetting legs net?** One $1 test. Decides how much
   two-sided quoting is worth when D9 is revisited.

---

## 8. THE BUILD ORDER

0. Start the φ-by-price-band capture (D14).
1. Variance instrument into the planner (D11).
2. Lock rung size for the clearing period (D12).
3. Cap per settle source = ceiling ÷ N (D5); presence-reserve sizing (D6, D7).
4. Swap the program-window filter for the settlement-horizon filter (D4).

Everything above is inside v5 (D1). Ceiling $300 (D2).

---

## 9. WHICH CONCEPT FILES CHANGE

- **[[43 - THE MONEY GAME]] §6** — "read every horizon from the market's own open/close" gains its
  missing half: there are TWO horizons, the program window and the market settlement, they are
  weakly coupled, and confusing them is what produced a two-cluster book.
- **[[47]] §4 / [[51]] §4** — the daily-supply and dailies-are-better claims are superseded by §3
  above. The `ρ` framing also understates a cluster: a cluster holds many markets, each with its
  own pool and its own $1 floor, so per-program ρ is not per-cluster capacity.
- **[[49]]** gains a case: R4 ("the sample must contain the configuration you are proposing") was
  satisfied *retroactively* — the whole 11–20¢ plan rests on a band our tape has never observed,
  and the capture in §7.1 exists because of that rule.
- **[[23]] Part V** — the fate sentence is now writeable and should be written from the settlement
  filter: *a position acquired by this system ends by settling within 7 days, worth $1 or $0 at a
  price we chose, against a subsidy of ≥$1.00 per market-period.*
