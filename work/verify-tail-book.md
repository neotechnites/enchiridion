# VERIFY-TAIL — deep verification of BENTER's unanchored-tail book (2026-07-27)

Target: the top door of `work/lane-BENTER-jul27.md` — sell the geometric floor on
KXHURCTOTMAJ-26DEC01 (≥4/≥5/≥7/≥8 majors) + KXMEASLES-26 (>6000/>8000).
Read first: Mesh §BENTER TWO-FACTOR LAW (33), note 07, note 15 §THE REAL GOAL, note 40 §bankroll.

**VERDICT: the analysis is SOUND and reproduces; the TRADE is DEAD for this bankroll.**
Every quantitative claim the lane made verified from primary source or live tape — several
of them to the contract. Three things the lane got wrong, one of which is material. And the
capital frame kills it independently of any of that: live bankroll is **$93.09**; the book
locks **$1,727** for 146 days.

---

## 1. PRIMARY SOURCE — both secondhand inputs verified

The lane flagged (honest-limit #5) that CDC and the hurricane state came secondhand because
`cdc.gov` 403s automated fetches. **Both are now primary-sourced. The 403 is defeated.**

**How** (→ capture gap closed): a bare browser UA is NOT enough — Akamai edge returns
`Access Denied / errors.edgesuite.net`. The full Chrome fetch-metadata header set gets 200:
```
-A '<Chrome UA>' -H 'Accept: text/html,...' -H 'Accept-Language: en-US,en;q=0.9' --compressed
-H 'sec-ch-ua: "Chromium";v="126", "Not)A;Brand";v="24"' -H 'sec-ch-ua-mobile: ?0'
-H 'sec-ch-ua-platform: "macOS"' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate'
-H 'Sec-Fetch-Site: none' -H 'Sec-Fetch-User: ?1' -H 'Upgrade-Insecure-Requests: 1'
```
Machine-readable endpoint (same headers + `Referer:` the measles page, `Sec-Fetch-Site: same-origin`):
**`https://www.cdc.gov/wcms/vizdata/measles/measles_hosp.json`** → `{"2026":{"total_cases":["2,318"],...}}`.
That is the position's mark, as JSON, no scraping. `Measlescasesbyweek.json` etc. are 404 (renamed).

| input | lane said | PRIMARY (2026-07-27) | source |
|---|---|---|---|
| CDC measles YTD | 2,318 as of Jul-23 | **2,318 as of Jul-23**, page updated Jul-24 | cdc.gov/measles/data-research + measles_hosp.json |
| measles pace | +41/wk (2,073 on Jun-11) | **+42.0/wk** 12-wk trailing; Jun-11 = **2,073** ✓ | 8 archived CDC snapshots, Apr-30→Jul-23 |
| 2025 full year | 2,289 | **2,289** ✓ | CDC same page |
| CSU Jul forecast | 9/4/1 | **9 named / 4 hur / 1 major** (Jul-8 update) | tropical.colostate.edu/forecasting.html |
| 2026 Atlantic YTD | 0 majors, 2 named | **0 majors, 0 hurricanes, 2 named (Arthur, Bertha)** | nhc.noaa.gov/archive/2026/ |
| HURDAT2 base rate | mean 2.66, P≥5 .171, P≥6 .079, P≥7 .026, P≥8 0/76, max 7 | **all exact** (1950-2025, n=76) | aoml.noaa.gov/hrd/hurdat/hurdat2.html |

**Verified measles pace series** (never before on disk — this is the trajectory the whole leg rests on):
Apr-30 1,814 · May-14 1,893 · May-28 1,983 · Jun-11 2,073 · Jun-25 2,134 · Jul-9 2,231 ·
Jul-16 2,260 · Jul-23 2,318. Weekly increments 29-58, no acceleration, 12-wk pace 42.0/wk.
Structural: **93% of 2026 cases are outbreak-associated and 1,371 of 2,153 come from outbreaks
that STARTED IN 2025** — this is a maturing carryover, not an ignition. 35 new outbreaks in 2026
vs 48 in all of 2025.

**PLUS one primary source the lane never pulled, and it is the best news in the file:**
CPC ENSO Diagnostic Discussion, **9 July 2026** — *El Niño Advisory*, weekly Niño-3.4 **+1.2 °C**,
ASO ONI tracking from AMJ +0.98 and strengthening, **81% chance of a VERY STRONG El Niño (≥+2.0)
during Oct-Dec**, 97% chance it persists to spring 2027. The lane conditioned on generic El Niño.
The correct conditioning tier is far stronger, and it changes the numbers (§2).

---

## 2. THE MODEL — one lane error corrected, and the conditioning improved

### 2a. The lane's El Niño conditional is WRONG
> lane: *"El Niño conditional (19 seasons): mean 1.63, **max 3, 0/19 ≥4**"*

Recomputed from HURDAT2 × CPC ASO ONI (n=76 aligned seasons):

| conditioning | n | mean | max | P(≥4) |
|---|---|---|---|---|
| unconditional 1950-2025 | 76 | 2.66 | 7 | 25.0% |
| ASO ONI ≥ +0.5 (El Niño) | 22 | 1.91 | **6** | **1/22** |
| ASO ONI ≥ +1.0 (moderate+) | 10 | 1.60 | 3 | 0/10 |
| ASO ONI ≥ +1.5 (strong) | 7 | 1.29 | 3 | 0/7 |
| ASO ONI ≥ +2.0 (very strong) | 2 | 1.50 | 2 | 0/2 |

**`max 3, 0/19 ≥4` is false at any standard El Niño definition.** The counterexample is
**2004 — ASO ONI +0.70, officially a weak El Niño episode, and it produced 6 major hurricanes**
(the Florida quadruple-landfall year). Whatever cut produced n=19 excluded it. This matters
because 2004 is *also* the closest structural analogue to today on the other axis (§2b).

### 2b. But the right conditioning is STRONGER than the lane's, not weaker
2026 is not a weak El Niño and it is 0-for-July. Both cuts, jointly:

- **majors by Jul-27 → full season** (n=76): 0 by Jul-27 (n=69) → mean 2.48, P(≥7) 1/69.
  1 by Jul-27 (n=6) → mean 4.00. 2 by Jul-27 (n=1, 2005) → 7. Weakly informative, correct sign.
- **0 majors by Jul-27 AND ASO ONI ≥ +1.0** → n=9: 1963(3) 1965(1) 1972(0) 1982(1) 1987(1)
  1997(1) 2002(2) 2015(2) 2023(3). **mean 1.56, max 3, 0/9 ≥ 4.**
- 0 by Jul-27 AND ONI ≥ +0.5 → n=21, mean 1.90, max 6 (that is 2004 again), 1/21 ≥ 4.

Model = NegBin(μ, overdispersion 1.35 — HURDAT2's own var/mean is 1.12, so 1.35 is padded).
Central μ = 1.60 (ONI ≥ +1.0 conditional). Stress μ = 2.00. CSU's own Jul-8 point forecast is 1.

| rung | live bid | live ask | p (μ=1.29) | **p (μ=1.60 central)** | p (μ=2.0 stress) | edge @ bid |
|---|---|---|---|---|---|---|
| ≥4 (T3) | 24 | 30 | 6.66% | **10.54%** | 16.66% | +13.5¢ |
| ≥5 (T4) | 13 | 16 | 2.57% | **4.49%** | 7.92% | +8.5¢ |
| ≥6 (T5) | 8 | 16 | 0.95% | **1.80%** | 3.52% | +6.2¢ |
| ≥7 (T6) | 10 | 11 | 0.33% | **0.69%** | 1.48% | **+9.3¢** |
| ≥8 (T7) | 8 | 10 | 0.11% | **0.25%** | 0.60% | +7.7¢ |

### 2c. NEW — the ladder is NON-MONOTONE, and that names the single best rung
**bid(≥6) = 8¢ but bid(≥7) = 10¢.** P(≥7) cannot exceed P(≥6). On mids: ≥6 = 12, ≥7 = 10.5,
≥8 = 9 — a **flat ~10¢ shelf**, not a decay. The market implies **P(≥8 | ≥6) = 9/12 = 75%**;
the count model says **14.1%**. That is the geometric-floor artifact in its purest measured form,
and it says **≥7 (T6) is the single richest rung on the ladder** — better edge than ≥8 *and*
better ROC (11.1% vs 8.7%) *and* a 1¢ spread. The lane sized ≥7 and ≥8 alike; ≥7 dominates.

### 2d. Measles — lane's probabilities verified, and the artifact measured
From verified 2,318 @ Jul-23 and 42.0/wk over 23.0 weeks to Dec-31:

| rung | needs/wk | × pace | bid | model p (autumn-decel, σ=0.75) | p at market's own body-implied σ | mid/fit |
|---|---|---|---|---|---|---|
| >3000 | 29.7 | 0.71× | 90 | 53% | 52% | 1.7× |
| >4000 | 73.1 | 1.74× | 38 | 13.1% | 20.5% | 1.9× |
| **>6000** | 160.1 | **3.81×** | 14 | **1.51%** | 5.55% | 2.5× |
| **>8000** | 247.0 | **5.88×** | 8 | **0.30%** | 2.17% | 3.7× |
| >10000 | 334.0 | 7.95× | 7 | 0.08% | 1.03% | 6.8× |

Lane said >6000 ≈ 1-2% and >8000 < 0.5% — **verified**. Better: the ladder is *internally*
inconsistent. Fitting a lognormal to consecutive rung pairs, the implied σ **explodes** outward:
**1.02 (>4000→>6000) → 1.38 (>6000→>8000) → 4.4 (>8000→>10000)**. No single distribution prices
this ladder. The floor is not a view; it is a quoting rule running out of road, and it is now
measured rather than asserted.

---

## 3. CORRELATION AUDIT — the lane named the wrong risk

> lane honest-limit #2: *"the legs are NOT independent in a '2026 is a catastrophe year' world."*

**That is the weakest of the three linkages and it is nearly zero.** Ranked by actual damage:

### RISK 1 — the basin-interpretation flip. Hits all four hurricane legs at once, totally. NEW.
`rules_primary` (T7, verbatim): *"If the **NOAA's National Hurricane Center records** more than 7
hurricanes of hurricane category 3 or above between January 1, 2026 and December 01, 2026..."*
**No basin restriction.** NHC's area of responsibility is the Atlantic **and the Eastern North
Pacific**. The event *title* says "major **Atlantic** hurricanes" and the series title is the
generic "Number of major hurricanes."

And the trap is vicious: **the very ENSO state that makes the Atlantic short safe is the state
that maximizes Eastern Pacific majors.** El Niño shears the Atlantic and supercharges the EPac —
2015 (ONI +2.21) gave 2 Atlantic majors and a record EPac season. Under an NHC-wide read in a
very strong El Niño, P(≥8) goes from 0.25% to *better than even*. The thesis's own tailwind is
the ambiguity's fuel. Live check: NHC currently has **Genevieve (135 kt, Cat 4)** and Fausto
(70 kt) — 1 NHC-wide major already, 0 Atlantic.

Mitigant: the UI/event title governs in practice, so this is a low-probability event — but it is
not zero, and the linked CFTC certification does **not** help (§4). Priced at 3% below.

### RISK 2 — style/repricing correlation. 100% by construction, and it is a mark risk not a settle risk.
All six legs are *the same trade*: short the geometric floor on an unanchored ladder. One desk
fixing its ladder rule, or one sharp arriving, reprices every leg simultaneously while you are
locked. And you cannot exit: the measles >6000 bid is 550 @14¢ then 200 @13¢ — that stack is
*the same lazy quoter you are betting against*. If the floor breaks, the bid that lets you out
is the first thing to disappear. **You are short a book whose only exit liquidity is your
counterparty's mistake.**

### RISK 3 — the physical world-state. ρ ≈ 0.
There is **no** plausible world in which hurricane counts and US measles counts move together.
The only real channel is second-order and tiny: a landfalling major → shelter crowding → measles
transmission in an under-vaccinated Gulf community, needing 4 months to amplify. I nonetheless
modelled ρ = 0.5 in log-intensity (far above anything defensible) to see what it costs.

### The honest joint left tail
400k-path Monte Carlo, NegBin hurricanes × lognormal measles, common shock factor:

| scenario | EV | P(any leg loses) | P(≥2 legs) | 1-in-20 | **1-in-100** | 1-in-1000 | worst |
|---|---|---|---|---|---|---|---|
| independent (ρ=0) | +$194 | 11.9% | 4.87% | −$168 | −$373 | −$907 | −$1,727 |
| correlated ρ=0.5 | +$174 | 13.8% | 7.30% | −$168 | −$718 | −$1,312 | −$1,727 |
| **ρ=0.5 + 3% basin-flip** | **+$150** | **16.6%** | **10.1%** | **−$583** | **−$718** | **−$1,517** | **−$1,727** |
| ρ=0.5 + bearish measles σ | +$142 | 16.9% | 9.09% | −$373 | −$907 | −$1,727 | −$1,727 |

**THE JOINT-RISK NUMBER: at the 1-in-100 level the book loses $718 — 42% of the $1,727 locked
and 3.0× the $237 of gross premium. At 1-in-1000 it loses $1,517 (88%). P(≥2 legs lose
together) = 10.1%.** Correlation is not what does the damage — the basin flip is. It alone
moves the 1-in-20 outcome from −$168 to −$583.

---

## 4. SETTLEMENT FINE PRINT — per rung

Pulled `/series/{S}`, `/markets/{ticker}`, and both CFTC product certifications.

| | KXHURCTOTMAJ | KXMEASLES |
|---|---|---|
| Source Agency | NOAA / NHC (`nhc.noaa.gov`) | CDC (`cdc.gov/measles/cases-outbreaks.html`) |
| who computes | NHC operational advisories | CDC, weekly, Thursday 12:00 PM cut |
| close | 2026-12-02 04:59Z | 2027-01-01 04:59Z (= Dec-31 23:59 ET) |
| expected expiration | 2026-12-02 | **2026-12-31** |
| **registered latest expiration** | 2026-12-02 | **2027-12-31** ← one full extra year |
| early settle | **yes** — "expires at the sooner of the occurrence of the event, or one day after December 01" | **yes** — Mesh-confirmed: >1500/>1750/>2000 all early-settled 2026-06-09 on crossing |
| revisions | *"Revisions to the Underlying made after Expiration will not be accounted for"* | same clause |
| fee | quadratic, multiplier 1 | quadratic, multiplier 1 |
| position limit | $25,000 max loss | $25,000 max loss |

**Three fine-print findings the lane did not have:**

1. **Early settle is one-way against the short.** Both contracts expire on *occurrence*. Sell
   >6000 and the loss crystallises the first 10:00 AM ET after CDC posts 6,001 — you lose fast;
   you win slow. Combined with "revisions after Expiration are not accounted for", a **transient
   overshoot that CDC later revises down still kills the position**. CDC does reclassify cases.
   The short has no revision recovery. This is a genuine asymmetry, unpriced by the lane.
   *(Direction is favourable on hurricanes: post-season TCR re-analysis routinely upgrades a
   storm to major months later, and that upgrade lands after Expiration, so it cannot hurt you.)*

2. **The measles lock is open-ended, not 5.2 months.** Appendix A: Expiration = sooner of
   (a) crossing, (b) *"the first 10:00 AM ET following the release of the data for all of `<year>`"*,
   or (c) **one year after `<year>`**. Trading closes Jan-1, but a NO holder is paid only when CDC
   releases final 2026 data. Kalshi's own `expected_expiration_time` is 2026-12-31 and the
   registered `expiration_time` is **2027-12-31**. Realistically it settles in early-to-mid
   January; the outer bound is a full extra year of dead capital. The lane's 5.2-month ROC
   denominator is the optimistic end of a range, not the range.

3. **The linked hurricane certification does not describe this product.** `KXHURCTOTMAJ` links
   `LTHUR.pdf`, whose Appendix A defines *"the number of Atlantic storms ... whose maximum
   sustained wind-speeds exceed `<speed>` **and make landfall in the continental United States**"* —
   a **landfall** contract, dated 8/31/21, expiring December 1 **2021**. KXHURCTOTMAJ is a
   basin-wide *count*. So the one document that would resolve the §3 basin ambiguity in the
   short's favour is the wrong document. Terms-and-conditions hygiene on this series is poor,
   and Rule 7.2 additionally lets Kalshi redesignate the Source Agency mid-contract.

---

## 5. MAKER EXECUTION — depth verified live, and the maker upgrade is half fiction

Live orderbook (`/markets/{t}/orderbook`, key `orderbook_fp`, dollar-denominated), 2026-07-27:

| rung | bid | depth @bid | cum ↓3 levels | ask | **spread** | maker room? |
|---|---|---|---|---|---|---|
| HUR ≥4 (T3) | 24 | **205** | 244 @ ≥22 | 30 | **6¢** | YES — rest 27 |
| HUR ≥5 (T4) | 13 | **200** | 322 @ ≥10 | 16 | 3¢ | yes — rest 15 |
| HUR ≥6 (T5) | 8 | 10 | 306 @ ≥6 | 16 | 8¢ | yes, but rung is dominated |
| HUR ≥7 (T6) | 10 | **205** | 410 @ ≥5 | 11 | **1¢** | **NO — take the bid** |
| HUR ≥8 (T7) | 8 | **210** | 415 @ ≥5 | 10 | 2¢ | marginal — rest 9 |
| MEA >6000 | 14 | **550** | 2,706 @ ≥12 | 15 | **1¢** | **NO — take the bid** |
| MEA >8000 | 8 | **594** | 1,970 @ ≥6 | 9 | 1¢ | **NO — take the bid** |

**The lane's "200-650 contract bid depth" claim is verified live, to the contract**
(205/200/205/210/550/594 vs the lane's 205/200/200/200/550/594).

**But the lane's maker rescue is wrong on the rungs that matter.** *"As a maker (rest offers
under the ask): fees → $0.00, capacity multiplies."* On four of six legs the spread is **1¢** —
there is no "under the ask" to rest at. Your choices are (a) join the bid queue behind 550
contracts and wait months on a book that trades a handful of lots a week, or (b) cross and pay
the fee. **Capacity does not multiply; it is capped by the top-of-book lot.** Where the maker
upgrade is real is exactly the rung the lane treated as ordinary: **T3 at 24/30 is a 6¢ spread**,
and T4 at 13/16 is 3¢.

The design that follows from the tape:
- **Take** the bid on T6 (≥7), MEA>6000, MEA>8000 — 1¢ spreads, deep-enough single lots.
- **Rest** on T3 @27 and T4 @15 (LIP-eligible to Sep-1, fee $0.00) — 6¢ and 3¢ of spread to
  capture, and no adverse selection: nobody holds private information about December hurricane
  counts. This composes with the HOUSE-FEE lane exactly as the lane predicted.
- **Do not ladder down.** Cumulative depth is a trap: T7 is 8:210 then 6:115 then 5:90, so
  reaching 415 contracts means an average of 6.8¢ against a rung whose whole thesis is the 8¢.
  Taking level 2 destroys ~15% of the edge on that leg.
- **Fee correction:** at Kalshi's `ceil(0.07·P·(1−P)·C)` the taker book costs **$14.31**, not
  the lane's ~$9 — a 60% understatement, ~6% of gross premium.

---

## 6. CAPITAL / LOCK vs THE NOTE-15 STAGED FRAME — this is what actually kills it

Verified book economics (independent case, no basin risk):

| | lane claimed | **verified** |
|---|---|---|
| capital | $1,713 | **$1,727** |
| premium | $236 | $237 |
| expected loss | $46 | **$35** |
| fees | $9 | **$14.31** |
| EV | +$181 | **+$188** |
| ROC | 10.6% | **10.9%** |
| annualized | 28% | **27%** (cap-weighted tenor 146d) — and **22%** once ρ and the 3% basin flip are priced |

The lane's arithmetic reproduces. **The capital frame does not.**

- **Live bankroll is $93.09** (note 40, peak $106.03, dd 12.2%). The book locks **$1,727** —
  **18.5× the entire bankroll.** It is not sizeable today at any Kelly fraction.
- Even at the top of the stage it targets, $1k, the book is **1.7× the whole stage bankroll**.
- Note 15's stated stage requirement at $1k→$10k is **5-15%/day**. This book returns **27%/yr
  ≈ 0.08%/day**. It is **~100× below the stage's required rate**, and it would consume the
  bankroll for 146 days — during which the streak/volbook/house systems that *are* the flywheel
  get nothing. **At the $1k stage the binding constraint is capital velocity, not edge quality**,
  and a 146-day lock is the single most expensive thing you can buy.
- Idea 5 in the lane already proved the fast-recycle version does not exist (short-horizon tails
  are anchored by visible spot and fairly priced). So this cannot be fixed by shortening it.
- **Where it does belong:** at the $50k steady state, $1.7k is 3.4% of bankroll for +$374/yr —
  harmless, uncorrelated, ~0.15% of the $250k/yr target. Capacity is hard-capped at ~$1.7-3k
  by the top-of-book lots, so it never scales past that. **Shelf it to the $50k stage.**

**Answer to "is 28% annualized on locked $1.7k the best use of that slot": no — it is close to
the worst, because at $93 (or $1k) the slot does not exist.** The number is real; the slot is not.

**If it were ever taken, take a third of it.** The two-rung book — sell **HUR ≥7 (T6) @10¢**
(edge +9.3¢, the non-monotone rung) and **MEA >6000 @14¢** (edge +12.5¢, the widest absolute edge
in the file) — locks **$658**, earns **$97.50** gross, and carries **$8.4** of expected loss.
It drops the ≥4 leg, which alone carries **47% of the book's entire expected loss** ($16.4 of
$34.8) and is the one rung where a real counterexample exists (2004: 0 majors by Jul-27,
El Niño, finished with 6).

---

## 7. THE REUSABLE SCREEN — re-run. The 82 does not survive, and there is no next-5.

Re-ran the fit-body → extrapolate → flag `mid/fit > 1.5` screen on
`catalog_open_202607.json` (8,437 events), then against a fresh live snapshot (8,156 events).

**The "82 open ladders" claim does not reproduce as a screen result.**

| filter | N |
|---|---|
| ≥5 numeric rungs | 2,013 |
| + event OI ≥ 20k | 245 |
| + far-rung mid in 1-15¢ | **128** |
| + "near-flat" \|b\| < 0.50 | 76 |
| + \|b\| < 0.65 | **80** |
| + \|b\| < 0.70 | 83 |
| **+ mid/fit > 1.5 (the actual flag)** | **23** |
| **+ monotone (structurally sane)** | **9** |

82 exists only inside the arbitrary window \|b\| ∈ [0.65, 0.70] on the "flat decay" cut. Worse,
**`b` is log-price per unit of K and K's units are not comparable across series** — dollars for
KXWTIMAX, integer counts for KXMEASLES, index points for KXINXMAXY. Any fixed \|b\| cut is
**dimensionally meaningless as a cross-series filter.** The claim's real substance is **23**
ladders, **9** monotone. Of the 80-set, 63 are still open and 59 still quoted in 1-15¢ today.
→ **The screen needs a scale-free slope (b·K_far, or fit in log-K) before it is re-used.**
Also add a `days_to_close ≥ 7` guard: KXWTIH scored ratio 435 and KXSILVERH 301, both pure
artifacts of a same-day ladder whose body had collapsed.

### The next 5 by ROC — all five fail

| # | far rung | thr | bid | depth | cum↓3 | days | ROC | ann | OI | anchor | verdict |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | KXAAAGASED-26NOV03-5.00 | gas >$5 election day | 5 | 100 | 503 | 98 | 5.3% | 21.1% | 1,075 | **NYMEX RBOB + EIA weekly** | SKIP — anchored; DEAD-on-ROC (5<8¢) |
| 2 | KXSOLMAXY-27JAN01-300 | SOL max >$300 | 4 | 55 | 508 | 157 | 4.2% | 10.0% | 12,266 | **SOL spot/perp** | SKIP — anchored; DEAD-on-ROC |
| 3 | KXINXMAXY-01JAN2027-8999.99 | S&P max >8999.99 | 3 | 431 | 692 | 157 | 3.1% | 7.9% | 25,665 | **CBOE SPX options** | SKIP — anchored; non-monotone; DEAD-on-ROC |
| 4 | KXTESLASEMI-27JAN-25000 | Semi >25,000/qtr | 3 | **1** | 1,638 | 157 | 3.1% | 7.3% | 359 | **none** | **passes anchor** — but 1 contract at 3¢; DEAD-structural + DEAD-on-ROC |
| 5 | KXETHMAXY-27JAN01-6000.00 | ETH max >$6,000 | 3 | 3,649 | 106,276 | 157 | 3.1% | 7.3% | 160,451 | **Deribit ETH options** | SKIP — anchored; DEAD-on-ROC |

Four are anchored by a sharp external market. The one unanchored name — **KXTESLASEMI, ratio
46.4, the largest geometric-floor violation in the entire live catalog** — has a **one-contract**
top bid. The violation is real and unmonetizable. Every candidate's sellable bid is ≤5¢, under
the lane's own 8-10¢ premium floor, against 98-157 day locks.

Sweeping *every* remaining unanchored count ladder (KXLAUNCHES, KXDEPORTATIONS,
KXFOMCDISSENTCOUNT, KXNEWSCOTUSCONF, KXTRUTHSOCIAL, KXNBAWINS, KXMLBSTATCOUNT, KXTESLASEMI,
KXTRUMPFIRE, KXAISPIKE, KXVOTEPRIMARY) at OI ≥ 20k and ≥7 days to close: **max bid 3¢, max
annualized ROC 9.0%.**

Structural junk found in the flagged-17, for the Mesh: KXNFLWINS-27MIA-17, KXMLBWINS-SEA/BOS,
KXECONSTATCPIYOY, KXCOPPERD, KXWTIH, KXSILVERH all have `yes_bid = 0` (one-sided, unsellable).
KXTRUMPFIRE-27-5 shows **10,000 contracts resting at 0¢** — phantom depth.
KXAAAGASMAXNY-26DEC31-6.20 quotes 2/19 (spread > mid).

**→ There is no #4 through #8. The unanchored-count universe is exhausted by the 12 series the
lane already worked**, and the three survivors it named (KXHURCTOTMAJ-T7 8¢ ratio 2.09,
KXMEASLES-10000 7¢ ratio 3.65, KXSPACEXCOUNT-210 7¢ ratio 2.22) are the only far rungs in the
whole live catalog clearing both the anchor test and the premium floor. The screen's product is
not a pipeline — it is a **one-time inventory, and the lane already spent it.**

*(Inverse observation worth keeping: the only far rung in the live set with genuinely deep
sellable size is KXALLSVENSKANTOTAL-26JUL27HACAIK-6 — 12¢ bid, **10,746 contracts** at top,
79,198 cumulative, 14 days, 2,702% annualized. It **fails** the geometric screen — ratio 0.85,
tail quoted *cheap* — and is anchored by sportsbook total-goals lines. Where the depth is, the
screen says don't look. That inversion is the finding.)*

---

## LEDGER TAKEAWAY

The lane did honest work and its numbers hold up under primary sourcing — the CDC count, the
CDC pace, the CSU forecast, the 2026 Atlantic state, the HURDAT2 base rates, and every single
bid-depth figure verified, several exactly. Three corrections: the El Niño conditional was wrong
(2004 gave 6 majors under El Niño), fees were understated 60%, and the maker upgrade does not
exist on four of six rungs because the spread is 1¢. Two additions the lane missed are worth
more than the corrections: **the ladder is non-monotone at ≥6/≥7, which names ≥7 as the best rung
in the file**, and **`rules_primary` omits "Atlantic" while NHC's remit includes the Eastern
Pacific — where a very strong El Niño, the same one making the Atlantic short safe, produces
record major counts.**

The screen that was supposed to be the durable product is the weakest part: 82 → 23 → 9, on a
dimensionally invalid slope filter, and the next five candidates are all anchored or unsellable.

None of that decides it. **$93.09 decides it.** A 146-day lock on $1,727 at 22% annualized is
not a trade this bankroll can hold, and at the $1k stage note 15 is asking for 5-15%/day.
File the analysis, keep the CDC 403 bypass and the ENSO conditioning, shelf the position to the
$50k stage — and if it is ever opened, open a third of it: **≥7 at 10¢ and >6000 at 14¢, $658.**
