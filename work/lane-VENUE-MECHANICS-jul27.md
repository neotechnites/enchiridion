# Lane: VENUE-MECHANICS — 2026-07-27 ideation burst (lane #2)

Charter: `work/steer-ideation-jul27.md`. Lane assignment: the pre-T0 "initialized" window,
listing lag, and eventually-consistent indexes **as a class** — what is visible-to-the-diligent
before it's visible-to-the-lazy? Fine print: check who can ORDER during initialized (propose the
demo probe, don't assume; **no demo or prod orders placed by this lane**).

Spin-up: [[33 The Mesh]] read completely, [[34]] VENUE-MECHANICS brief, [[15]] traps + three-legal-
verdicts, [[20]] discipline, graveyard §33 + prior `lane-VENUE-MECHANICS-burst1.md`.
All probes = **public read-only GETs** on `api.elections.kalshi.com`, no auth, no orders.

---

## HEADLINE (read this if nothing else)

The pre-T0 / listing-lag class is **real as mechanics and dead as an edge, for one reason that
nothing in the vault had measured: there is no counterparty in the window.**

- The initialized population is **not a private channel** — one documented query parameter
  (`status=unopened`) returns all **77,263** of them.
- A brand-new listing sits with a **completely empty book for 31.6s** after going active, then
  the venue seeds **0.99/0.01 × 896 both sides**. On the 17 wide-seed markets we have capture
  for, **16/17 ended with open interest 0** after 7.7–45.6 hours.
- In the *only* new-listing family with real flow (MENTIONS), **volume in the first 30 minutes
  was 0 contracts on 11/11 markets; median first trade was T0+107.9 min.**
- The one place the lag *does* bite is 15m crypto, and there the mechanism is now nailed:
  `/markets?status=open` is a **15.00-second cache grid**, per-series phase-locked
  (**BTC +6.17s ± 0.16, ETH +2.68s ± 0.52**, 16/16 on-grid). The list can *never* reach the
  T0+4.8s dip; direct-ticker can.

**LIVE DEFECT FOUND, with the fix (note 15: report + fix in the same message, with numbers).**
`crates/streak/src/strategy.rs:625` discovers the current 15m window with
`eng.kalshi.markets(series, "open")` — i.e. `GET /markets?series_ticker=…&status=open`, **the
cached index**. Nestor's own comment at strategy.rs:652-657 already measured the symptom:

> *"Measured over n=518 windows on 3 days of `data/obs/`, the FIRST observation of a new market
> lands at a **MEDIAN T0+25s** — and the 40¢ rest is fitted on a dip that bottoms at a median
> T0+4.8s. **13.3% of windows first observe after T0+40** (forced `taker_late`, no maker leg at
> all) and **4.2% after T0+60 (window missed outright)**."*

**This lane supplies the mechanism, and it matches their number.** The lag is not jitter and cannot
be fixed by polling faster (nestor already polls ~1s): the index is rebuilt on a **15.00s clock**,
per-series phase-locked, and market inclusion costs 1–2 cycles → median **T0+21.2s (BTC) / T0+31.9s
(ETH)**, pooled ≈ 26.5s vs their measured 25s. The websocket does not rescue it — strategy.rs:479
only registers WS interest *after* REST discovery, so WS is downstream of the cached list.

**Fix:** replace list-discovery in streak with a constructed-ticker direct GET. `Kalshi::market()`
already exists (`crates/engine/src/kalshi.rs:388`, public, uncached). The 15m ticker is fully
deterministic from the close time in ET — `{SERIES}-{%y%b%d upper}{%H%M}-{%M}` — probe-proven 6/6 in
`probe_direct_ticker.py`. Direct GET returns the market at **T0−10s** (`status:"initialized"`) and
first priced at median **T0+5.4s (BTC) / T0+9.6s (ETH)**, recovering **15.8s / 22.3s** and landing
*at* the T0+4.8s dip instead of ~21–32s after it. This is the "queued code" in [[40]] §2 — the
numbers here say it is not cosmetic, it is the difference between having a maker leg and not.
Kalshi.rs:355-360 already learned exactly this lesson for the **post-close** side (`recent_closed` is
deliberately status-agnostic because "the `status=settled` filter lags the actual result"); the same
fix was never applied to the **pre-open** side.

**Top door (V2): does the matching engine accept a resting order during `initialized`?**
`verify-streak-execution.md` §4.2 prescribes posting the 40¢ maker leg at **T0−5 to −10s**, which is
inside the initialized window; [[40]] records nestor actually resting **at T0**. Nobody has tested
whether the prescribed timing is legal. 2 demo contracts at 1¢ settles it, and either answer is
load-bearing for Monday's streak session. Spec in §V2 below. **This lane placed no orders.**

**Actionable spin-off for another lane:** the seed-prior edge (verify-seed-prior: n=492 word
markets seeded 0.54/0.46, realize 0.417, blind-NO +10.3¢ net / confident-subset +18.9¢ net)
**cannot be captured at T0**. Its entry window is **T0+60 to T0+120 min** — that is when the first
taker arrives, and by then the seed is already gone (median traded price 0.410 vs the 0.54 seed).

---

## Ledger

| # | idea | mechanism / fish | cheapest decisive kill | numbers | verdict |
|---|------|------------------|------------------------|---------|---------|
| V1 | The pre-T0 "initialized" window is a private channel the lazy can't see | Fish: competitors polling only `status=open` | One GET: does the API expose the initialized population? | `status=unopened` → **77,263 markets, all status `initialized`, 60 distinct series**. Median **16.11h** to open, p10 3.10h, p90 32.1h, max **3,023h (126 d)**. `status=initialized` itself 400s; `unopened` is the documented alias. **0 / 77,263 priced.** Indexes fully consistent: `/orderbook` → `{"yes_dollars":[], "no_dollars":[]}`, `/trades` → `[]`, `/candlesticks` → `[]`, `/events?with_nested_markets` → present, direct `/markets/{t}` → present. | **DEAD as asymmetry** (one documented param reveals all of it). Survives only as a **scheduler** — see V7. |
| **V2** | **Resting orders accepted during `initialized` → own queue position at T0, at $0 maker fee.** *This is the lane's top door.* | Fish: everyone who can only queue after T0 — including our own current implementation | **DEMO PROBE — proposed, not run** (spec below) | **This is not hypothetical: `verify-streak-execution.md` §4.2 prescribes "T0−5 to −10s … post a resting GTD limit BUY, full size (8-12), at L=40c", and its own caveat says the 24% fill rate "ignores queue position … fills at exactly L with size ahead may not occur → 24% is a mild over-estimate." [[40]] says nestor actually rests "10x@40¢ GTD **at T0**." So the live system is executing the policy 5–10s late, and nobody has tested whether the prescribed timing is even legal.** Payoff scale from their table: rest@40 alone 2.45¢ → full policy 3.55¢/contract (prev1), 4.72¢ (4-streak); the dip bottoms at **T0+4.8s** and P(min≤40¢)=24%. Queue-front at T0 turns that over-estimate into a floor. Read-only priors against a 2xx: orderbook returns 200 with zero levels pre-T0, and venue seed liquidity doesn't appear until T0+34.6s (new series) / T0+1.4–10.0s (15m crypto). | **CONDITIONAL(gate: demo POST on an `initialized` market returns 2xx AND the order is in the book at T0 with priority)** — cheapest high-value test on the board: 2 demo contracts at 1¢. |
| V3 | The unopened index gives a head start on brand-new series → build a prior, be first | Fish: retail arriving after the listing is visible on the site | Catch two new series live and measure created→open; compare to what `listing_monitor` already achieves via `/series` | Caught live today: **KXAKSEN1** created 15:39:16Z, open 16:00:00Z = **20.7 min lead**; **KXWIDGOV2ND** created 15:47:18Z, open 16:10:00Z = **22.7 min lead**. But `/series` leads *further*: on the 8 `NEW_SERIES` events in `listing_events.jsonl`, listing_monitor's detection was **−6.8 to −393 min relative to the first market's `created_time`** and **−0.1 to −1,182 min relative to open** (KXUSDRESERVE −1,182, KXUSGDPSHARE −1,154, KXUSDINTLPAY −1,041). Only 2 of 60 unopened series were absent from the Jul-26 12,187-series baseline. | **DEAD-as-new (redundant)**. `/series` already leads market creation. Marginal add: unopened supplies the **exact open_time countdown**, which `/series` does not. Fold into listing_monitor as a countdown field, not a new strategy. |
| V4 | Uniform seed on a fresh mutually-exclusive ladder → Σbid > 1 → sell-all dutchbook | Fish: the venue's own seeding bot | Read the seed vector of every new ladder in `listing_books.jsonl` + one live open | **Live KXAKSEN1 (4 candidates), seed at T0+34.6s: 0.99 ask / 0.01 bid, size 896.00 on BOTH sides, identical on all 4 rungs. Σyes_ask = 3.96, Σyes_bid = 0.04.** Historical seeds (53 first-seen books): KXRPRESPRIMARY Σya 2.09 / Σyb 0.84; KXDPRESPRIMARY Σya 2.04 / Σyb 0.65; KXUSDRESERVE & KXUSDINTLPAY Σya 6.93 / Σyb 0.07 on 7 rungs each. **Σyes_bid never exceeds 0.84.** | **DEAD (structural)**. Reconfirms BUFFETT B9 in the new-listing case. |
| V4b | *(Mesh correction, not an idea)* "Kalshi seeds new series at uniform 0.54/0.46" | — | same data | The uniform 0.54/0.46 seed is **MENTIONS-family-specific**: 13/13 KXEARNINGSMENTIONGRAB at exactly 0.54/0.46. Election ladders are **prior-weighted**: KXRPRESPRIMARY JVAN 0.42/0.39, MRUB 0.33/0.30, tail 0.05/0.02; KXDPRESPRIMARY KHAR 0.19/0.16, WMOO/REMA/JSHA/JOSS/GNEW 0.11/0.08, tail 0.02/0.00. Threshold ladders get the degenerate 0.99/0.01. Overall seed ask histogram (n=53): 0.99×17, 0.54×13, 0.05×9, 0.11×5. Seed books are **static**: KXRPRESPRIMARY tail rungs unchanged 0.05/0.02 → 0.05/0.02 over **2,736 min (45.6h)**. | **Mesh edit required** — see §Mesh delta. |
| V5 | Be the first real quote inside the 98¢-wide fresh seed book (maker fee $0, 31.6s of empty book) | Fish: retail who market-buys a fresh listing and pays 0.99 | Do wide-seed markets accrue ANY open interest? | 17 of 53 first-seen books had spread > 50¢. **End OI = 0 on 16/17** (median capture span 461 min, max 2,736 min = 45.6h). Sole exception KXACQANNOUNCEOPENR (endOI 200) narrowed to ≤10¢ within **20 min**. 10/17 narrowed to ≤10¢ (median 441 min); 7/17 still >10¢ at end of capture. | **DEAD on flow** — a no-counterparty kill, not a pickoff kill. The graveyard's pickoff objection never even gets to apply. |
| V6 | Be first at T0 on MENTIONS — the one new-listing family with both flow and a mispriced uniform seed | Fish: verify-seed-prior's n=492 word markets (seeded 0.54/0.46, realize **0.417**) | When does the first taker actually arrive, and at what price? | MENTIONS carries **7,252 of 8,065 contracts (90%)** across all 53 tracked new-listing markets; 13/13 traded vs 9/40 elsewhere (813 contracts in 66h). **But: volume in the first 30 min = 0 contracts on 11/11 markets. Median first trade T0+107.9 min (min 63.6, max 599.3).** By first trade the seed is gone: first-trade prices 0.19/0.19/0.24/0.24/0.29/0.34/0.37/0.39/0.56/0.59/0.81, taker=yes on 9/11; **median traded price 0.410 vs the 0.54 seed**; ~49.6% of lifetime volume inside the first 2h. | **DEAD for the pre-T0/speed premise.** The seed-prior edge itself is untouched — but its **entry window is T0+60–120 min**, and it is a resting-quote play, not a speed play. Hand to the seed-prior owner. |
| **V7** | 15m-crypto list-index lag is a **deterministic 15s cache grid**, so the list can never reach the T0 entry — direct-ticker must be the sole path. **The fish turned out to be us** (streak/strategy.rs:625). | Fish: any bot whose T0 entry path is `GET /markets?status=open` — including nestor today | `lag mod 15` on the n=16 first-open observations already on disk | **lag mod 15 = 6.17s ± 0.163 (BTC, n=8, range 5.84–6.46); 2.68s ± 0.524 (ETH, n=8, range 1.74–3.36). 16/16 on-grid. Pooled phase sd 1.79s vs 4.33s expected if lag were uniform-random.** Cycles missed: BTC {0:1, 1:4, 2:3}, ETH {0:1, 1:2, 2:5}. Median list lag **21.16s BTC / 31.93s ETH**. Direct-ticker first-priced: BTC {1.43, 5.40, 6.94} med **5.40s**, ETH {7.59, 9.61, 10.01} med **9.61s** → recovered window **15.8s (BTC) / 22.3s (ETH)**. Price carried by the recovered window (n=6 paired, Jul-26): Δ = +4, −1, −4 (BTC), −5, 0, 0 (ETH) → **mean \|Δ\| = 2.33¢, mean Δ = −1.0¢**. | **TRADE-shaped (execution)** — already queued in [[40]] §2. Lane contribution: the **mechanism + phase constants**, and the hard conclusion that `status=open` is structurally incapable of the T0+4.8s dip (verify-streak-execution). Direction of the 2.33¢ unresolved at n=6. |
| V8 | Sub-cent (deci-cent) tick levels = free queue priority for 1/10 of a cent, at $0 maker fee | Fish: anyone quoting on the whole-cent grid | Pull depth-100 books on the 15m families: are the sub-cent levels already occupied? | New venue fact: 15m crypto is **`tapered_deci_cent`** — steps 0.0010 on [0, 0.10], **0.0100 on [0.10, 0.90]**, 0.0010 on [0.90, 1.00]. All 9 15M families (BTC/ETH/SOL/XRP/DOGE/BNB/HYPE/NEAR/ZEC). Everything else we trade is `linear_cent`: KXBTCD, KXETHD, KXBTC, KXETH, KXGOLDD, KXSILVERD, KXBRENTD, KXNATGASD, KXINXU, KXNASDAQ100U, KXHIGHNY, KXCPIYOY. **Occupancy: in the tail zones, 234 sub-cent levels already resting vs 111 cent levels**, sizes 100–600 (e.g. KXBTC15M no-side 0.9010×109, 0.9030×600, 0.9050×500, 0.9070×500, 0.9090×500). | **DEAD.** MMs already own the deci-cent grid. Worse: the *tapered* structure means there is **no sub-cent tick anywhere in 10–90¢** — exactly where nestor rests (40¢) and IOCs (46¢) — so no queue-jump lever exists on the live strategy at all. |

---

## V2 — the demo probe, specified (for whoever runs it; this lane placed no orders)

Objective: settle whether the matching engine accepts orders on a market in `status=initialized`.

1. Pick a **demo** market ~12h from open, e.g. the furthest-out `KXBTC15M-*` from
   `GET /markets?series_ticker=KXBTC15M&status=unopened&limit=100`. Confirm `status=="initialized"`.
2. `POST /portfolio/orders`: `action=buy`, `side=yes`, `type=limit`, `yes_price=1` (1¢),
   `count=1`, fresh `client_order_id`. **Record the HTTP status and the full error body verbatim.**
3. If 2xx: `GET /portfolio/orders?status=resting` — does it exist? Then
   `GET /markets/{t}/orderbook` — does the level appear pre-T0, or only at T0?
4. Repeat once on a market **in the active-but-empty window** (T0+3s … T0+34s on a new series;
   T0+1s … T0+6s on 15m crypto). This one is expected to succeed and is the control.
5. Cancel everything; confirm `reduced_by` (synchronous truth, [[40]]).

Total exposure: 2 demo contracts at 1¢. Expected result: **400 / "market not open"**.

**Why this one matters (unlike the rest of the lane).** V5/V6 show the *new-listing* window has no
counterparty, so pre-T0 access is worthless there. But 15m crypto is the opposite case — flow is
instant, and `verify-streak-execution` §4.2 already prescribes resting at **T0−5 to −10s**, which is
*inside the initialized window*. [[40]] records the live implementation as resting **at T0**. Either:

- the POST **succeeds** pre-T0 → nestor should move its maker leg to T0−10s and gets queue-front on
  the 40¢ bid when the T0+4.8s dip arrives (their 24% fill rate is explicitly flagged as a
  queue-position over-estimate; queue-front makes it a floor), **or**
- the POST **fails** → `verify-streak-execution`'s prescribed timing is unimplementable and the
  ledger's step 2 should be amended to "T0+0 via direct-ticker poll" — which also settles why the
  live system diverges from the prescription.

Both outcomes are worth having, and both are 2 demo contracts away. **Run this before the Monday
streak session.**

**Second blocker, found in our own code (independent of the venue answer).** Even if the exchange
accepts pre-T0 orders, nestor cannot currently use them:

- `crates/streak/src/lib.rs:8` — *"rest a full-size 40¢ bid on the reversal side **from T0**"* —
  the implementation encodes T0, not the prescribed T0−5..−10s.
- `crates/streak/src/signal.rs:90-92,140-144` — the entry gate is
  `ttc = close_unix - now; if !(MIN_TTC_SECS..=WINDOW_SECS).contains(&ttc) { return Err(Skip::NotEntryWindow) }`
  with `WINDOW_SECS = 900`, `MIN_TTC_SECS = 840`. **At T0, ttc = 900; at T0−10s, ttc = 910 > 900 →
  `NotEntryWindow`.** And `Skip::retryable()` (signal.rs:73-75) returns true **only** for
  `PrevNotSettled`, so `NotEntryWindow` is **terminal** — a market first seen pre-T0 is rejected and
  never re-evaluated.

So moving the maker leg pre-T0 needs the demo answer **and** a two-line change to the upper bound of
that guard (plus making `NotEntryWindow` retryable when `ttc > WINDOW_SECS`, since that case is
"too early", not "too late" — they are currently conflated into one terminal skip).

---

## What died, and why it matters that it died this way

Three separate ideas (V4, V5, V6) all failed on the **same** hidden fact, which no prior ledger
had: **new listings have no flow for the first hour-plus.** The vault's "new listings are sloppy
for ~48h" (Mesh §STRUCTURE) is true about *quotes* and false about *volume*. Being early is only
worth something where somebody is trading, and on Kalshi that is essentially 15m crypto and the
established slate — never a fresh book.

That collapses the whole "visible-to-the-diligent" premise for this lane down to one survivor
(V7, 15m crypto, already queued), and it does so with numbers rather than vibes:
**0 contracts in 30 min, 11/11 markets; OI 0 after 45.6h, 16/17 markets.**

---

## Mesh delta (to add to [[33]])

1. **`GET /markets?status=unopened` returns the entire pre-open population** (77,263 markets,
   60 series, all `status:"initialized"`, median 16.1h to open, max 126 d, none priced).
   `status=initialized` 400s. The pre-T0 window is public, not privileged.
2. **`/markets?status=open` is a 15.00-second cache grid**, per-series phase-locked:
   BTC15M +6.17s ± 0.16, ETH15M +2.68s ± 0.52 (n=16, 16/16 on-grid). Observed list lag =
   phase + 15·k, k ∈ {0,1,2}; median 21.2s / 31.9s. **The list index can never serve a T0 entry.**
   Direct-ticker fetch is uncached: first priced at median 5.4s (BTC) / 9.6s (ETH) after T0.
3. **New listings have no flow.** Wide-seed (>50¢) markets: OI 0 on 16/17 after 7.7–45.6h.
   Even in the one family with volume (MENTIONS, 90% of all new-listing volume), **first-30-min
   volume = 0 on 11/11, median first trade T0+107.9 min**. Correct the "new listings are sloppy
   for ~48h → a listing monitor is a strategy-generator" line: sloppy quotes, no counterparty.
4. **Seed structure is family-specific, not uniform.** MENTIONS: 0.54/0.46 on 13/13. Elections:
   prior-weighted (0.42/0.33/…/0.05). Thresholds & fresh which-candidate ladders: degenerate
   **0.99/0.01 with size 896 both sides**. Σyes_bid across a fresh exclusive ladder is 0.04–0.84,
   **never > 1** → no sell-all dutchbook (BUFFETT B9 again).
5. **New series are created ~20–23 min before open** (KXAKSEN1 20.7, KXWIDGOV2ND 22.7), go
   `active` at T0+3.0s, and the book stays **completely empty for 31.6s** before the venue seeds.
   `/series` leads market creation by a further 7–393 min — listing_monitor's existing poll is
   the better sensor.
6. **`price_level_structure` / `price_ranges` are per-family.** 15M crypto = `tapered_deci_cent`
   (0.1¢ ticks on [0,10¢] and [90¢,100¢]; **1¢ ticks on [10¢,90¢]**); dailies/hourlies/index/econ
   = `linear_cent`. The deci-cent tails are already densely quoted (234 sub-cent vs 111 cent
   levels, sizes 100–600) — no free queue priority, and none available at all in 10–90¢.
7. Market records also carry `settlement_timer_seconds` (1 for 15m crypto), `can_close_early`,
   and `no_sub_title:"Target price: TBD"` pre-T0 — **the 15m strike is not set until T0**, so
   nothing about the contract's payoff is knowable early. `rules_primary` *is* fully written
   pre-open.

---

## Files

- `/Users/ryanwhitehead/kalshi_data/scripts/vm_unopened_census.py` → `vm_unopened_census.json` (77,263 recs)
- `/Users/ryanwhitehead/kalshi_data/scripts/vm_newseries_open_watch.py` → `vm_newseries_open.jsonl` (KXAKSEN1/KXWIDGOV2ND crossing T0 live)
- `/Users/ryanwhitehead/kalshi_data/scripts/vm_t0_emptybook.py` → `vm_t0_emptybook.jsonl` (15m crypto T0 empty-book / first-trade)
- Reused: `listing_books.jsonl` (5,204 rows, 53 tickers), `listing_events.jsonl`, `listing_latency.jsonl`, `direct_ticker_probe.jsonl`

## Capture demand

- Add `status=unopened` polling to `listing_monitor.py` **only** as an open_time countdown for
  MENTIONS-family listings, and schedule the snapshot cadence around **T0+45 min → T0+150 min**
  (where the flow actually is), not the first 2h from T0.
- `vm_t0_emptybook.py` is re-runnable; more boundaries would firm the n=6 recovered-window Δ.
