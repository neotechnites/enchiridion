# Lane MUSK — Ideation burst 2026-07-27 (census refresh + COUNT-family playbook)

Archetype: MUSK — first principles. A market IS a settle source + a rulebook + fees. Charter lane #3:
what is NEW on the exchange since the Jul-24 census (12,179 baseline), and what does the COUNT-family
playbook do to it. Spin-up: [[33 The Mesh]] complete, [[34]] MUSK brief, [[32]] §9 exemplar, [[20]], graveyard.

**Everything below is measured today (2026-07-27) from public GETs + `~/kalshi_data`. No estimates presented as data.**
API truth honoured throughout: plain `yes_ask`/`open_interest`/`volume` read None; only `*_dollars`/`*_fp` are live.

---

## 0. THE CENSUS DELTA (the map, refreshed)

| quantity | Jul 24 baseline | now (Jul 27 ~16:00Z) | delta |
|---|---|---|---|
| series (`/series` full pagination) | 12,179 | **12,191** | **+12, −0** |
| open markets (`/events?status=open&with_nested_markets`) | 76,186 | 68,142 | — |
| markets created since Jul-24 16:14Z | — | **15,018 across 399 series** | the real churn |
| series with ≥1 open market | 3,001 | 2,980 | 95 cold-started, 116 went dark |

**+12 new series in 72h** (8 caught by `listing_monitor` before it stopped Jul-26 15:46, 4 caught by my live diff today):
`KXEARNINGSMENTIONGRAB`, `KXACQANNOUNCEOPENR`, `KXRPRESPRIMARY`, `KXDPRESPRIMARY`, `KXHOBBYTEMP`,
`KXUSDRESERVE`, `KXUSDINTLPAY`, `KXUSGDPSHARE`, `KXWIDGOV2ND`, `KXFISHBACKBALLOT`, `KXSENATEADJOURN`, `KXAKSEN1`.
By category: Economics 3 · Elections 3 · Politics 2 · Mentions 1 · Financials 1 · Weather 1 · (1 misc).

**Fee-schedule delta: ZERO.** 12,061 `quadratic` + 130 `quadratic_with_maker_fees` + the same 13
`fee_multiplier=0` series as burst-1 (`KXBTCY/KXETHY` + 11 one-off geopolitics). No new zero-fee series,
no series changed fee type. M2's zero-fee wake-up trigger has **not** fired.

**Instrument distrust (the MUSK move, done twice):**
1. `listing_monitor` stopped Jul-26 15:46 local — the "monitor is running" assumption was false; the last 21h
   were only recoverable by a live catalog diff. (Fix: it needs to move to the VPS with the rest.)
2. 95 series look like "wake-ups" against the Jul-24 open-market catalog. Only **2** are baseline artifacts
   (`KXB65/KXB85`, oldest live market Jul-08); **93 are genuine cold starts** — but 88 of those are the ordinary
   listing cadence (UCL/UEL fixtures listed 03:16Z today, FX weeklies 13:25Z, hourly commodities 14:00Z).
   **A wake-up detector that does not subtract the cadence is 93% noise.**

**First-principles verdict on the census as a strategy generator:** the series catalog is nearly static
(+4/day, mostly one-off politics). The churn is 5,000 markets/day and ~99% of it is recurring cadence.
The census refresh is *not* an idea machine by volume — it is worth exactly the 4 doors it located below.

---

## 1. LEDGER

| id | idea | mechanism / WHO is the fish | cheapest kill run | numbers | verdict |
|---|---|---|---|---|---|
| **M1** | Census refresh itself | Recon: does 72h of exchange churn contain a new engine? | Full `/series` diff vs `listing_known.json`; market-level `created_time` diff vs `catalog_open_202607.json`; cadence subtraction | +12 series / −0; 15,018 new markets / 399 series; 93 cold starts of which 88 = cadence; fee schedule unchanged | **DEAD as a generator, LIVE as a locator** — 4 doors below came out of it |
| **M2** | `KXHEARINGMENTION` post-lock ratchet (the LASTWORDCOUNT triad, transferred) | Word spoken at a hearing = mathematically decided; if the market stays open on a lagged transcript, buy decided winners. Fish = transcript-watchers | 35 finalized events, 561 rungs, all trades pulled | **DEAD.** Rungs early-close *at the mention*: median lock-lead to ≥95¢ = **10,102 s (2.8 h)**, p10 = 4,494 s, **min 1,973 s (33 min)**. Across **300 YES rungs, only 16 trades ≤90¢ existed in the final 30 min**. Listed `close_time` (e.g. Aug-13) is a placeholder overwritten by early close — YES rungs close at the word, NO rungs at gavel-down. Zero post-lock window | **DEAD-with-numbers** (extends B1's earnings-MENTION kill to the whole hearing family) |
| **M3** | **`KXHEARINGMENTION` sell-the-lift (the residual the kill exposed)** | The lock is efficient, but the *path* is not: retail lifts "will they say X?" YES tickets all through the hearing at prices far above the realized hazard. Fish = the in-hearing YES-buyer | Same 561-rung tape; condition on the last trade ≥2h before gavel-down, drop already-decided rungs (px ≥0.95 or ≤0.02) | **n=226 yes-taker lifts: mean px 0.545 → realized YES 0.323 = +22.2¢/contract for the seller.** Monotone in price: 0.2-0.4 +18.7¢ (n=43) · 0.4-0.6 +24.3¢ (n=52) · 0.6-0.8 +34.0¢ (n=51) · 0.8-1.0 +25.4¢ (n=54). **22/26 events positive, median +14.2¢, worst −9.2¢.** Deepens with time: at T-60min every bucket 0.1→0.8 realized **zero** YES (n=89) | **TRADE-shaped — top door** (gates below) |
| **M4** | `KXUSFLYCAN` weekly accumulator vs the live public counter | Weekly cancellation total accrues Sat→Fri and is displayed live on a free page; the ladder prices roughly the *unconditional* weekly distribution. Fish = whoever hasn't opened FlightAware | Fetched the settle page; reconstructed the weekly distribution from 10 settled Kalshi ladders; read the intra-week price path on 6 rungs | Live counter now: **US = 409** week-to-date (global 3,649); daily US = 150 today (part-day), 129 Sun, ~130 Sat. Settled weeks: 8000-8500, 5500-6000, 4800-5000, 4400-4600, 3200-3400, 2000-2500, 2000-2250, 1000-1200, <1800, <2000 → **P(>3000) unconditional = 5/10**. Live ladder: T1000 .95/.98 · T2000 .81/.88 · **T3000 .54/.67** · T4000 .26/.40. To settle >3000 needs **+2,591 in 4 days = 648/day vs a 140/day run rate**. Precedent: JUL17-T3000 was 0.48 Monday → 0.06 Thursday → NO | **CONDITIONAL(gate)** — live read says sell T3000/T4000; gate = 2 daily counter snapshots to confirm the Sat-start week boundary + run rate before sizing |
| **M5** | `KXARCTICICEMIN` deep-wing sale (new listing, Jul-25) | Public daily NSIDC CSV + a computable melt trajectory + a crowd that fears the ice-collapse tail. The Mesh's measles shape, on a settle source with a 48-year free CSV | Pulled the full `N_seaice_extent_daily_v4.0.csv` (15,792 rows); OLS min ~ Jul-26 extent, 2007-2025 | Jul-26 2026 extent = **6.787**. OLS: `min = −0.390 + 0.685·jul26` → **pred 4.258, resid sd 0.340**. Market-implied normal (from the .58/.44 rungs) = **μ 4.22, σ 0.334** — *centre and width both match; the ladder is NOT naively wrong*. But the deep wings break away: T4.0 bid .35 vs model .22 (raw 19y frequency .105) · T3.8 bid .25 vs .09 · T3.6 bid .18 vs .03. **Sell-YES EV +12.6¢ / +16.1¢ / +15.3¢** — and those are exactly the rungs carrying the OI (200/210/115 vs 5 on the centre rungs) | **TRADE-shaped, capacity-capped** (~$300 of fish; 66-day capital lock to Oct-1) |
| **M6** | Burst-1 M6 revisited: DDR5/memory sticky-index wake-up (`KXDDR5WS`, `KXDDR5EWS`) | The gas-AR(1) mechanism transferred to a sticky supply-constrained spot index (Ornn), which M6 parked pending a live book | Read every rung of all 5 listed weeks | **240 markets listed out to Week 36. Every rung: yes_bid 0.00-0.01 / yes_ask 0.98-0.99, OI 0.00, volume 0.00.** Five consecutive weekly events, zero counterparty | **DEAD-with-numbers** — M6's wake-up gate has *not* fired; the Ornn family lists but nobody trades it. Stop re-proposing it until a rung shows OI |
| **M7** | The new "share of global X" econ family (`KXUSDRESERVE` IMF COFER, `KXUSDINTLPAY` SWIFT, `KXUSGDPSHARE` World Bank) | Three Economics series in 3 days, all settling on a **public, slow, low-variance official statistic** — BENTER factor-1 passes cleanly (unlike the Spice-Data chicken-sandwich kill) | Read the full ladders + close times + OI | `KXUSDRESERVE` closes **2026-09-29**, OI **0.00 on every rung**, spreads 5-8¢. `KXUSDINTLPAY` closes **2027-01-21**, OI **0.00 on every rung**, spreads 8¢. `KXUSGDPSHARE` closes **2027-07-01** (11-month lock), OI 6,418 but concentrated in 3 rungs; ladder is non-monotone in the middle (T25.9 .25/.26 below T26.0 .26/.28) | **CONDITIONAL(gate)** — killed *today* by capital lock + zero counterparty, not by mechanism. Gate: only worth modelling if a quarterly-cadence sibling lists (COFER is quarterly; a Q-by-Q series would be a genuine BUFFETT surface) |
| **M8** | `KXSENATEADJOURN` — trade the published calendar | New Politics series; the Senate publishes its 2026 calendar, so the recess date is public months ahead. Fish = whoever guesses instead of reading senate.gov | Fetched senate.gov 2026 schedule; compared to the ladder | Published: **"Aug 10 – Sep 11 State Work Period"** ⇒ scheduled adjournment Fri **Aug 7**. Market: Before Aug-8 = **.88/.89**, Before Aug-11 = .92/.93, Before Aug-15 = .98/.99. **The schedule is already in the price** — the 11¢ residual is pure slip risk (appropriations/nominations), not a diligence gap. OI 0.00 on all three rungs | **DEAD** — public-schedule edge fully arbitraged at listing; nothing left but a political forecast |

---

## 2. THE TOP DOOR, IN PLAIN ENGLISH (M3)

A congressional hearing is running. Someone lists 15 words. Retail buys "he'll say *tariffs*" at 60¢ because it
*feels* likely. Two hours before the gavel, across 226 such lifts, those tickets were worth 32¢. Kalshi closes
each word the instant it is spoken, so there is no free money *after* the fact — the money is in being the
person who sells the ticket while the crowd is still buying it.

**Why it survives the graveyard:** the resting-sell/pickoff kill (91-100% pickoff, 3 proofs) is *measured into
this number* — the 32.3% realized-YES rate IS the pickoff, and the seller still nets +22.2¢. And the population
measured is specifically **yes-taker lifts**, i.e. exactly the fills a resting offer receives. The mirror
population (no-taker hits, someone selling into our bid) pays only **+5.0¢ (n=265)** — so the edge is
**maker-side only**, which is precisely the side that now bills **$0.00**. Fee-quadratic-at-50¢ (1.75¢) would
have eaten a quarter of the JUL-15-shaped events; it no longer does.

**Named gates before any glory:**
1. **Maker-fee confirmation in prod** — the whole edge is priced off `is_taker=false ⇒ fee_cost=0`. First resting fill must self-confirm.
2. **Book depth, not tape depth.** The +22.2¢ is from the trade tape. Per-rung volume runs 600-50,965 contracts, but nobody has checked the resting size at the ask. Needs one hearing's book capture.
3. **`n=226` over 26 events, one series, ~8 weeks.** Monotone across 5 price buckets × 3 horizons and 22/26 events positive, so it is not one-print P&L (graveyard rule) — but it is one *family*, and the family is ~8 weeks old.
4. **Cadence risk:** 35 finalized events in ~8 weeks ≈ 4-5/week. If Kalshi de-lists or the crowd learns, it evaporates. Capture now (BEZOS logic): the tape retains ~10 weeks.
5. **The seed-prior transfer FAILS here — do not confuse the two.** Blind-NO at the first traded price is only **+2.7¢ (n=526, mean px 0.591 → realized 0.565)**. The verify-seed-prior +10.3¢ blind-NO does *not* generalize to hearing words. The edge is **strictly intra-hearing, strictly on the lift.**

Cheapest next step (one hearing, ~2h, no capital): the live `KXHEARINGMENTION-26JUL29` (Fauci, Senate Homeland
Security, 20 rungs, currently .04/.90 pre-hearing) — capture the book + tape through the hearing and check whether
resting-offer size at the ask exists at the prices the tape shows.

---

## 3. HANDED TO OTHER LANES (found by the census, not mine to close)

- **`KXINXDUD` / `KXNDQHUD` — S&P-500 *Daily* and NASDAQ-100 *Hourly* Up/Down, first markets created TODAY 14:55Z.**
  OI already 5,897 (INXDUD) / 1,007 (NDQHUD, hour 1). This is the crypto-15m engine's shape transplanted onto an
  index with an external sharp anchor. Settle source string is loose: *"For example, Google Finance"*. → RENTEC / HOUSE-FEE.
- **`KXTRUEV` — daily Truflation EV Commodity Index ladder, OI 9,341 on the front rung.** Real-time public index,
  15 rungs, closes 03:59Z next day. Free-feed access is the whole gate (`truflation.com` marketplace page is a
  JS SPA; no public REST found at the obvious path). **Useful trick regardless: Kalshi's own settled ladders
  reconstruct the index history for free at 10-point resolution** — Jul-23 settled in [1199.52, 1209.52];
  Jun-09/10/11/12 in [1230,1240]/[1235,1245]/[1235,1245]/[1239,1249] ⇒ drift ≈ −1/day, |3-day move| < 10. → INFO-CHANNEL.
- **`KXWTACHALLENGERMATCH`** cold-started Jul-25 with **OI 1,010,337** across 38 two-sided markets — by far the
  largest new-OI object of the window. → sports lanes.
- **`KXHOBBYTEMP`** (Houston Hobby max temp, new Jul-24) has **still not produced a live book in 72h** — the
  weather-family listing lag is ≫48h; the "first 48h" window does not exist for new weather stations.

---

## 4. MESH DELTAS (write these back)

- **MENTION/COUNT family, final form:** the ratchet is dead in *both* directions on Kalshi's hearing/earnings
  mention markets — YES rungs early-close at the spoken word (min lock-lead 1,973 s over 300 rungs), NO rungs
  close at gavel-down. The surviving edge in the spoken-count family is **not the lock, it is the path**:
  sell the in-hearing lift (+22.2¢, n=226, 22/26 events).
- **Seed-prior does not generalize across word families.** Earnings words: blind-NO +10.3¢ (n=492). Hearing
  words: blind-NO **+2.7¢** (n=526). Per-family confirmation is mandatory, as note 63 warned.
- **Census cadence subtraction is mandatory.** 93 of 95 apparent series wake-ups were the ordinary daily/weekly
  listing cadence. A wake-up monitor without cadence subtraction is 93% false positives.
- **`listing_monitor` died Jul-26 15:46** (Mac, not migrated to the VPS). 21h of listing events were only
  recoverable by a live `/series` diff.
- **Zero-fee and maker-fee series counts are unchanged in 72h** (13 / 130) — the fee-seam is static; stop re-checking weekly.
- **Arctic/NSIDC is a first-class free settle source** (`noaadata.apps.nsidc.org/.../N_seaice_extent_daily_v4.0.csv`,
  daily, 1978→, 200 OK with a browser UA; WebFetch 500s on it — curl with a UA works).
  Same for FlightAware `/live/cancelled/week` (403 to WebFetch, 200 to curl+UA).

---

*Files: analysis + pulls in the session scratchpad (`series_live.json`, `live_events.json`, `hm_all.json`,
`nsidc.csv`). Nothing written to `~/kalshi_data`; no nestor changes.*
