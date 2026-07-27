# verify-flycan — FLIGHT-CANCELLATION COUNTER (MUSK M4), Ryan-ordered validation 2026-07-27

Candidate as stated: *"When FlightAware's public counter projects one side of the weekly
KXUSFLYCAN market at 85%+ but it's priced under 65¢, buy that side, hold to settlement."*

Spin-up: [[33 The Mesh]], [[07 Overfitting & Validation Discipline]], `work/lane-MUSK-jul27.md` §M4.
Read-only. Everything below measured today from public GETs (Kalshi API, FlightAware, BTS, Wayback).

---

## 1. SETTLEMENT FINE PRINT (task 1) — exact, from `rules_secondary`

> "For this market, the relevant value is the number shown in the field labeled **“Total cancellations
> within, into, or out of the United States this week”** on FlightAware’s live weekly delay and
> cancellation page **at the time of observation**. This market resolves based on that figure **as
> checked by the Exchange at 5pm EDT on <Friday>**."

| item | finding |
|---|---|
| which number | the **US** line, not the global one. Today: US **436** vs global **3,710** — an ~8.5× difference. Getting the wrong line is a total loss, not a rounding error. |
| MiseryMap | **not** involved. MiseryMap is delays-only and is not referenced anywhere in the rules. |
| page | `https://www.flightaware.com/live/cancelled/week`. **Kalshi does NOT cite the URL** — the rules describe the page in prose only. |
| `settlement_sources` on the series | **WRONG / boilerplate**: it lists BLS Employment Situation and BLS CPI. There is no FlightAware entry in the API's settlement-source field at all. Structural fine-print risk: the only machine-readable settle pointer on this series is incorrect. |
| week boundary | **Saturday-start.** Verified programmatically: the day pages sum EXACTLY to the week counter with Friday EXCLUDED — Sat 119 + Sun 129 + Mon 173 + Tue 14 + Wed 1 = **436 = the week counter**; `minus3days` (Fri) = 128 is not in it. Window = **Sat 00:00 ET → Fri**. |
| timezone | FlightAware renders **ET** (page clock reads `01:38PM EDT`); Kalshi observes at **5pm EDT Friday**. Aligned. |
| the counter is forward-looking | The week counter **already includes cancellations announced for future days** (today it carries Tue 14 + Wed 1). Any run-rate model that treats the counter as "realized so far" is double-counting the front of the tail. |
| mechanics | `close_time` Fri 20:59Z, `settlement_timer_seconds` 1800, `expiration_time` +7d 22:30Z. `fee_type` **quadratic**, `fee_multiplier` 1 → **taker pays 0.07·P·(1−P), maker pays $0**. |
| the hard one | The settle source is a **live, non-archived counter with no history and no API**. Wayback has exactly **one** usable 2026 capture of it. There is no way to audit a past settlement and no way to dispute one. |

---

## 2. THE 10 SETTLED WEEKS (task 2)

Reconstructed from the settled ladders (last YES rung → first NO rung). All 10 settled events
that ever listed markets; `26MAY15/26MAY08/26APR20` exist as events but listed **zero** markets.

| week ending (Fri 5pm ET) | last YES | first NO | **settled FA-US count** | >3000? |
|---|---|---|---|---|
| 2026-05-22 | T3200 | T3400 | **(3200, 3400]** | yes |
| 2026-05-29 | — | T1800 (lowest rung) | **≤ 1800** | no |
| 2026-06-05 | T1000 | T1200 | **(1000, 1200]** | no |
| 2026-06-12 | T4800 | T5000 | **(4800, 5000]** | yes |
| 2026-06-19 | T4400 | T4600 | **(4400, 4600]** | yes |
| 2026-06-26 | T2000 | T2500 | **(2000, 2500]** | no |
| 2026-07-03 | — | T2000 (lowest rung) | **≤ 2000** | no |
| 2026-07-10 | T5500 | T6000 | **(5500, 6000]** | yes |
| 2026-07-17 | T2000 | T2250 | **(2000, 2250]** | no |
| 2026-07-24 | T8000 | T8500 | **(8000, 8500]** | yes |

Unconditional **P(>3000) = 5/10**. Median week ≈ 2,600. Range 1,100 → 8,250 (**7.5×**).

### 2a. The literal rule is NOT backtestable — and that is a finding, not a shrug
The counter has **no public history**. Wayback CDX over `flightaware.com/live/cancelled/week`
returns 6 captures since 2024 and exactly **one** inside the Kalshi era:
`20260721104448` → Tue 2026-07-21 06:44 ET, **US = 5,311**, week that settled (8000, 8500].
So across the 10 settled weeks there is **one** mid-week counter observation. n=1. A "projects
85%+" rule cannot be evaluated on n=1, and it cannot be calibrated at all.

### 2b. So the run-rate premise was tested on real daily data instead — BTS
Pulled BTS On-Time Performance monthlies (2025-05..08, 2026-03..05; 7 × ~31 MB, parsed to daily
cancellation counts, **zips deleted**) → **215 days, 29 complete Sat→Fri weeks**.

**Day-of-week shape (mean share of the weekly total, n=29):**

| Sat | Sun | Mon | Tue | Wed | Thu | Fri |
|---|---|---|---|---|---|---|
| 12.3% | 15.7% | 17.9% | 12.5% | 11.1% | 14.0% | 16.3% |
| (med 7.1) | (11.6) | (14.2) | (10.6) | (7.8) | (10.3) | (11.7) |

Mean ≈ flat (14.3% = 1/7), **but every day's sd of share is 10–15 points** — i.e. the mean shape is
uninformative and the week is dominated by *which* days a storm lands on. Sat+Sun+Mon ≈ **46%** of a
mean week, so a naive `×7/3` linear extrapolation is roughly **unbiased in the median** and useless
in the tail:

**Linear (×7/3 from end-of-Monday) projection ÷ actual final, n=29: median 0.95, mean 1.07,
range 0.27 → 1.97.** The projection is off by more than 2× in either direction in a third of weeks.

**The multiplier that actually matters — final ÷ (Sat+Sun+Mon), n=29:**
mean **3.10**, median **2.45**, **min 1.19, max 8.56**, p90 7.42.

**A mid-week run-rate can therefore essentially never reach 85% confidence** except at strikes far
outside the 1.2×–8.6× envelope. The premise of the candidate rule is structurally broken: the
projector it assumes does not exist.

### 2c. The three real mid-week-vs-outcome checks that do exist

| week | mid-week observation | naive linear projection | actual | error |
|---|---|---|---|---|
| ending 2026-07-24 | FA counter Tue 06:44 ET = **5,311** (48.9% of the Sat→Fri clock elapsed) | 10,860 | (8000, 8500] | **overshot +32%** |
| ending 2026-05-22 | BTS Sat+Sun+Mon = 540 | ×7/3 → 1,260 BTS ≈ 1,500 FA | (3200, 3400] | **undershot −55%** |
| ending 2026-05-29 | BTS Sat+Sun+Mon = 682 | ×7/3 → 1,591 BTS ≈ 1,900 FA | ≤ 1800 | ~right |

Both directions, ±2×, on three points. Consistent with the 29-week envelope.

### 2d. What IS in the tape: the ladder is systematically YES-overpriced mid-week
Pulled every trade on all 126 settled markets (**3,549 trades**). Last trade at or before each
weekday 17:00 ET vs the settled result, one observation per rung, then aggregated **per week**
(rungs inside a week are perfectly correlated — week is the unit):

| snapshot | rungs | week-level buy-NO edge (mean) | median | weeks positive |
|---|---|---|---|---|
| Mon 17:00 | 51 | **+17.2¢** | +25.5¢ | **6 / 8** |
| Wed 17:00 | 73 | **+17.0¢** | +30.8¢ | **7 / 9** |

Restricted to the candidate's own price band (YES 0.35–0.85), Wed 17:00: **+34.8¢ per rung** across
15 rungs / 6 weeks, week-level +13.0¢, 4/6 weeks positive.
Calibration is monotone and one-sided: at Wed 17:00, YES at 0.59 realized 0.17; YES at 0.79 realized
0.50; even YES at **0.98** realized only **0.74**. Buying YES was negative in every bucket above 10¢.

**Direction: the money is SELLING YES / BUYING NO, not "buying the projected side."**
But **7/9 weeks is p≈0.09 under a fair coin** — this is a 9-week sample and does **not** clear note 07.

---

## 3. THE LIVE WEEK, PRICED (task 4)

Live 2026-07-27 13:38 ET. FA US week-to-date = **436** (Sat 119, Sun 129, Mon-to-1:38pm 173,
+15 already booked for Tue/Wed). Kalshi `KXUSFLYCAN-26JUL31`, close Fri 20:59Z.

Fair YES from the **12 comparable low-start weeks** (BTS Sat+Sun+Mon ≤ 550) — the multiplier is
scale-free so the FA↔BTS level difference cancels. Base = 470 (Sat+Sun+**full** Mon estimate):

| rung | needs mult | fair YES (n=12) | 95% CI | all-29 base rate | mkt yes bid/ask | sell-YES EV @bid | EV at worst CI |
|---|---|---|---|---|---|---|---|
| T1000 | 2.13× | 0.83 | .62–1.00 | 0.59 | 0.95 / 0.98 | +11.7¢ | −5.0¢ |
| T2000 | 4.26× | 0.58 | .30–.86 | 0.24 | 0.81 / 0.87 | +22.7¢ | −5.2¢ |
| **T3000** | **6.38×** | **0.25** | **.01–.49** | 0.10 | **0.59 / 0.68** | **+34.0¢** | **+9.5¢** |
| T4000 | 8.51× | 0.17 | .00–.38 | 0.07 | 0.26 / 0.40 | +9.3¢ | −11.8¢ |
| T5000 | 10.6× | 0.00 | — | 0.00 | 0.11 / 0.21 | +11.0¢ | +11.0¢ |

T3000 is the only rung positive at the lower CI bound. Sensitivity: at base 408 it is +34.0¢
(+9.5¢ worst); at base 530 it falls to +17.3¢ (**−10.6¢ worst**) — i.e. **the entire verdict turns on
the exact Monday-evening counter value**, which is exactly what nobody has been recording.

### The book kills it anyway
Full depth (`orderbook_fp`; plain fields read None), 2026-07-27 13:38 ET —
**buy-NO ladder = price × size:**

```
T3000   0.41x2   0.43x5   0.49x1   0.50x2   0.54x4      (yes bid 0.59 / ask 0.68 — 9¢ wide)
T4000   0.73x1   0.74x5   0.75x25  0.85x25  0.90x30
T5000   0.89x5   0.90x51  0.95x3
T2000   0.19x5   0.20x16  0.35x25  0.40x100
```

At the money rung there are **2 contracts** at the good price and **~14 contracts** before the price
runs past fair. Total takeable edge across the whole ladder ≈ **$15–25 of expected value**, gross.
Taker fee at 0.59 is 0.07·0.59·0.41 = **1.7¢/contract**. OI: T3000 479, T4000 38, T5000 9 —
the OI is real but it is *resting elsewhere*, not offered.

Maker route: the series is plain `quadratic`, so **resting costs $0 in fees**. An offer to sell YES
inside the 0.59/0.68 spread is the only way to size this at all; T3000 has traded 492 contracts in
9 days (~55/day), so tens of contracts a week is the honest ceiling. That is a **$20–50/week** door
on a 4-day capital lock — below the threshold at which it is worth an execution slot.

---

## 4. VERDICT

**The rule as written: DEAD — STRUCTURAL.** Not because the direction is wrong, but because the
instrument the rule depends on does not exist: (a) there is no history of the settle counter, so a
"projection" cannot be calibrated or backtested (n=1 archived mid-week observation in 10 weeks);
(b) the 29-week run-rate multiplier envelope is **1.19×–8.56×**, so a mid-week run rate cannot
produce an 85% claim at any tradeable strike; (c) the rule says *buy* the projected side, and the
tape says the money is on the **sell** side (buy YES was negative in every price bucket above 10¢).

**The residual the kill exposes: CONDITIONAL(gate) — sell-the-YES-ladder, not buy-the-projection.**
Mechanism named: the ladder prices something close to the *unconditional* weekly distribution
(P(>3000) = 5/10 all-weeks) and updates too slowly for the *conditional* one (P(>3000 | quiet
Sat–Mon) ≈ 0.25, n=12). Week-level +17¢, 7/9 weeks, at both Mon and Wed snapshots.

Gates, all of which must clear before any size:
1. **n.** 9 weeks / 12 conditioning weeks. Note 07 bar is n≥60 with an honest split. 7/9 is p≈0.09.
   Only forward weeks fix this. **This is a capture-first idea, not a trade-now idea.**
2. **The counter, recorded.** Every week not captured is permanently lost. The Friday 16:55 ET
   snapshot is the single load-bearing observation (it is what the Exchange reads).
3. **Book, not tape.** The +17¢ is from the trade tape. Measured resting depth today is
   **2 contracts** at the good price. The maker fill rate at an offer inside the spread is unknown
   and is the real capacity question.
4. **BTS↔FA proxy.** The multiplier distribution is BTS domestic-reporting-carrier; the settle is
   FlightAware within/into/out-of-US (~1.1–1.3× the level). Ratios are scale-free only if the
   FA/BTS ratio is day-of-week stable — untested. Two weeks of captured counter + the day pages
   settles it directly.
5. **Seasonality.** All 29 BTS weeks are Mar–Aug. Winter (the 2026-01-26 archive shows a **11,610**
   single-day US figure) is a different animal; do not carry these multipliers into Q4/Q1.

**Capacity ceiling regardless of gates: ~$20–50/week.** This is a fish-sized pond, not an engine.

---

## 5. CAPTURE READINESS (task 3)

`~/kalshi_data/scripts/flycan_capture.py` — written, **dry-run verified**, **NOT scheduled**.

- Appends two tiny JSONL files: `flycan_counter.jsonl` (week counter + all 7 day pages + page clock
  + tab labels) and `flycan_books.jsonl` (full ladder: bid/ask/OI/vol + top-6 resting size both sides).
- Built-in integrity check `day_sum_matches_week` — recomputes the Sat-start decomposition every run
  and flags the day FlightAware changes the window definition. Currently **true**.
- Honours the machine notes: curl+browser-UA (FA 403s a bare fetch), `*_dollars`/`*_fp` only,
  `expect_key` validation on every Kalshi page (429 bodies parse as JSON), 3× retry on FA.
- Nothing written on `--dry`. No files created by this lane.

**Suggested arming (Fable's call, VPS is UTC):**
```
5  13 * * *   ~/kalshi_data/.venv/bin/python ~/kalshi_data/scripts/flycan_capture.py   # 09:05 ET
55 20 * * *   ~/kalshi_data/.venv/bin/python ~/kalshi_data/scripts/flycan_capture.py   # 16:55 ET
35 21 * * 5   ~/kalshi_data/.venv/bin/python ~/kalshi_data/scripts/flycan_capture.py   # Fri 17:35 ET, brackets the observation
```
The Friday pair is the one that matters: it is the only way this house ever gets an auditable record
of what the Exchange saw.

---

## 6. MESH DELTAS

- **A settle source with no history is a structurally different animal.** KXUSFLYCAN settles on a
  live counter that is not archived, has no API, and is not even correctly named in Kalshi's own
  `settlement_sources` (which points at BLS). Any strategy of the form "project the settle source"
  is un-backtestable *by construction* on this family. Capture-first or don't touch it.
- **Kalshi `settlement_sources` is not trustworthy.** First measured instance of the field being
  outright wrong. Read `rules_secondary`, not the metadata.
- **BTS On-Time Performance monthlies are a first-class free daily-flight-operations source**
  (`transtats.bts.gov/PREZIP/On_Time_Reporting_Carrier_On_Time_Performance_1987_present_YYYY_M.zip`,
  ~31 MB/month, 200 OK with a browser UA, ~2-month publication lag — 2026_5 live, 2026_6 404).
  Columns are `FlightDate` / `Cancelled` (NOT `FL_DATE`). Delete the zips after parsing (disk).
- **Scale-free ratios beat level calibration.** BTS and FlightAware measure different flight
  populations, but `final ÷ week-to-date` cancels the level entirely — the right way to port a proxy
  dataset onto a settle source you cannot observe historically.
- **Weekly-accumulator markets: the mid-week run rate is not a projector.** 29 weeks, multiplier
  1.19×–8.56×. Whenever a Mesh idea says "the run rate tells you the answer", this number is the
  prior that kills it.
- **Wayback is a usable but thin settle-source archive.** `/live/cancelled/yesterday` has ~98
  captures (13 usable distinct 2026 days) vs the week page's 1. When a settle page has no history,
  check the *sibling* pages — they are crawled far more often.

---

*Pulls in the session scratchpad (`/tmp/fcw/trades.json` 3,549 trades, `/tmp/fcw/bts_daily.json`
215 days, `/tmp/fcw/weeks.json` 29 weeks). BTS zips deleted. Only file created outside scratch:
`~/kalshi_data/scripts/flycan_capture.py`. No nestor changes, nothing scheduled.*
