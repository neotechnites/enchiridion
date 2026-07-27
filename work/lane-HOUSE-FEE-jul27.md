# Lane: HOUSE-FEE — the maker-fee asymmetry industrialized + LIP composition (2026-07-27)

Archetype: THE HOUSE. Charter: `work/steer-ideation-jul27.md` lane 1. Spin-up done
(33 Mesh complete → 34 HOUSE brief → 20 → graveyard: Mesh §graveyard + 15 traps).
Prior art resumed from disk, NOT re-derived: `work/lane-HOUSE-burst1.md` (H1 markout≈0 on
KXAPRPOTUS), `work/lane-HOUSE-burst2.md` (H9/H10/H12/H13), `work/build-house-probe.md`
(demo evidence: GTC+expiration_ts+taker_at_cross, lazy 2-3min expiry sweep, reduced_by truth),
R152/R153 (LIP), R161 (maker fills FREE). Scouts spawned: 0. Nestor untouched.

## 0. THE LOAD-BEARING FACT, RE-VERIFIED ON DISK
`~/kalshi_data/house_truth_probe.jsonl`: every `is_taker:false` fill carries
`fee_cost:"0.000000"`; taker fills carry 0.0145 @0.71 and 0.0047 @0.929 = exactly
`0.07·P·(1−P)`. **Maker = $0.00, taker = up to 1.75¢/contract.** Everything below is
built on that asymmetry.

## 1. STRUCTURE FINDING — the spread does NOT scale with the fee (76,186-market census)
Full open-market catalog snapshot (`catalog_open_202607.json`, 8,437 events / 74,646 active
markets), two-sided books with 24h volume (n=12,502, 76.5M contracts/24h):

| mid bucket | 0-5¢ | 20-25 | 45-50 | 50-55 | 75-80 | 95-100 |
|---|---|---|---|---|---|---|
| median spread | 1.0¢ | 2.0¢ | 3.0¢ | 3.0¢ | 3.0¢ | 1.0¢ |
| vol-wtd spread | 0.41¢ | 2.05¢ | 2.00¢ | 1.52¢ | 1.09¢ | 0.51¢ |
| taker fee 0.07P(1−P) | 0.17¢ | 1.40¢ | 1.75¢ | 1.75¢ | 1.22¢ | 0.17¢ |

Spread is roughly **price-invariant (1-3¢)**; the fee is quadratic. Consequences, both used below:
- **Maker-free is worth 0.07·P(1−P), i.e. ~nothing at the extremes (0.2-0.5¢) and 1.75¢ at 50¢.**
  Any "the fee is now gone" idea sited at 1-5¢ or 95-99¢ is worth ≤0.5¢ and mostly noise.
- **Spread by volume:** 22.5% of exchange 24h volume sits in sub-cent-spread books, **62.6% at
  exactly 1¢**, only 14.8% at ≥2¢. Burst-2's "quote only when spread ≥2¢" gate therefore
  excluded 85% of the exchange's flow — and that gate was set by the (now-dead) 0.44¢ maker fee.

## 2. FAMILY TOXICITY MAP (new — `scripts/house_markout_families.py`, fee=0)
Markout = taker_dir×(P[t+h]−P[t]); **>0 = taker right = maker adversely selected**. In-band
0.10-0.90. `makerA` = −markout (mark-only, conservative); `makerB` = +measured half-spread proxy.

| family | tapes | trades | h=300s markout | adv% | maker net/fill (A..B) | verdict |
|---|---|---|---|---|---|---|
| KXAAAGASD (AAA gas daily) | 618 tk | 128,946 | **−1.174¢** | 34.7 | **+1.17 .. +2.67¢** | best on the exchange |
| KXCPIYOY (econ ladder) | 34 | 20,585 | −0.929¢ | 25.0 | +0.93 .. +1.43¢ | burst-2 upheld |
| KXAPRPOTUS (poll avg) | 72 | 62,762 | −0.036¢ | 32.6 | −0.14 .. +0.54¢ | **downgraded** |
| KXTEMP*/weather hourly | 2,418 | 145,187 | **+4.761¢** | 61.1 | **−4.76 .. −3.26¢** | anti-house |

Also measured: **taker-flow autocorrelation P(same direction as previous trade) = 0.79-0.85 in
every family.** The trade-price mark is therefore NOT bounce-neutral, so makerA understates and
makerB (which adds a measured half-spread of 0.5¢ approval/CPI, 1.5¢ gas) overstates; the truth
is bracketed. Burst-2 assumed a 1.0¢ half-spread on approval where the opposite-side-trade proxy
says 0.5¢ — that alone halves burst-2's approval number.

**NEW LAW (the lane's mechanism correction):** the house's enemy is not underlying SPEED, it is
**continuous public information arrival**. Weather is a slow underlying and is the most toxic book
we have ever measured (worse than crypto's 70-76% ITM) because the settle value is revealed
minute by minute all day; the taker who reads the METAR is always right. Gas is an equally slow
underlying and is the LEAST toxic because the settle value (AAA's national average) is revealed
**once, on a schedule** — between prints there is literally no new information, so taker flow is
noise and mean-reverts. Burst-1/2's "slow + mean-reverting" heuristic is superseded by
**"lumpy, scheduled, public information"**.

Gas gate + robustness (h=300s, contract-weighted, `KXAAAGASD`, 67 days, 2.30M contracts in-band):
- ungated −0.729¢ · drop 12-13 UTC (post-AAA-print hours; +3.08¢ and +0.65¢ = the only large
  adverse cells) → **−0.875¢** · 21-03 UTC only → −1.071¢.
- split-half: −0.852¢ (first 33d) / −0.896¢ (last 34d). **53/67 days maker-positive.**
- day-bootstrap 95% CI on the gated number: **[−1.164, −0.589]¢** (excludes 0).
- capacity: 64,458 contracts/day median; 32.5k/day in-band+gated; $32k/day notional.

## 3. THE LIP IS A PUBLIC, UNAUTHENTICATED, MACHINE-READABLE FEED (new API truth)
`GET /trade-api/v2/incentive_programs?limit=1000` (+`next_cursor`) — **no auth** — returns every
program: `market_ticker, start_date, end_date, period_reward, target_size_fp,
discount_factor_bps, incentive_type, paid_out`. Pulled **30,000+ programs** (2026-06-12 →
2026-08-15), of which **1,705 markets are LIVE right now**.
- `incentive_type`: liquidity 29,924 / volume 76. `discount_factor_bps` = 5000 (0.50) on 29,915.
- `target_size_fp` = 1000 contracts on 29,298 (300/500/250/5000/10000 on the rest).
- `incentive_description`: new_event 16,822 · (blank) 12,151 · long_dated 885 · series_lip 142.
- Scoring (help centre, verified today): per-second snapshot, **score = order SIZE (contracts)
  × distance multiplier**, reward = your score / all scores × pool. Excluded: Kalshi affiliates,
  **existing market makers**, IBs/FCMs, non-US. Min payout $1. Program ends **2026-09-01**.
- Taking `period_reward` in units of $1e-4 (the only unit that reconciles with the published
  "$10-$1,000/day pool"): **$32,434/day of live pool across 1,705 markets right now.**
- Where the money is (live, $/day): KXNDQHUD & KXINXHUD 1h programs $7,098/day-equiv each
  (=$285 per hour-long program), KXAAAGASD 17 rungs × $150 = **$2,553**, KXRAIN 40 × ~$55 =
  $2,179, KXTRUEV $1,603, KXEURUSDAW $559, KXFEDMENTION 43 markets $453.
- All-time program counts: KXTEMPCHIH 1,982 · KXTEMPDCH 1,981 · KXTEMPLAXH 1,972 · KXTEMPAUSH
  1,690 · KXTEMPNYCH 1,680 · **KXAAAGASD 209** · KXAPRPOTUS 48 · KXCPIYOY 6 · KXBTC15M **0** ·
  KXMLBGAME 0 · commodity dailies (GOLDD/BRENTD/NATGASD/SILVERD) **0**.
- **The composition insight:** Kalshi pays the most, most often, in the weather hourlies — the
  exact family where §2 measures passive quoting at −4.8¢/contract. **The LIP rebate is priced
  as compensation for adverse selection.** The trade is to take the rebate where the toxicity
  is measured to be ZERO OR NEGATIVE (gas), not where it is largest.

### The yield screen (`scripts/lip_screen.py`, live books for all 1,705 markets)
Model: rest 100 contracts at the cheaper side's best price; score = 100 vs the visible book's
Σ size×0.5^|ticks from same-side best|; collateral = 100×price.

| basket | books | deploy | modeled LIP | 10% realization |
|---|---|---|---|---|
| KXAAAGASD all 17 rungs | 17 | $1,075 | $289/day | $29/day |
| KXEURUSDAW (20) | 20 | $259 | $386/day | $39/day |
| KXRAIN (67) — TOXIC | 67 | $1,108 | $819/day | $82/day |
| **greedy $50 sleeve, toxic families excluded** | **10** | **$50** | **$216/day** | **$22/day** |
| greedy $93 (full bankroll), toxic excluded | 18 | $93 | $317/day | $32/day |

Books are near-empty: KXAAAGASD-26JUL28-4.100 has a total book score of **90 contracts** against
a $150/day pool. The competition that does exist is visible and identifiable: single 2,004-lot
resting bids at 1¢ and 3,309 at 98¢ (i.e. ~$20-30 of collateral farming a $100 pool) plus 1-lot
ladders at 18/21/24/26/31/36/41¢ — someone is already running the cheap-side farm, at size, and
nobody is quoting the middle.

## Ledger

| # | idea | mechanism / fish | kill-test | numbers | verdict |
|---|------|------------------|-----------|---------|---------|
| **H15** | **LIP-yield farm: rest 100 lots at the cheap side's best on incentivized books whose passive flow is measured non-toxic** | Fish = the venue itself. Kalshi pays $32k/day to rent resting size because SIG/Jump are program-EXCLUDED and thin books are unquoted. Score is per-CONTRACT while collateral is per-DOLLAR, so the payout is decoupled from the risk. We are eligible (US retail, not an "existing MM"). | Public program feed × live books × on-disk toxicity per family | 1,705 live markets, $32,434/day pool; $50 sleeve of 10 non-toxic books modeled **$216/day**, worst case loss = the $50 collateral; gas arm additionally earns **+0.875¢/contract** on any fill | **TRADE-shaped, probe-first** (see gates G1-G4) |
| **H16** | **Gas-daily maker house (KXAAAGASD)** — two-sided quote in-band, pull 12-13 UTC | Fish: retail churning a market whose settle (AAA national average) receives NO information between daily prints; flow is noise and mean-reverts. Maker fee now 0, so the whole spread is kept. | Markout + hour gate + split-half + day bootstrap | n=96,395 in-band fills / 2.30M contracts / 67d; gated markout −0.875¢ (CI [−1.164,−0.589]); 53/67 days positive; 32.5k contracts/day of gated flow; spread 3¢ (half-spread proxy 1.5¢) | **TRADE-shaped** (best on-disk house family we own; composes with H15 — same books, same quotes) |
| **H17** | Weather house (burst-1 H5, "quote KXHIGH off the ensemble feed") | Claimed fish: quoters who don't read NWS. Reality: the settle value is revealed continuously and publicly all day, so the TAKER is the one reading it. | Same markout, 2,418 weather tickers | markout **+4.035/+4.761/+5.681¢** at 60/300/1800s, **adv% 58-61%**, n=95,494 in-band — worse than crypto. Half-spread is 1.5¢: the maker loses ~3.3¢/fill net. | **DEAD-with-numbers** (kills burst-1 H5; and it explains why weather is the LIP's biggest spend) |
| **H18** | **The 1¢-spread unlock**: burst-2's "spread ≥2¢" gate was a fee artifact | At 1¢ spread a two-sided maker grossed +0.12¢/round-trip under the 0.44¢ maker fee → uninvestable. At $0.00 it grosses **+1.00¢**, an 8× change with no new signal. 62.6% of all exchange volume trades at exactly 1¢ spread; another 22.5% sub-cent. | Catalog census + fee arithmetic | 47.9M of 76.5M contracts/24h sit in 1¢ books; the gate change multiplies the addressable flow by ~6.7× | **CONDITIONAL** — gate: queue priority. At 1¢ there is no price to improve; fills go to time priority against bots (KXMLBGAME, KXPGATOUR are 1.00¢/0.41¢ books = the graveyard's arbed-at-every-size crowd). Do NOT deploy in the liquid 1¢ books; DO delete the ≥2¢ gate for the thin incentivized ones. |
| **H19** | **LIP capital-efficiency law**: score ∝ contracts, collateral ∝ price ⇒ quote the CHEAP side | 100 contracts resting cost $1 at a 1¢ bid and $50 at 50¢, for the same size score. Census: 952 active markets have a ≤5¢ cheap side with 24h volume ≥500; 100 lots on ALL of them costs **$2,231** vs **$34,083** for 100 lots on 690 mid-priced books — **15× more score per dollar**, and the max loss per book is the 1-5¢ paid. | Catalog census + live books | existing cheap-side top-of-book size p25/p50/p75 = 67/550/3,499 (we would be joining, not creating, the queue) | **CONDITIONAL** — gate G2 (distance metric). If distance is measured to the MIDPOINT rather than to the same-side best, a 1¢ bid on a 25¢-mid book scores ~0 and the law inverts to "quote the middle". This single unknown moves the flagship by ~10×. |
| **H20** | Re-verdict of burst-2's two house books at fee=0 | The fee cut raises both, but the honest half-spread proxy lowers approval more than the fee raises it. | Same markout engine, measured (not assumed) half-spread | KXCPIYOY: **+0.93..+1.43¢/fill**, adv% 25 → holds, and it is LIP-listed (6 programs). KXAPRPOTUS: **−0.14..+0.54¢/fill**, markout +0.043¢ at 60s and +0.137¢ at 1800s (both adverse), adv% rising 31.8→37.3 with horizon → **thin to nil**; burst-2's +0.60¢ assumed a 1.0¢ half-spread the tape does not support (0.5¢). | **CONDITIONAL(downgrade)** — CPI arm survives; **the approval arm should not be the probe vehicle.** Swap the house probe's vehicle 1 from KXAPRPOTUS to KXAAAGASD. |
| **H21** | Maker ceiling = taker ceiling + 0.07·P(1−P) (applies to streak's resting leg) | Every price ceiling nestor computed from a taker's break-even is **1.7¢ too low for a resting order** at 40-50¢. The fitted policy rests at 40¢ (free side) and takes at 46¢ (taxed side) — the fee-optimal assignment is the reverse: rest at the HIGHER price. | Fee arithmetic vs the fitted-execution tape | fee at 46¢ = 1.74¢; a maker fill at 47.7¢ ties a taker fill at 46¢. The tape shape (opens ~53, dips 44-47 within ~5s, sweeps up 55%) says a 40¢ rest is far below the dip and rarely fills. | **CONDITIONAL** — gate: rerun the fitted-policy capture curve (`work/verify-streak-6mo.md`, 1,951 signals) with the resting rung at 46-47¢ and maker fee 0, measuring P(touch) and the swept-window conditional win rate. NO nestor change proposed until that number exists. |
| **H22** | Free maker leg resurrects sub-fee arbs (dutchbook, cross-venue, ladder seams) | If one leg rests, its fee vanishes — does the 0.65¢ median arb clear? | `dutchbook_paper.jsonl` (n=32 episodes) | Those arbs live at 92-98¢ where the TOTAL two-leg fee is already only **0.22¢/contract**; a free maker leg saves ~0.11¢ against a 0.65¢ median edge, while resting converts controllable leg risk into uncontrollable fill timing. The fee was never the binding constraint at the extremes (§1). | **DEAD-with-numbers** (and generalizes: never site a maker-fee idea at extreme prices) |

## Verdict summary
- **2 TRADE-shaped:** H15 (LIP-yield farm) and H16 (gas maker house) — and they are the SAME
  quotes on the SAME 17 books: the rebate and the spread edge are collected by one order.
- **2 DEAD-with-numbers:** H17 weather house (+4.8¢ adverse, n=95k — kills burst-1 H5),
  H22 free-maker-leg arbs (0.11¢ saving vs 0.65¢ edge).
- **3 CONDITIONAL(named gate):** H18 (queue priority in 1¢ books), H19 (LIP distance metric),
  H21 (streak resting-rung capture curve).
- **1 CONDITIONAL(downgrade):** H20 — KXAPRPOTUS demoted from PROMISING to marginal; CPI holds.

## TOP DOOR (what to build/probe next) — the $20 question worth $30-300/day
**Rest 100 contracts on 2-3 KXAAAGASD rungs for one full program window (12:00→03:59 UTC) and
read the actual reward.** Cost: $1-20 of collateral, worst case = that collateral. It settles
four gates at once, and no amount of analysis can settle any of them:
- **G1 (unit):** is `period_reward` 1e-4 dollars? ($100/rung/16h). One payout observation fixes it.
- **G2 (distance metric):** same-side-best or midpoint? Probe design: rest one 100-lot AT the
  best bid and one 100-lot 3¢ inside on a second rung; the payout ratio reveals the curve.
- **G3 (target_size 1000):** is the pool paid pro-rata regardless, scaled by book/1000, or not
  at all below the threshold? Our 100 lots are 10% of it.
- **G4 (crowding):** the pool is pro-rata; a public feed means the puddle can be found by anyone.
  R152's warning stands and argues for probing NOW — the program ends **2026-09-01** either way.
Existing machinery covers the mechanics: `crates/house` already does GTC+`expiration_ts`+
`taker_at_cross` resting orders, orphan sweep, coid-scoped cancels, −$20 stop (build-house-probe).
Two changes needed: **vehicle = KXAAAGASD (not KXAPRPOTUS, per H20), and size 100 (not 1)** —
100 is the LIP minimum-relevant size and at a 1-19¢ rung costs $1-19, inside any sane risk cap.
Per R153 this belongs in the separate maker binary/subaccount, not in nestor.

## Net-new facts for the Mesh
1. **The LIP is a public unauthenticated feed** (`/trade-api/v2/incentive_programs`) exposing
   30k+ programs with pool, target size and discount factor per market — the venue tells you
   exactly where it is paying for liquidity, and $32,434/day is live right now. This is a
   "visible-to-the-diligent" surface of the same class as the pre-T0 initialized window.
2. **Toxicity is driven by CONTINUOUS PUBLIC INFO ARRIVAL, not by underlying speed.** Weather
   (slow underlying, continuous revelation) is the most adversely-selected book we have ever
   measured (+4.8¢, adv 61%, n=95k); AAA gas (slow underlying, one scheduled revelation) is the
   least (−0.88¢ gated, CI excludes 0, 53/67 days). "Slow and mean-reverting" was the wrong law.
3. **Kalshi's rebate schedule is an adverse-selection price list.** The venue spends most where
   quoting is most toxic (weather hourlies = 9,300 of 30,000 programs). Read the program feed
   as a toxicity map, then take the rebate only where our own tape says the flow is benign.
4. **The equilibrium spread is price-invariant (1-3¢) while the fee is quadratic** → maker-free
   is worth 1.75¢ at 50¢ and ~0.2¢ at the extremes; and 85% of exchange volume trades at ≤1¢
   spread, so the old ≥2¢ house gate was excluding the exchange.
5. **Maker fills are FREE, confirmed on our own tape** (`house_truth_probe.jsonl`: is_taker=false
   ⇒ fee_cost 0.000000, repeatedly), while takers pay exactly 0.07·P(1−P).

## Files
- `~/kalshi_data/scripts/house_markout_families.py` — the family toxicity engine (fee=0, flow
  autocorrelation, measured half-spread, makerA/makerB bracket). Run: `python3 … all`.
- `~/kalshi_data/scripts/lip_screen.py` — pulls every LIP program + live books, ranks markets by
  modeled $/day per $ of collateral.
- Working data: `/private/tmp/claude-501/-Users-ryanwhitehead/449dc817-6064-457d-a116-2df58b67bcb2/scratchpad/house/`
  (`ip_all.json` 30k programs, `books.jsonl`+`books2.jsonl` 1,705 live books, `cat2.json` census).
