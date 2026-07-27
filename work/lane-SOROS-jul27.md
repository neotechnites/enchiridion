# Lane SOROS/EVENT — burst 1, 2026-07-27

Archetype: SOROS (reflexivity) × EVENT-VOL DESK. Charter lane #5: event-day structure around
this week's cluster + the scheduled-print calendar, composing with the event-wings build.
Spin-up: steer-ideation-jul27, [[33 Mesh]] complete, [[34]] SOROS + EVENT-VOL briefs,
[[20]], [[15]] traps, [[38]] DEAD list, lane-EVENT-VOL-burst1, lane-SOROS-burst1/2.
Graveyard honoured: EIA-day wings, FOMC-move *ladder* buying (KXFED rung), buy-the-jump on
liquid ladders (EV1/EV2), intra-venue spike-fade (S3), outcome-reflexivity (S8) — none re-run.

---

## HEADLINE 1 — THE CALENDAR IN THE BUILD IS OFF BY A DAY (and off by an instrument)

Verified against Kalshi's own settled-ladder close times + public schedule:

| when (2026) | what |
|---|---|
| **Wed Jul 29 18:00Z** | FOMC statement (14:00 ET) |
| **Wed Jul 29 18:30Z** | Warsh press conference + Q&A (14:30 ET) |
| **Wed Jul 29 ~20:05Z** | **MSFT + META** earnings, after the close |
| **Thu Jul 30 12:30Z** | **GDP Q2 advance + PCE deflator + initial claims** (08:30 ET, triple) |
| Fri Jul 31 12:30Z | Employment Cost Index |
| Fri Aug 7 12:30Z | NFP |

[[40 LIVE STATE]] §"THE IMMEDIATE WORLD" item 4 says *"Wed PCE/GDP + MSFT/META wings"*.
**PCE/GDP is THURSDAY.** Wednesday is FOMC + earnings. [[15]] §queue item 2 had the date right
(Jul 30) but nothing carries the weekday. If the wings go on Wednesday morning expecting a
12:30Z macro print, they are on a day with no 08:30 print at all.

## HEADLINE 2 — THE 08:30 ET MACRO SLOT IS ONE OF THE **QUIETEST** 30-MIN WINDOWS OF THE CRYPTO DAY

2yr 5-min BTC/ETH (2024-07-23 → 2026-07-22, n=522 weekdays), `soros_evt_jump.py`:

| window (UTC) | BTC med move | BTC P(≥0.75%) | BTC P(≥1.0%) | ETH P(≥0.75%) |
|---|---|---|---|---|
| **12:30-13:00 (08:30 ET macro slot)** | 0.157% | **2.9%** | 0.8% | 8.8% |
| 11:30-12:00 placebo | 0.134% | 2.3% | 1.7% | 4.4% |
| 10:30-11:00 placebo | 0.125% | 1.0% | 0.4% | 5.2% |
| **13:30-14:00 (equity open, NO print)** | 0.324% | **14.2%** | 8.8% | 20.7% |

The unscheduled equity-open window is **5× more likely** to blow through a 0.75% wing than the
scheduled macro-print window. The 08:30 ET release is not a discontinuity in crypto.

## HEADLINE 3 — FOMC IS THE ONLY SCHEDULED PRINT THAT MOVES THE TAPE, AND THE **SECOND** BRACKET IS THE JUMP

Exact FOMC decision timestamps pulled from Kalshi's own `KXFED` settled events (28 meetings;
16 inside the 2yr spot window). Windows below are the **actual tradeable Kalshi hourly bracket**
(entry T−15min before the release, strikes ±X% off spot at entry, settle on the bracket close).
Controls are the identical clock windows on non-FOMC weekdays — clock fully held constant.

| coin | bracket | X | FOMC days | non-FOMC same clock | ratio | Fisher p |
|---|---|---|---|---|---|---|
| BTC | statement (17:45→19:00Z) | 0.75% | 5/16 = 31.2% | 163/1012 = 16.1% | 1.94× | 0.105 |
| BTC | statement | 1.0% | 3/16 = 18.8% | 84/1012 = 8.3% | 2.26× | 0.147 |
| BTC | **presser/Q&A (18:45→20:00Z)** | 0.75% | 5/16 = 31.2% | 146/1011 = 14.4% | 2.16× | 0.072 |
| BTC | **presser/Q&A** | **1.0%** | **5/16 = 31.2%** | **69/1011 = 6.8%** | **4.58×** | **0.0040** |
| ETH | statement | 0.75% | 8/16 = 50.0% | 267/1012 = 26.4% | 1.90× | 0.039 |
| ETH | presser/Q&A | 1.0% | 6/16 = 37.5% | 151/1011 = 14.9% | 2.51× | 0.025 |

**And the wing premium is measured, not assumed** (`hourly/KX{BTC,ETH}D_obs.jsonl`, 68 days,
band 2-20¢, entry-ask vs realized settle):

| coin | lead | avg ask | realized hit | richness |
|---|---|---|---|---|
| BTC | 15 min | 6.84¢ | 5.29% (n=2380) | **1.29×** |
| BTC | 30 min | 8.11¢ | 6.94% (n=2724) | 1.17× |
| BTC | 50 min | 11.37¢ | 9.49% (n=1886) | **1.20×** |
| ETH | 15 min | 4.92¢ | 2.66% (n=1767) | 1.85× |
| ETH | 50 min | 6.87¢ | 5.87% (n=1583) | 1.17× |

Net multiple at the 1.0% strike, presser bracket, BTC = **4.58 / 1.20 ≈ 3.8×**. That is the
whole lane in one number, and it is the only positive number I found.

Corroborating microstructure: the two brackets in question are, on ordinary days, among the
*quietest and cheapest* of the 24 — BTC hour-19Z bracket avg wing 6.9¢ / 3.6% hit / buy-YES
evw −4.36¢; hour-20Z 7.3¢ / 4.5% / −5.14¢. The market prices them as dead afternoon hours,
because 96% of the time they are.

---

## LEDGER

| # | idea | mechanism / fish | kill-test | numbers | verdict | files |
|---|---|---|---|---|---|---|
| **E1** | **Buy the ±1.0% strangle on the crypto hourly bracket containing the FOMC presser (enter 18:45Z Wed Jul 29, bracket closes 20:00Z)** | The one scheduled US event that injects a real jump into 24/7 crypto. Reflexive (SOROS): the Q&A is unscripted, the tape reacts to the tape, and this is only Warsh's 2nd/3rd meeting so no persona prior is priced ([[40]]: Powell-based priors are stale). Fish = the hourly wing seller pricing the normal dead-afternoon base rate — **including our own VOLBOOK** | 2yr 5-min BTC/ETH, exact FOMC timestamps from Kalshi KXFED settled events, tradeable-bracket windows, same-clock non-FOMC control; + measured ask-vs-realized richness at matched lead | BTC 1.0% presser: **31.2% (5/16) vs 6.8% (69/1011), 4.58×, p=0.0040**. ETH 2.51×, p=0.025. Wing richness 1.20× at 50-min lead → net ≈3.8×. Point-estimate EV: 2 legs ≈ 8¢ each at 75-min lead + ~1¢ fees ≈ 17¢ cost vs 31.2¢ expected = **+14¢/contract, ~+82% ROI**. Honest CI: 5/16 → 95% [11%, 59%]; at the low bound the strangle LOSES ~6¢ | **CONDITIONAL — one named gate, resolves in 2 days**: at 18:45Z Wed read the ±1.0% asks. Trade only if the pair costs **≤20¢**; if the book has already lifted them to ≥30¢ the uplift is priced and stand down. Size small (n=16, low cadence) | `scripts/soros_evt_jump.py`, `scripts/soros_evt_brackets.py`, `scripts/soros_evt_calendar.py`, `macro_calendar.json` |
| **E2** | **Bracket selection: buy the SECOND bracket (presser/Q&A), not the first (statement)** — the two-stage-jump refinement | The crowd anchors on the scheduled instant (14:00 ET statement) and treats 14:30-15:00 as post-event calm. The statement is a number the rates market already prices; the Q&A is unscripted commentary. Classic reflexive second-order move | Same test, statement bracket vs presser bracket, both strikes | BTC at 1.0%: presser **4.58× (p=0.0040)** vs statement 2.26× (p=0.147). BTC at 0.75%: presser 2.16× vs statement 1.94×. ETH at 1.0%: presser 2.51× (p=0.025) vs statement 1.88× (p=0.115). Presser dominates on both coins at both strikes, and it is the only cell that clears p<0.01 | **TRADE-shaped refinement of E1.** If only one bracket is bought, buy the presser bracket at the 1.0% strike. Note [[38]]'s "FOMC-move ladder buying DEAD (0/12)" is the **KXFED rate rung** — a different instrument, anchored by Fed futures; it does not cover the crypto hourly wing | same |
| **E3** | Wings on the **weekly jobless-claims** print (every Thu 12:30Z) — the "weekly cadence gives 4× the n" hope (EVENT-VOL EV5) | Weekly official print, same 08:30 ET slot, cadence 52/yr instead of 12 | 2yr, Thursday 12:30Z vs all weekdays and vs same-day clock placebos, calendar-free (claims is guaranteed every Thursday) | **BTC 12:30-13:00Z Thursdays: P(≥0.75%) = 0.0%, 0/104 over two years.** Median move 0.130% — the *lowest* of the five weekdays. At the 45-min tradeable bracket: Thu 5.8% vs all-weekday 5.0% (no uplift). ETH: Thu 14.4% vs all-weekday 14.2% — identical to three significant figures | **DEAD (structural, n=104).** Weekly claims injects zero measurable jump. Closes EVENT-VOL EV5; the cadence hope was the whole point of it and there is nothing to accrue | `scripts/soros_evt_jump.py` |
| **E4** | Wings on **Thu Jul 30's GDP + PCE + claims triple print** — i.e. the trade the build is actually scheduled to place | Three prints in one 12:30Z slot should be a strictly larger discontinuity than any single print | 2yr 12:30Z bracket; named slices: PCE/GDP window (dom ≥26 or =1), CPI window (dom 10-15), NFP (first Friday) | BTC 45-min bracket base **P(≥0.75%) = 5.0%** vs measured wing richness 1.20× → a 0.75% strangle costs ~2×11¢ against a 5% payoff. Slices (30-min): PCE/GDP window 4.6% vs 2.9% baseline (n=108, ~5 real events); **CPI window 2.0% vs 3.1% — LOWER**; NFP first-Friday 4.2% = 1/24 vs 2.8%. ETH: CPI window 8.8% vs 8.8%, PCE window 9.3% vs 8.8% | **DEAD as a standalone at 2yr power.** No 08:30 ET print family clears the premium. Compose instead: move the wing budget to Wednesday's FOMC brackets (E1/E2) | `scripts/soros_evt_jump.py` |
| **E5** | Re-audit the **event-wings evidence base itself** before forward-testing it again | [[15]] trap #6 already flags it as a one-event edge. I unpacked the primary artifact leg by leg | Read `ev_wing_results.json` (64 attempted legs, 24 traded) and check every leg's entry-quote AGE against [[15]] trap #2 (print age must be ≤30s) | **16/16 control legs LOST**, avg −8.40¢/leg. 8 event legs avg +30.6¢ — but 100% of the P&L is 3 winning legs, all on **one date** (2026-07-14 CPI) across two regime-correlated coins ([[33]]: pooled cross-asset significance is fake). **Every winning leg's entry was a STALE quote: up_age = 171 min (BTC 0.75), 528 min (ETH 1.0), 141 min (ETH 0.75).** Losing legs had fresh ages (2-15 min). A 1¢ ask 8.8 hours old is not a touchable ask | **DEAD / graveyard correction.** The event-wings edge is n=1 *and* trap-#2 contaminated. Forward-testing Jul 30 tests a hypothesis whose only positive evidence is a stale-quote artifact. Recommend [[38]] "OUTSIDE THE FUNNEL" entry be re-labelled | `ev_wing_results.json` (existing) |
| **E6** | **MSFT/META earnings-gap wings** on the index ladder | Buy the NDX bracket wings before a megacap after-hours print; [[33]] says "earnings gaps flow into NDX bins next morning" | Read the actual listing/close times of the index brackets that surround the release (live API) | `KXNASDAQ100-26JUL27H1600`: **open 2026-07-25T02:30Z → close 2026-07-27T20:00Z**. `KXNASDAQ100-26JUL28H1600`: **open 2026-07-28T02:30Z**, status `initialized`. Every daily index bracket opens 02:30Z on its own trading day and closes 20:00Z. **No bracket spans the 20:00Z→02:30Z after-hours window.** Hourly index ladders (`KXINXU`/`KXNASDAQ100U`) exist only for close hours 14-20Z = 10:00-16:00 ET — also no overnight coverage. Single-stock earnings-move series do not exist: `KXMSFT`, `KXAAPL`, `KXEARNINGS`, `KXMSFTEARN` all return **0 open events** (`KXMETA` is a DAP/head-count series, not price) | **DEAD (structural, no venue).** There is no Kalshi instrument that contains a megacap after-hours earnings release. MSFT/META report ~20:05Z Wed; the first ladder that can hold the gap lists 02:30Z Thu, ~6.5h later, with strikes and prices already set post-gap | live API probe (inline) |
| **E7** | The seam E6 leaves behind: **the 02:30Z listing grid on a gap night.** Is the strike grid centered on the stale prior 16:00 ET close, or on post-gap futures? | [[33]]: "new listings are sloppy for ~48h" × an overnight earnings gap. If Kalshi anchors the grid to the previous cash close while NQ futures have already moved 1-2% on MSFT/META, the whole ladder lists mis-centered and the gap-side rungs are stale from the first quote. Fish = whoever quotes the ladder off its own listing defaults | Live probe at 02:30-03:00Z Thu Jul 30: read `KXNASDAQ100-26JUL30H1600` strike grid + first quotes, compare grid center against (a) Wed 16:00 ET NDX close and (b) NQ futures at 02:30Z. Mis-centering ≥0.5% = the trade. Constructed-ticker fetch works pre-T0 (the Jul-28 initialized event returned 30 markets on one call and 0 on the next — the list index is eventually-consistent, confirming [[33]]'s pre-T0 mechanic for the INDEX family too) | NOT RUN — needs a 02:30Z snapshot that does not exist on disk. Cost to close: 2 API calls at 02:35Z Thu | **CONDITIONAL — named probe, 2 calls, Thu 02:35Z.** Generalizes beyond earnings: every index ladder lists at 02:30Z on overnight-stale information | — |
| **E8** | Run E1/E2 on the **index hourly ladder** (`KXINXU` / `KXNASDAQ100U`) instead of crypto — same bet, cheaper premium | The equity index is the *native* FOMC instrument; crypto only imports the move. And the index hourly wing is priced far closer to fair than the crypto one | `wing_obs_KXINXU.jsonl` / `wing_obs_KXNASDAQ100U.jsonl` wing calibration (band 2-20¢) | **KXINXU: avg ask 5.41¢ vs 5.15% realized hit = 1.05× richness (n=233) — essentially FAIR**, vs BTC's 1.20-1.29×. KXNASDAQ100U 9.56¢ vs 5.34% = 1.79×, but median quote age 1910s (32 min) — unusable prices, NDX hourly is too thin. The FOMC hour IS covered (close_hr_utc 19 present: 229 KXINXU / 178 NDX obs). **Blocker: only 9 dates (Jul 10-22), zero FOMC days, and no 2yr SPX intraday on disk** → the index's own FOMC vol multiple is unmeasured; the crypto multiple cannot be assumed to transfer | **CONDITIONAL — needs the index FOMC multiple.** Named gate: pull 2yr SPX/NDX 5-min (or capture forward) and repeat the E1 test on the index. If the index multiple is ≥2× at 1.05× premium, this strictly dominates the crypto version. Cheap next-burst pull | `wing_obs_KX*U.jsonl` (existing) |

---

## TOP DOOR

**E1+E2: the ±1.0% strangle on the crypto hourly bracket containing the FOMC press conference
(BTC, enter 18:45Z Wed Jul 29, bracket closes 20:00Z).** Only positive cell in the lane;
4.58× realized-tail uplift against a measured 1.20× premium, p=0.0040, and it is actionable in
two days. It composes with the wings build by *redirecting* it: same instrument class, same
±X% strangle shape, same T−15min entry — different day (Wed not Thu), different clock
(18:45Z not 12:15Z), different strike (1.0% not 0.75%).

**Gate (must be checked, cost = one quote read):** at 18:45Z, if the ±1.0% pair costs ≤20¢,
take it; at ≥30¢ the uplift is already in the ask and stand down.

**Honest weaknesses, stated up front:**
- n=16 FOMC meetings. 5/16 → 95% CI [11%, 59%]; the lower bound loses.
- BTC and ETH share regime ([[33]] graveyard) — this is ~16 events, not 32.
- **Cadence caps it at 8 trades/yr.** This edge can never accrue n on its own. It is a
  Class-B, low-frequency sleeve, not a system — size it accordingly.
- Warsh is 2-3 meetings into the chair. The 16-meeting sample is mostly Powell. The direction
  of that bias is unknown; a new chair's Q&A is plausibly *more* volatile, but that is a story,
  not a number.

## WHAT THIS LANE KILLED (money saved)
Jobless-claims wings (0/104 at 0.75%, two years) · the Thu Jul 30 GDP+PCE+claims wing trade as
specified (5.0% hit vs ~1.20× premium) · MSFT/META gap wings (no venue: measured listing times,
no bracket spans the after-hours window, no single-stock series) · and the event-wings evidence
base itself (n=1, on stale quotes of 141-528 minutes).

## COST
3 scripts, 2 disk passes over 2yr 5-min BTC/ETH + 68-day Kalshi hourly obs + 9-day index hourly
obs + `ev_wing_results.json`, ~180 API calls (calendar build + live venue probes), 2 web
searches (calendar confirmation), 0 scouts.
