# Lane BENTER — burst 1, 2026-07-27

Archetype: BENTER (feature model / diligence beats genius). Lane #4 of the jul27 burst:
**computable-trajectory forecast doors** — public settle source × lazy crowd × fittable model
(the measles shape), swept across the liquid-ladder catalog. Steering: *the calibration map says
residual edge lives only where NO anchor exists — hunt there.*

**Ritual descent:** goal → money → nestor. State = 5-system slate, staged $1k→$10k, maker fills
billed $0.00, LIP pays for resting size until Sept 1. **Maximize** here = a settle value I can
observe/reconstruct whose *far tail* a lazy quoter misprices, on a book with real depth.
**Therefore:** the decisive test for every idea is (a) does the far rung's threshold have a
computable base rate, and (b) **is there an external sharp market anchoring this ladder** — because
where an anchor exists the fat tail is real probability, not laziness.

Data: live public Kalshi GETs (unauth `api.elections.kalshi.com`), `~/kalshi_data/catalog_open_202607.json`
(8,437 events / 3,030 series, Jul-24 snapshot), `aaa_gas_series.json`, FRED `GASREGW` (1,875 weekly
obs 1990-2026), HURDAT2 1950-2025. One `researcher-med` scout for foreign public values
(→ `~/kalshi_data/benter_scout_jul27.json`).

---

## THE LANE'S LAW (→ Mesh)

**The geometric-ladder artifact.** Kalshi long-dated `≥K` ladders are quoted as a *pure geometric
decay in K* — constant probability ratio per rung, all the way out. Measured by fitting
`log p = a + b·K` on each ladder's liquid body (mid ∈ 0.12–0.88) and extrapolating:

| ladder | body fit | body mid/fit | far-rung mid/fit | far rung |
|---|---|---|---|---|
| KXAAAGASMAX-26DEC31 | `−0.889/$` | 0.8–1.2 | **1.0** | >$7.00 @ .055 |
| KXWTIMAX-26DEC31 | `−0.0207/$` | 1.0 (all 8) | **1.6** | >$200 @ .099 |
| KXHURCTOTMAJ-26DEC01 | `−0.402/storm` | 0.8–1.1 | **1.7** | ≥8 @ .090 |
| KXSPACEXCOUNT-26B | `−0.0486/launch` | 0.9–1.2 | **2.2** | >210 @ .075 |
| KXMEASLES-26 | `−0.00031/case` | 0.9–1.3 | **1.6** | >10000 @ .075 |

Real count/price tails decay **super-exponentially** (Poisson/Gaussian/diffusion-max), so a
geometric quote systematically overprices the far tail, and the error compounds with distance from
the body. **The floor is empirical: no rung on any of these ladders is quoted below ~5¢ no matter
how absurd the threshold.** A catalog sweep found **82** open ladders (≥5 rungs, OI ≥ 20k) whose
last rung sits in 1–15¢ with a near-flat decay into it — this is a *class*, not five markets.

**But the floor is only EDGE where there is no anchor.** The decisive split this lane established:

- **Anchored** (gas, WTI, BTC, ETH, SOL, S&P/NDX max ladders; Fed; U3): the fat tail is priced by,
  or coherent with, a deep external/own-venue sharp market. Selling it is selling real probability.
- **Unanchored** (hurricane counts, launch counts, disease counts): no financial market anywhere
  prices "8 major Atlantic hurricanes." The floor is pure retail lottery demand against a
  **computable public base rate**. This is the residual the calibration map predicted.

Screen (reusable, `/tmp/floor.py` pattern): fit the body, extrapolate, flag `mid/fit > 1.5`, then
**ask what anchors it**. Anchor present → skip. Anchor absent → compute the base rate and sell.

---

## IDEAS

| # | idea | mechanism / fish | cheapest kill | numbers | verdict |
|---|---|---|---|---|---|
| 1 | **Sell the Atlantic major-hurricane far tail** — KXHURCTOTMAJ-26DEC01, rungs "Above 4/6/7" (=≥5/≥7/≥8 majors), settle **NHC**, close Dec-2 | Retail buys the disaster lottery rung; NO financial market prices Cat-3+ counts, so nothing anchors the ladder. Fish = fear-buyer + a desk quoting a geometric ladder that adjusted the BODY for El Niño and left the TAIL on the rule | HURDAT2 1950-2025 frequency + El Niño conditional + the ladder's own body-extrapolation | HURDAT2 n=76: mean **2.66**, P(≥5)=**17.1%**, P(≥6)=**7.9%**, P(≥7)=**2.6%**, **P(≥8)=0/76**. Record = **7** (2005, 2020). El Niño conditional (19 seasons): mean **1.63**, **max 3**, **0/19 ≥4**. 2026 state: **0 majors, 2 named storms** as of Jul-27; CSU 7/8 cut to **9/4/1**; NOAA 1-3 majors. Market body implies mean ≈2.2 (El-Niño-adjusted ✓) but tail: ≥5 .13/.16, ≥7 **.10/.11**, ≥8 **.08/.10** — body-fit extrapolation says .052/.077 → **mid/fit 1.7**. Bid depth **200 contracts** on ≥4, ≥5, ≥7, ≥8. Sell ≥7 @ .10: capital $180/200ct, model p 0.3-1% → **ROC 10.2% / 4.2mo = 30% ann**. Sell ≥4 @ .24 (205ct): model p 8-12% → **18% / 4.2mo = 53% ann**. Strip (≥4,≥5,≥7,≥8): capital ~$694, premium $111, exp. loss ~$28 → **EV +$83, 12%/4.2mo ≈ 36% ann** | **TRADE-shaped** (top door). Gate = accept a 4.2-mo lock + tiny Kelly sizing (fat left tail). Named risk: 2023 produced 3 majors *despite* strong El Niño on record SSTs — the conditional is n=19 |
| 2 | **Sell the measles far tail** — KXMEASLES-26 ">6000 / >8000 / >10000", settle **CDC weekly**, close Jan-1 | Same shape, best depth. Crowd prices 35-yr-high outbreak fear as an open-ended right tail; the count is a published, near-linear accumulator | CDC pace arithmetic vs threshold (no model needed) | **2,318 confirmed as of 2026-07-23**; 2,073 on 2026-06-11 → **+41/wk**. 23.2 weeks to Jan-1 → central **~3,270**. >6000 needs **159/wk = 3.9× pace** sustained through seasonal autumn deceleration; >8000 needs **245/wk (6×)**; >10000 needs **331/wk (8×)**. Pre-2026 post-elimination US record = **1,274 (2019)**; 2025 full year = 2,289. Market: >3000 .90/.92 (fair — needs 29/wk), >4000 **.38/.39**, >6000 **.14/.15**, >8000 **.08/.09**, >10000 **.07/.08**. Bid depth **550 / 594 / 651** contracts. Sell >6000 @ .14: capital $473, model p ~1-2%, taker fee 0.84¢ → **13.7% / 5.2mo = 33% ann**; >8000 @ .08 → 21% ann | **TRADE-shaped.** *Corrects burst-2 idea 3b*: burst-2 aimed at **>4000** where the model is genuinely uncertain (needs 72/wk = 1.76× pace — a real possibility). The unambiguous rung is **>6000+**, where no plausible pace reaches the threshold |
| 3 | **Sell the SpaceX launch-count far tail** — KXSPACEXCOUNT-26B ">190 / >200 / >210", settle **FAA/SpaceX**, close Jan-1 | Public launch record; the annual ladder's own tail contradicts its own **monthly** sibling on the same settle source | On-venue coherence: settled monthly rungs give the realized run rate, no external data needed | Kalshi's own settled monthlies: **May = 12, Jun = 14**; the live Jul ladder implies **E[Jul] ≈ 13.9** (>13 .855, >14 .025). YTD **87 Falcon as of Jul-25** (2025 full yr 165; 2024 134). Remaining 5.2 mo at 13.3/mo → year-end **~157**; market median (>160 @ .42) **≈158 ✓ coherent in the body**. Tail: >180 needs **17.7/mo (+33%)**, >190 **19.6/mo (+47%)**, >210 **23.5/mo (+77%)** — vs a monthly sd of ~1.5-2 that puts >210 at **>10σ**. Market >180 .14/.15, >190 .09/.11, >200 .08/.11, >210 **.07/.08** (body-fit → .034, **mid/fit 2.2**). Implied density (190,200] ≈ **0** while P(>210) = 7.5% | **CONDITIONAL — gate: maker-only.** The price read is the cleanest of the three, but **bid depth is 6-34 contracts** on the tail rungs (vs 200-650 on hurricane/measles). Taker capacity ≈ $45. Rescue = rest offers under the ask (fee $0.00, LIP-eligible to Sep-1); needs the resting-order fill probe |
| 4 | **Sell the AAA-gas 2026 running-max far tail** — KXAAAGASMAX-26DEC31 ">5.40 … >7.00", settle **AAA daily national avg**, close Dec-31 | *Was this lane's leading candidate.* 35 years of history says the thresholds are unreachable; ladder is deep (OI 613k, spreads 2-4¢) | **The anchor test** — map each gas rung to its WTI equivalent and compare against Kalshi's own WTI-max ladder | Base rate (FRED GASREGW, Jul-27→Dec-31 max ratio, n=35 yrs): **max ever 1.341** (2005 Katrina), 2nd 1.161 (2017 Harvey). Regime gate (10 "spike-already-faded" years, which 2026 matches at ytdmax/now = 1.107): **max 1.146**, and the three genuine oil-crisis years were **1991 1.033 / 2008 0.973 / 2022 0.968**. From $4.110 that put >5.40 at ~2.9%, >6.00 ~1.1%, >7.00 ~0.27% vs market .18/.12/.05 → looked 10-20× rich. **THEN THE ANCHOR TEST KILLED IT:** retail-gas ≈ f(crude); mapping via the 2022 slope (3.8¢ gas per $1 WTI, WTI ≈ $96 today) gives gas $6.00 ≈ **WTI $146**, gas $6.40 ≈ **WTI $200**. Kalshi's **KXWTIMAX-26DEC31** (OI **2.4M**, spreads **0.2-2¢**) prices >$150 @ **.169**, >$180 @ .114, >$200 @ **.099**. Gas >6.00 @ .13 vs crude-implied ~.15; gas >6.40 @ .09 vs WTI>200 @ .099. **Coherent to 1-2¢ across the whole strip** | **DEAD-with-numbers.** The 35-yr unconditional base rate is the WRONG prior with the Strait of Hormuz closed since 2026-02-28 (gas $2.96 → $4.55 = **+54%** Feb-May). Two ladders, one desk, one geometric rule — and the deep one (WTI, 2.4M OI) is the sharp side. **This is the ACKMAN anchor law biting my own best idea** |
| 5 | **Short-horizon running-max tail** — KXAAAGASM-26JUL31 ">4.16" @ .06/.08 with 4 days left | If the far-tail floor were pure laziness it would appear at every horizon; monthly recycle would solve the capital-lock problem | AAA daily vol from `aaa_gas_series.json` (67 obs) + empirical 4-day forward max | AAA daily σ = **0.467%**, lag-1 autocorr **0.75** (smoothed retail avg = trending, not a walk). Empirical 4-day max ratio: p90 **1.0173**, p95 **1.0248**, sample max 1.0345. ">4.16" from ~4.07 needs **1.0221** → model **≈7%**. Market **6-8¢** | **DEAD (fairly priced)** — and *informative*: **the mispricing is horizon-specific.** Short-dated tails are anchored by the visible spot; only the 5-month tail is quoted by rule. Corollary: the fast-recycle version of this door does not exist |
| 6 | **Sell the screwworm human-case tail** — KXSCREWWORMCOUNT-27JAN01 ">3 / >5 / >10", settle CDC/state | Novel-pathogen fear; genuinely unanchored; largest OI on the ladder sits on the **most absurd** rung (>10 @ 12,593 OI) — textbook lottery flow | Base rate + **ROC arithmetic** | **0 US human screwworm cases in 2026**; zero locally-acquired human infestations ever recorded; the only US human case ever = 1 travel-associated (Maryland, Aug-2025). First US *animal* case of this outbreak 2026-06-03 (TX calf). Probability read is right (>10 ≈ 0). But market bids: >3 **.05**, >5 **.04**, >10 **.04** → selling at .04 = **4.2% ROC over 5.2 mo = 10% ann** | **DEAD on ROC.** Establishes the lane's **premium floor**: the unanchored-tail trade needs **≥8-10¢** of premium; below that the 5-month capital lock drops annualized ROC under 15% even at p≈0 |
| 7 | **Sell the U3 max tail** — KXU3MAX-27 ">6%" @ .052/.055, OI 85,606 (tight, deep) | Recession fear; running max of a monthly BLS print | Current level + fastest historical rise + ROC | U-3 = **4.2%** (June 2026, rel. 2026-07-02). >6% needs **+1.8pp in 6 prints**; fastest non-COVID 6-mo rise = +2.6pp (2008Q4-2009). p ≈ 1-2%. Sell @ .052 → **3.7% ROC / 5.4mo = 8% ann.** Ladder is also **non-monotone/phantom** above 6% (>7% .012/.041, >15% .003/.030 — unsellable spreads) | **DEAD on ROC** (+ partially anchored by the rates/recession complex). Same premium-floor lesson as #6 |
| 8 | **Artist-streams year-end ladders** — KXARTISTSTREAMSY (**60 events / 1,281 rungs**, settle **luminatedata.com**) | The biggest unanchored sweep-shaped surface in the catalog: catalog artists' annual streams are extremely stable YoY = highly fittable; crowd = fans | Two-factor law, factor-1 first (burst-2's rule: check WHO computes the settle number) + book coherence | **Factor-1 partial fail:** Luminate is a paid vendor (the Spice Data pattern); Spotify public playcounts recover only part of "Worldwide Streams." **Factor-2 fail is structural:** books are **phantom, one-sided and non-monotone** — 2Pac >2.5B .95 but >2.9B **.97** and >3.2B .95; Beatles >3.3B **0.00/1.00** while >3.5B .99; Billie Eilish >11.75B/>12.0B/>12.25B all **0.0000/0.9700 with OI 0**. Rung OI 50-4,000 | **DEAD (structural)** — same phantom-quote trap that killed KXAIRFARECPI in burst-1. Re-flag only if two-sided liquidity appears |

---

## TOP DOOR — the unanchored-tail book (ideas 1 + 2, with 3 pending a maker probe)

One trade, three uncorrelated public-record accumulators, all sold at the geometric floor:

| leg | sell @ | ct avail | capital | premium | model p | 
|---|---|---|---|---|---|
| KXHURCTOTMAJ ≥4 majors | .24 | 205 | $156 | $49 | 8-12% |
| KXHURCTOTMAJ ≥5 | .13 | 200 | $174 | $26 | 3-5% |
| KXHURCTOTMAJ ≥7 | .10 | 200 | $180 | $20 | 0.3-1% |
| KXHURCTOTMAJ ≥8 | .08 | 200 | $184 | $16 | ≤0.3% |
| KXMEASLES >6000 | .14 | 550 | $473 | $77 | 1-2% |
| KXMEASLES >8000 | .08 | 594 | $546 | $48 | <0.5% |
| **total (taker, at the bid, today)** | | | **~$1,713** | **~$236** | |

Expected loss ≈ $46, taker fees ≈ $9 → **EV ≈ +$181 on $1,713 over ~4.5 months ≈ 10.6%, ~28% annualized.**
As a **maker** (rest offers under the ask): fees → $0.00, capacity multiplies, LIP-eligible to Sep-1.

**Why this is the shape the calibration map predicted:** every liquid Kalshi catalyst ladder is
anchored *except* the ones whose underlying no financial market trades. Cat-3 hurricane counts and
CDC case counts have **no** anchor, a **fully public settle record**, and a **fittable trajectory** —
factor 1 and factor 2 both pass for the first time since the BENTER two-factor law was written.

**Honest limits (state these before any capital moves):**
1. **4-5 month capital lock** — the stage penalty burst-1/2 flagged is real and unfixed; idea 5 proves the fast-recycle version does not exist.
2. **Fat left tail.** Each leg is short a lottery. One 8-major season or one 6,000-case measles year erases many years of that leg's premium. Kelly-tiny sizing, and the legs are NOT independent of each other in a "2026 is a catastrophe year" world.
3. **Small conditioning n**: 19 El Niño seasons; 6 weeks of measles pace.
4. **Capacity is genuinely small** (~$1.7k taker). Correct for the $1k→$10k stage; worthless at scale — a THORP-sized edge wearing BENTER clothes.
5. Both measles and the AAA peak came to me **secondhand** (cdc.gov and gasprices.aaa.com both 403 automated fetches). Re-verify from primary before sizing.

## Capture gaps (demand-led)
- **NHC/HURDAT live season feed** — to mark the hurricane legs and exit early if 2026 turns active.
- **CDC weekly measles endpoint that doesn't 403** — the position's entire mark.
- **Resting-offer fill probe on a thin long-dated ladder** — idea 3's whole gate, and the maker
  upgrade for ideas 1-2. Composes directly with the HOUSE-FEE lane: **a 5-month absurd-threshold
  rung is the one book where resting is NOT adversely selected** — nobody holds private information
  about December hurricane counts, and the flow is retail fear-buying.

## Lane takeaway
The sweep found the class the charter asked for and then **killed its own best instance honestly**:
the gas running-max tail looked 10-20× rich against 35 years of FRED history, and the anchor test
against Kalshi's own 2.4M-OI WTI ladder showed the two are coherent to 1-2¢ — the base rate was the
wrong prior in a live Hormuz regime. What survived is narrower and better founded: the geometric
floor is only edge where **nothing anchors the ladder**, which on today's Kalshi means public-record
*counts* — hurricanes, launches, disease. ~28% annualized on ~$1.7k, four-month lock, tiny Kelly.
The reusable product is the **screen** (fit the body → extrapolate → flag mid/fit > 1.5 → *ask what
anchors it*), which found 82 candidate ladders and can be re-run on any listing snapshot.
