# Verify: LIP feed + gas markout + gas microstructure (DEPTH-LIP, 2026-07-27)

Lane: burst-2 DEPTH-LIP, following `work/lane-HOUSE-FEE-jul27.md`'s TOP DOOR.
Method: independent re-pull of the public feed (no auth), adversarial re-run of the on-disk
markout, live book sampling of the 17 gas rungs, and — the thing that changed everything — the
**actual CFTC rule filing containing the exact scoring algorithm**.
Read-only throughout. No orders placed. Scouts: 2 (markout adversary, H18 census).

---

## 0. THE FIND THAT REFRAMES THE LANE

`https://www.cftc.gov/sites/default/files/filings/orgrules/26/02/rules02112639183.pdf`
("KalshiEX LLC – Amendment to August 2025 Liquidity Incentive Program", filed 2026-02-11,
**effective 2026-02-28**) contains the **complete, binding, mechanical LIP algorithm**. The lane
was reasoning about G2/G3 as unknowns that only a probe could settle. They are settled in writing.

Verbatim mechanics:

1. **Snapshot** = one per second, exact time drawn from a random uniform distribution (nonpublic).
2. **Snapshots are EXCLUDED unless there is two-sided liquidity** — "resting orders sufficient to
   meet the Target Size on **each** side of the market". (This exclusion is the Feb-2026 amendment;
   it did not exist in the Aug-2025 program.)
3. **Qualifying set, per side:** Reference Price = the highest bid on that side, **"if it exists and
   is less than the highest possible price"**. Walk down from the Reference Price accumulating size;
   every level touched joins the Qualifying set; stop once cumulative ≥ Target Size. **If bids run
   out before Target Size is reached, the Qualifying set is CLEARED** (side fails).
4. **Score(bid) = DiscountFactor^(ReferencePrice − Price(bid)) × Size(bid)**. Distance is measured
   in ticks **from the same-side best price** — *not* from the midpoint.
5. **Normalized per side:** Normalized(bid) = Score(bid) ÷ Σ Score(all qualifying bids on that side).
   SnapshotLPScore(user) = Σ normalized-yes + Σ normalized-no. **Max 2.0; the total across all users
   on an included snapshot is exactly 2.0.**
6. TimePeriodScore(user) = Σ_snapshots score(user) ÷ Σ_snapshots Σ_users score. Payout = that × Time
   Period Reward, paid if ≥ $1.00, rounded down to the cent.
7. Caps: Time Period ≤ 31 days; **100 < Target Size < 20,000**; Discount Factor ≤ 1.00;
   **Time Period Reward ≥ $10 and ≤ $1,000 per calendar day** encompassed in the Time Period.
8. Eligibility exclusions: Kalshi affiliates; **members with an executed Market Maker Agreement**;
   IBs/FCMs and their customers. (No "non-US" exclusion in the filing — that's help-centre wording.)
9. **Program runs until the earlier of September 1, 2026 or Kalshi amending/terminating.**

### What this settles, analytically, for free

| lane gate | status | resolution |
|---|---|---|
| **G2** (distance from same-side best or midpoint?) | **CLOSED** | Same-side best, in ticks, DF^N. **H19's cheap-side capital-efficiency law is intact, not inverted.** |
| **G3** (target_size=1000 semantics) | **CLOSED** | It is an **aggregate two-sided gate**, not a per-user minimum. 100 lots score fine. But if either side's *total* resting size fails to reach 1,000, **the snapshot is dropped for everyone**. |
| **G4** (crowding) | **quantified, not closed** | Denominator is the qualifying set only, normalized per side. See §4 sensitivity. |
| **G1** (unit) | **narrowed to near-certainty, still needs one payout** | See §1. |

### And it settles the size doctrine the opposite way from R153
R153's ledger says "SIZE BAND 100-20,000 CONTRACTS (1-lot probe scores ~nothing)". The filing says
100-20,000 is the range of the **Target Size parameter Kalshi sets per market**, not a per-user size
requirement. **A 1-lot resting order scores, and scores well, on a thin book** (§4). R153's mechanics
paragraph should be corrected.

---

## 1. LIP FEED RE-VERIFICATION (independent full re-pull)

`GET /trade-api/v2/incentive_programs?limit=1000` + cursor, no auth. 120 pages, curl (python
urllib fails on SSL cert verification in this env). Snapshot 2026-07-27 ~16:26Z.
Data: `scratchpad/lip/ip_all_fresh.json`.

| claim in lane-HOUSE-FEE | verified? | truth |
|---|---|---|
| "30,000+ programs, 2026-06-12 → 2026-08-15" | **NO — truncated pull** | **119,615 programs, 2025-09-18 → 2026-08-15** (unique ids, no dupes). The prior pull stopped at exactly 30,000 = 30 pages. |
| "liquidity 29,924 / volume 76" | **NO** | liquidity **97,297** / volume **22,318**. |
| — | *new* | **Every volume program has ended.** Max volume `end_date` = 2026-07-01T03:59Z. The Volume Incentive component is dead; only the liquidity component is live. |
| "discount_factor_bps = 5000 on 29,915" | **YES, and stronger** | 5000 on 91,825 of 97,297; **5000 on 100% of live programs**. Other values (2500/4000/1000/3000/500) are historical only. |
| "target_size_fp = 1000 on 29,298" | **partially** | All-time: 1000 (46,416) / **300 (41,463)** / 2500 / 250 / 200 / 500 / 5000 / 2000. Live: **1000 on 1,847, 300 on 49.** |
| "incentive_description: new_event 16,822 / blank 12,151 / long_dated 885 / series_lip 142" | **counts wrong, shape right** | blank 60,815 / new_event 30,795 / long_dated 5,545 / **series_lip 142** (the series_lip count is coincidentally exact: **74 KXRAIN + 68 KXAAAGASD**, and that is the whole set). |
| "**Program ends 2026-09-01**" | **YES — verbatim in the CFTC filing** | "continue until the earlier of September 1, 2026, or the date that Kalshi amends or terminates". Feed max `end_date` = 2026-08-15T14:00Z (programs are scheduled ~3 weeks out). **36 days left.** |
| "`period_reward` in units of $1e-4" | **YES, on four independent legs — but still not a payout observation** | see below |
| "**$32,434/day of live pool**" | **NO — rate/total confusion** | **$23,768/day** trailing-24h accrual; **$25,507/day** next-24h scheduled. |
| "KXAAAGASD 17 rungs × **$150** = $2,553" | **NO** | 17 rungs × **$100** = **$1,700 per 16h window**. |
| "KXAAAGASD 209 programs all-time" | **NO** | **316**. |
| "Kalshi pays the most, most often, in the weather hourlies" | **true all-time, FALSE right now** | KXTEMP*/KXHIGH*: 31,866 programs, $1.26M of $7.13M all-time spend (17.7%). **Live weather programs right now: ZERO.** The weather hourly LIP has been switched off. |

### G1 — the $1e-4 unit, four independent confirmations
1. **min(period_reward) over all 97,297 liquidity programs = 10,000 → exactly $1.00**, which is
   exactly the filing's minimum payout ("if the result is ≥ $1.00, the result is paid out"). A pool
   smaller than the minimum payout is nonsensical; a pool equal to it is the natural floor.
2. `period_reward = 1,000,000` (2nd-most-common value, 15,759 programs) → **$100**, matching the
   help centre's worked example verbatim: *"If you represent 20% of qualifying liquidity in a $100
   reward period, you earn $20."*
3. The filing caps the reward at **$10–$1,000 per calendar day**. At 1e-4, the median program of
   ≥12h duration is **$21.43/day** and **81.7%** land inside [$10, $1000]. At 1e-5 the median is
   $2.14/day and only 6.4% land inside — 81% of programs would be below the mandated $10 floor.
4. At 1e-3 the gas rungs would be $1,000 per 16h = **$1,500/day**, which **violates** the $1,000/day
   cap. 1e-3 is excluded by the filing, not by taste.

**Residual G1 risk: low but non-zero.** All four legs are inference. One observed payout closes it.

### Where the money actually is, live (time-weighted, next 24h, @1e-4)
KXRAIN $1,669 (40 mkts) · **KXAAAGASD $1,226 (17)** · KXMLBMENTION $1,211 · KXWNBAMENTION $1,210 ·
KXEARNINGSMENTION{PYPL $1,078, F $1,010, BA $943, SBUX $766, META $725, HOOD $685, CMG/MSFT $605} ·
KXTRUEV $770 · KXEURUSDAW $559 · KXFEDMENTION $453 (43) · KXH200MS $395 (141) · KXCOINBASE $333.
All-time top spend: KXMIDTERMMOV $294k · KXWCMENTION $191k · KXTEMPCHIH/DCH/LAXH/AUSH/NYCH ~$167k ea.
All-time liquidity spend: **$7,134,531 over 324 days ≈ $22.0k/day**.

### Reward readout (public, and faster than the help centre implies)
- Help centre: rewards are **not** real-time; "Final scoring occurs after a program ends, and payment
  follows in a later processing run. Timing can vary." UI paths: **Rewards → Current month /
  Lifetime rewards → reward details**, and **Account → Activity → Credits**.
- No public rewards endpoint exists (`/portfolio/rewards`, `/portfolio/credits`, `/portfolio/
  incentives`, `/incentives` all 404). `incentive_programs` accepts no `market_ticker` filter.
- **But `paid_out` is a public boolean on every program object, and it is fast.** All 330 completed
  KXAAAGASD programs read `paid_out: true`, including the 17 that ended **2026-07-27T03:59Z** — i.e.
  the flag flipped within ~12.5 hours of window close. **Poll `paid_out` for the scoring-complete
  signal, then read the dollar amount in the UI the same evening.** Probe turnaround is one day,
  not one week.

---

## 2. GAS MARKOUT — ADVERSARIAL RE-CHECK (the 12-13Z gate is DEAD; the effect is not)

Engine reproduced exactly (size field is `count_fp`; dedupe on `trade_id`; `trades_gas.jsonl.gz`
contains **only KXAAAGASD**, no KXAAAGASW). Scripts: `scratchpad/gasadv/`.

**Reproduction: exact.** ungated CW **−0.729¢** (n=96,395 / 2,300,194 contracts / 67 days);
12-13Z-gated **−0.875¢**. Trade-weighted is friendlier (−1.175 / −1.203); contract-weighting is the
conservative choice and the right one.

**Placebo (note-07 §"the rescuing gate must itself clear this note's bar") — the gate FAILS.**
- Hours 04–11Z contain **zero trades** (the market is closed), so only 16 of 24 two-hour placebo
  blocks are real.
- **12-13Z ranks 1 of 24** among all two-hour drops. Textbook argmax.
- Mean gated markout over all 24 placebo drops = **−0.719¢**, i.e. a *random* 2h drop buys +0.010¢.
  The real gate's +0.146¢ **is entirely the maximum of the sweep**. Range [−0.875, −0.176].
- Leave-one-hour-out: dropping 12Z → −0.837¢ (best), dropping 3Z → −0.365¢ (worst).

**Split — the gate-selection procedure loses money out of sample.**
- Best gate fitted on half 1 = **16-17Z**; applied to half 2 → −0.647¢ vs half 2's own ungated
  −0.703¢, i.e. **worse than not gating**. Best gate on half 2 = 11-12Z; applied to half 1 → −0.765¢
  vs −0.759¢ ungated, i.e. nil. **Gate selection does not generalise.**
- The 12-13Z cell itself is real but small: +1.943¢, adverse on only 38/63 days (60%), bootstrap CI
  [+0.678, +3.435], and it is **2.8% of contracts**. Removing its 3 worst days moves the ungated
  number only −0.729 → −0.774¢.

**Day-clustered bootstrap:** ungated **−0.729¢, 95% CI [−1.016, −0.438] — clears zero on its own.**
Gated −0.875¢ [−1.179, −0.577]. 52/67 days maker-positive ungated. **The gate is optional garnish.**

**Horizon × band (all ungated CW):** negative in all 9 cells; 8 of 9 CIs exclude zero.
h=60: −0.575/−0.584/−0.546. h=300: −0.729/−0.787/−0.672. h=1800: −0.473/−0.541/−0.416
(bands 0.10-0.90 / 0.20-0.80 / 0.05-0.95). h=300 mid-band is the friendliest of nine — mild
horizon cherry-picking, but the effect does not live on one cell.

### The mechanism story in the lane note is FACTUALLY WRONG — and the corrected one is better
`KXAAAGASD-26JUL28-4.110` metadata, pulled live: **open_time 12:00:00Z, close_time 03:59:00Z**
(= 11:59pm ET), **expected_expiration_time 14:00:00Z the NEXT day**, settlement_timer 300s.
The tape agrees exactly (first trades 12:00-12:09Z, last 03:51-03:58Z, 04-11Z dead).

- **12-13Z is the first two hours of a brand-new market, not an AAA print.** Markout by
  hours-since-open: +0h **+3.080¢**, +1h +0.651¢, decaying and turning strongly maker-favourable
  into the close: +14h −1.180¢, **+15h −2.008¢** (that last hour alone is 22% of all contracts).
  This is ordinary open-uncertainty / stale-quote behaviour.
- **The AAA print never happens during the session at all.** AAA publishes no committed update time
  (https://gasprices.aaa.com/about-aaa); the national average is a single daily refresh landing
  shortly after midnight ET ≈ **04:00-05:00Z**, *after* the 03:59Z close. Kalshi's own schedule
  corroborates: close 11:59pm ET, resolution expected 10:00am ET next day.
- No single clock time dominates the intraday jump distribution: mean |ΔP| is 3.0-4.6¢ across
  12:00-14:15Z and 1.4-1.7¢ overnight; per-day max-jump buckets are smeared across 12:00, 12:45,
  14:00, 14:15, 02:30 and 03:45.

**The lane's law survives in a stronger form.** It said "the settle value is revealed once, on a
schedule, so between prints flow is noise". The truth is cleaner: **during the entire 16-hour
session there is NO information arrival whatsoever — the determining print lands after the market
is closed.** That is why gas is the least toxic book we own. But the lane's *specific* claim
("12-13Z = post-AAA-print hours") is false, and the gate built on it was fitted to the open and then
rationalised.

### NUMBER TO STAKE
**−0.729¢/contract, 95% CI [−1.016, −0.438]**, n=96,395 fills / 2.30M contracts / 67 days, fee 0.
Do **not** size on −0.875¢. For planning, haircut to **−0.50¢** (the h=60 wide-band corner, CI
[−0.771, −0.296]), because h=300 mid-band is the friendliest of nine cells.
**Caveat that matters operationally: ~45% of the edge sits in the final two hours before the 03:59Z
close (−1.18¢ at +14h, −2.01¢ at +15h). A probe that is not live at 02:00-04:00Z will not see it.**

---

## 3. GAS MICROSTRUCTURE, LIVE (17 rungs, 45s cadence, 2026-07-27 16:28Z→)
`scratchpad/micro/books.jsonl` (221+ snapshots), `lipscore.py` = the CFTC algorithm implemented
verbatim, `probe_sim2.py` = size/placement/competition sweeps.

### 3a. Only 7 of 17 rungs are LIP-eligible at all
| rung | yes bid | yes ask | state |
|---|---|---|---|
| 4.070 | 99 | — | **pinned**: yes bid at the 99¢ cap ⇒ no NO bid can rest above it (would need a yes ask >99¢). Permanently one-sided. |
| 4.075 | 98 | — | one-sided, **revivable** (a NO bid at 1¢ rests legally) |
| 4.080 | 99 | — | **pinned** |
| 4.085 | 98 | 99 | NO side only 25 lots < Target 1000 ⇒ excluded, **revivable** |
| 4.090–4.120 (7 rungs) | 97/91/68/40/30/4/1 | +1..+6 | **two-sided and qualifying, 100% of sampled snapshots** |
| 4.125–4.150 (6 rungs) | — | 1 | **pinned**: yes ask already at the 1¢ minimum tick ⇒ no yes bid can rest below it. Permanently one-sided. |

**Consequence: ~$1,000 of the $1,700/window gas pool sits on rungs where no snapshot can ever be
included.** Whether that money is simply unpaid, or whether the `paid_out: true` flag on the pinned
rungs means something else, is a live question the probe answers for free.
*(This depends on reading "the highest possible price" as 99¢, the highest valid Kalshi limit price.
Under the permissive reading — 100¢ — rungs 4.070/4.080 and 4.125-4.150 are still pinned by the
crossing constraint, so the conclusion holds either way; only who gets paid on them changes.)*

### 3b. The qualifying set is astonishingly thin — this is the whole edge
Per-side score totals on the live mid rungs (Σ DF^N × size over the qualifying set), 16:35Z:

| rung | yes ref | yes top-level size | **yes side total score** | no ref | no top size | **no side total score** |
|---|---|---|---|---|---|---|
| 4.100 | 68¢ | 35 | **60.5** | 31¢ | 0.16 | **5.2** |
| 4.105 | 40¢ | 24 | **24.9** | 59¢ | 50 | **73.2** |
| 4.090 | 96¢ | 44 | **155.6** | 2¢ | 1,041 | **1,041** |

The books hold 3,000-6,000 contracts each, but **DF=0.5 annihilates everything more than ~6 ticks
from the best**: on 4.100 the yes side's 3,489 resting contracts produce a total score of 60.5,
because the 1¢ wall of 1,003 lots is 67 ticks away and scores 0.5^67 ≈ 0.
**⇒ 100 lots at the best price on 4.100's yes side = 62.3% of that entire side's score.**
On the no side, 100 lots at 31¢ = **95.0%**.

This invalidates `lip_screen.py`'s model, which scored our 100 lots against
`Σ size × 0.5^|ticks from same-side best|` over the **whole visible book** and ignored both the
Target-Size qualification walk and the per-side normalisation. It understated our share by ~5-50×
on the mid rungs and overstated eligibility on the pinned ones.

### 3c. Quote competition and stability
Over the sampled window: median spread 1-2¢ on the qualifying rungs (6¢ on 4.110); best-price
change rate 0-40% per 45s sample; top-of-book size **1-79 lots on the mid rungs**, versus
1,700-15,000-lot walls on the pinned extremes. The existing competition is (a) tick-boundary
collateral farmers at 1¢/99¢ who — per §3a — **are earning nothing**, and (b) 24-50-lot orders at
the top of the mid rungs, which are currently collecting the whole $700/window between them.

### 3d. Flow / fill reality (today's tape through 16:35Z, 4.6h into a 16h window)
43,296 contracts traded across all 17 rungs. By hour: 12Z 12,012 · 13Z 6,960 · 14Z 19,788 ·
15Z 2,365 · 16Z 2,171. The extremes trade in 2,000-4,000-lot blocks at 12Z/14Z; the **mid rungs
(4.090-4.115) trade 500-1,300 contracts each over the session, i.e. ~100-300/hour.**
**A 100-lot order at the top of book on a mid rung will fill, repeatedly, within the day.**

---

## 4. THE PROBE ECONOMICS (`probe_sim2.py`, exact algorithm, static-book assumption)

Join at the best price on **both** sides of the 7 qualifying rungs; reward $100/rung/16h window.

| our size / rung / side | collateral | modeled reward | **return per window** |
|---|---|---|---|
| 1 | $7.87 | $20.14 | 256% |
| 10 | $78.70 | $105.16 | 134% |
| **100** | **$787** | **$311** | **39.6%** |
| 300 | $2,361 | $432 | 18.3% |
| 1,000 | $7,870 | $652 | 8.3% |

Improving by one tick (where the spread allows) adds ~9%: $339.69 vs $311.42 at size 100.

**The capital-efficient point is 10-30 lots, not 100.** The lane's "size 100 is the LIP
minimum-relevant size" comes from R153's misreading of Target Size; the marginal $ of collateral
above ~30 lots buys a rapidly shrinking share because we already dominate a 25-60-point score.
The binding constraint at small size is the **$1.00 per-program minimum payout**: at 1 lot several
rungs pay under $1 and are forfeited entirely.

**Crowding (G4), our 100 lots vs rivals matching us at the same price:**
| rivals × 100 lots | 0 | 1 | 2 | 5 | 10 |
|---|---|---|---|---|---|
| our reward / 16h | $311 | $195 | $144 | $83 | $60 |
Halving on the first rival, then decaying slowly — because the *incumbent* book contributes so
little score that the fight is essentially between the new entrants.

**The revival trade (highest return per dollar on the board):**
| rung | action | our share | $/window | collateral | max loss |
|---|---|---|---|---|---|
| 4.075 | bid 1,000 NO @ 1¢ (creates the missing side) | 50.0% | $50.00 | $10.00 | $10.00 |
| 4.085 | bid 1,000 NO @ 1¢ (joins 25 lots, clears Target) | 48.8% | $48.78 | $10.00 | $10.00 |
Sole qualifying bidder on a side ⇒ normalized score 1.0 on that side ⇒ 50% of every included
snapshot. **$98.78 per window for $20 of collateral, if the reading is right and nobody copies.**
Also an ~EV-neutral outright: 1,000 NO at 1¢ on a market trading 98-99¢ YES.

---

## 5. H18 (1¢-spread unlock) ON THE INCENTIVIZED BOOKS ONLY
1,896 live liquidity programs, minus the 17 gas = 1,879 books. Total LIP 24h volume 1,282,184
contracts; 1,080 (57%) have any 24h volume.

| spread | books | % books | 24h vol | % vol |
|---|---|---|---|---|
| 0¢ | 0 | 0.0% | 0 | 0.0% |
| **1¢** | **1,378** | **73.3%** | **803,937** | **62.7%** |
| 2¢ | 115 | 6.1% | 104,273 | 8.1% |
| 3¢ | 48 | 2.6% | 54,564 | 4.3% |
| 4¢ | 28 | 1.5% | 12,697 | 1.0% |
| 5¢+ | 95 | 5.1% | 37,907 | 3.0% |
| one-sided | 174 | 9.3% | 268,806 | 21.0% |
| empty | 41 | 2.2% | 0 | 0.0% |

- **CONFIRMED:** 62.7% of LIP volume at exactly 1¢ — essentially identical to the exchange-wide
  62.6%. A `spread ≥ 2¢` gate would skip **84.3%** of LIP flow. **Delete the gate.**
- **NOT CONFIRMED:** the sub-cent leg. **Zero of 1,879 LIP books quote sub-cent** — no LIP market has
  fractional-cent ticks. The exchange-wide 22.5% sub-cent bucket lies entirely outside the
  incentivized universe.
- **NOT CONFIRMED:** "one-sided/empty books are the house opportunity." All 174 one-sided LIP books
  are **boundary-pinned** — 73 bid-only at 99¢, 101 ask-only at 0-2¢ — exactly the gas pathology of
  §3a. There is no quotable price on the missing side. That 21%-of-volume bucket is degenerate flow.
- **QUALIFIED:** of the 1,378 1¢ books only **794 are mid-range** (bid ≥10¢ and ask ≤90¢) = 42% of
  the universe and 26% of LIP volume. Queue at the best price: p25/p50/p75 = 238/520/1,122 lots
  (min-side 135/397/714); live orderbook pull of the 39 highest-volume mid-range books gives
  best-bid 46/196/374. **Hundreds of lots, not tens** — materially worse than gas.
- **Fee arithmetic honesty:** at fee 0, a 1¢ two-sided round-trip grosses +1.00¢ (vs +0.12¢ at the
  old 0.44¢ maker fee) — an 8× change, as claimed. But measured on the same books, |mid move vs
  previous quote| has mean **1.69¢**, with 34.4% moving ≥1¢ and 22.6% moving ≥2¢. **One adverse
  2¢ move wipes out two clean round-trips.** +1.00¢ is gross of adverse selection and must not be
  carried as an edge estimate.

**H18 verdict: half-confirmed.** The census claim holds on incentivized books and the ≥2¢ gate
should be deleted. The sub-cent and one-sided legs are wrong. And — the point that matters for this
lane — **spread is not an input to the LIP score at all**; the LIP-relevant variable is Target-Size
qualification on both sides, which is a *depth* condition, not a *spread* condition.

---

## 6. LEDGER DELTA vs `lane-HOUSE-FEE-jul27.md`

| # | prior verdict | new verdict | why |
|---|---|---|---|
| **H15** LIP-yield farm | TRADE-shaped, probe-first, gates G1-G4 | **TRADE-shaped, gates G2/G3 CLOSED by the CFTC filing, G1 near-closed, G4 quantified** — and the modeled yield is *higher* and the optimal size *smaller* than the lane thought | filing + exact-algorithm simulation on live books |
| **H16** gas maker house | TRADE-shaped at −0.875¢ gated | **TRADE-shaped at −0.729¢ ungated [−1.016, −0.438]; the gate is DEAD (argmax of 16, fails OOS); the mechanism story is corrected** | placebo/split/print-time |
| **H18** 1¢ unlock | CONDITIONAL on queue priority | **CONFIRMED for the census, DOWNGRADED in substance** — sub-cent and one-sided legs are wrong; +1.00¢ is gross, and mean adverse move is 1.69¢ | LIP-book census |
| **H19** cheap-side law | CONDITIONAL on G2 | **CONFIRMED** — distance is from the same-side best, DF^N | CFTC filing §Score(bid) |
| **NEW H23** | — | **The pinned-rung law: a market whose best bid sits at the 99¢ cap, or whose best ask sits at the 1¢ floor, can NEVER produce an included LIP snapshot** (no legal resting price exists on the missing side). 10 of 17 gas rungs and ~11% of all live LIP books are in this state. The 1¢/99¢ collateral farmers are earning **nothing**. | §3a, §5 |
| **NEW H24** | — | **The qualifying-set law: DF=0.5 means only the top ~6 ticks matter, and the per-side normalisation makes our share = ours ÷ (a score total of 5-160 on a thin book).** 100 lots = 40-95% of a side. This is where the LIP money actually is, and it is invisible to any whole-book model. | §3b |

## 7. FILES
- `/private/tmp/claude-501/-Users-ryanwhitehead/449dc817-6064-457d-a116-2df58b67bcb2/scratchpad/lip/`
  — `ip_all_fresh.json` (119,615 programs), `ip_pages.jsonl` (raw 120 pages),
  `gasmkts.jsonl` (17 live gas market objects), `cftc.txt` / `cftc_flat.txt` (**the filing text**).
- `.../scratchpad/micro/` — `sample.sh` (45s book sampler), `books.jsonl`, `trades_today.jsonl`,
  **`lipscore.py`** (CFTC algorithm, verbatim), **`probe_sim2.py`** (size/placement/competition).
- `.../scratchpad/gasadv/` — `core.py`, `s1.py`–`s6.py`, `recs.pkl` (markout adversary).
- `.../scratchpad/h18/` — `rows.json`, `mk.json`, `obs.jsonl`, `live_lip.txt`.
- Probe protocol: `work/probe-lip-gas.md`.
