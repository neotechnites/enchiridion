# 08 - Broad-Kalshi & Cross-Venue Hunt (2026-06-25)

> ⚠️ **Historical (goal framing outdated; weather status changed).** Goal is now STAGED, not flat 10%/day (note 15). The weather work below matured into an UNVERDICTED watchlist item — backtest +8.2¢ but forward capture (`ens_forward_capture.py`) only began Jul 2026; ~3-4 wks to verdict; do NOT build/trade it yet ([[18 - LIVE STATE (2026-07-23)]]).

New mandate: stop grinding KXBTC15M. Treat ALL of Kalshi as inefficient retail gold; find edges in OTHER markets or relative (cross-venue) arb. Target 10%/day, ~"1% on 10 near-guaranteed trades/day."

## Kalshi universe (mapped, `scripts/events_catalog.py` → `events_catalog.json`)
- 6,996 open events, 17 categories. **Sports = 176M of 24h vol (95%+ of all activity)**, then Elections, Crypto, Entertainment, Politics, Weather, Financials, Econ.
- ~2,700 mutually-exclusive (ME) events (Sports 1309, Elections 1111…) = the natural home for internal "guaranteed" arb.
- Beware `KXMVE…` multivariate parlay combos: 167k auto-generated 0-volume markets that swamp a naive `/markets` pull. Drive off `/events` instead.

## TESTED & DEAD this session
1. **Internal ME static arb** (`me_arb.py`): scan all ME events for Σ(yes_ask)<1 (buy-all-YES) or Σ(yes_bid)>1 (buy-all-NO). 43 "candidates" — ALL fake: the big-profit ones are **non-exhaustive longshot lists** (next Pope = 7 of ~250 cardinals; FL primaries; ME only means ≤1 wins, NOT that one must → buying all named YES can lose everything). The lone "real" one (CS2 map buy-all-NO +4¢) was a **0.05-contract stale bid**. **No internal arb at tradeable size** — MMs police it (same as crypto buckets).
2. **Cross-venue snapshot arb, Kalshi↔Polymarket World Cup** (`xvenue_wc.py`): 9 matched live games. Every 3-way Dutch book (cheapest ask per outcome across venues) = **1.004–1.020 ≥ $1, no lock.** Mid-price gaps only ±0.5–2¢ = inside Kalshi's 1¢ spread. **Marquee events are efficient cross-venue** (high attention + arbZ). Useful facts: **Polymarket is the sharp/deep venue** (0.1¢ spread, ~$1M liquidity); **Kalshi runs ~1¢ richer**.
3. **Favorite-longshot / "99-to-1 near-guaranteed" on liquid sports** (`favcal_pool.py`, `analyze_fav2.py`): price at close-2h vs result, MLB(875)+ITF-M(1469)+ITF-W(1477) games.
   - MLB favorites **calibrated→slightly OVERpriced** (70-80¢ win only 64.8%), net-NEGATIVE after fee in nearly every bucket; early/late halves **flip sign** (80-88¢: +4¢ early, −10¢ late). Dead — same instability trap as crypto favorites.
   - 15-30¢ underdog "underpricing" looked great pooled (+2.75¢/trade, both halves +) **but was 100% men's-ITF noise**: +5.87¢ ITF-M, −0.88¢ ITF-W, −0.93¢ MLB. **Does NOT replicate cross-sport** = the multiple-comparisons mirage ([[07 - Overfitting & Validation Discipline]]).
   - Only cross-sport-consistent cell: 88-94¢ favorites overpriced (−7.7/−12.4/−6.0¢) but fails adjacent-band robustness (80-88, 94-99 vary). Not tradeable.
   - **Verdict: Kalshi liquid sports are efficient.** Static calibration betting is net-negative after fees.

## REAL & UNKILLED — the one live edge: intraday cross-venue divergence
`scripts/xpath.py` — completed MLB game (Athletics@Giants 06-24), Kalshi 1-min snapshots vs Polymarket `prices-history` (1s). **During the live game the two venues diverge by 5–40¢** (std 6¢, 18/165 min with |gap|≥4¢; late-game e.g. K=99 vs P=51.5 when Kalshi priced in the decisive play first). Snapshot was flat ±1.5¢ — **the inefficiency is purely in the TIME dimension, not the snapshot.**
- This is the genuine "relative arb": **trade the lagging venue toward the leader.** Late-game gaps driven by a near-resolved game state are the closest thing to Ryan's "near-guaranteed."
- **Cannot be backtested at quote level**: Kalshi retains quotes/candles only ~1–2 days (trades ~2mo). Lead-lag from grid+forward-fill is an artifact (less-traded venue always looks laggy). → **must be validated LIVE** (poll both order books, fade divergences).
- **Key gotcha found:** Kalshi sports ticker times are **ET, not UTC** (e.g. `…2145…` = 21:45 ET = 01:45 UTC). Game window for path pulls must convert ET→UTC or you sample the flat pre-game line.

## TESTED & DEAD (cont.) — cross-venue BTC hourly (`scripts/xbtc.py`, `xbtc_rows.json`)
Kalshi `KXBTCD` vs Polymarket "bitcoin-above-…-Xpm-et", matched by settlement hour, 93 near-money points (~16 hourly settlements, Jun 24). At lead=settle−20min, compare Kalshi price vs Poly (interpolated to Kalshi's strike), test fading Kalshi→Poly to settlement.
- **Kalshi BTC-hourly is cleanly CALIBRATED** (price-decile → winrate: 0-10c→6%, 60-70c→70%, 80-90c→80%, 90-100c→91%) and is the BETTER predictor (MAE 0.285 vs Poly 0.393).
- Fading Kalshi toward Poly **LOSES −12¢/trade (19% win).** The +9.6¢ mean "gap" is a **systematic artifact** of linear-interpolating Poly's coarse $2,000 strike ladder across a steep CDF, not real mispricing.
- **Verdict: Kalshi BTC hourly is efficient too.** (Gotcha: settled Poly questions read "above 59,200" with NO `$`; Kalshi `KXBTCD-DDHH` HH is the EDT hour, close=HH+4 UTC; Poly only keeps ~recent hourly slugs.)

## OVERALL VERDICT THIS SESSION
Tested 4 independent static/snapshot edge classes across liquid Kalshi (internal arb, cross-venue snapshot arb, sports favorite-longshot, cross-venue BTC hourly). **ALL efficient / mirages.** Same verdict as the crypto 15m work: **Kalshi's liquid markets are well-calibrated; static edges don't survive.** The ONLY real divergence is **intraday/live** (sports paths, 5-40¢ gaps) — a latency edge that can't be backtested (no historical Kalshi quotes) and must be captured by a LIVE bot. That is the one remaining lane.

## WEATHER — the real retail lane (tested 2026-06-25, `scripts/weather_*.py`)
Kalshi daily high-temp markets (KXHIGHNY=Central Park/KNYC, +MIA,CHI,ATL,BOS,DEN…): mutually-exclusive 2°F buckets, settle on NWS daily climate report. Pure retail; free NWS obs+forecasts.
- **Slow intraday convergence = the inefficiency (quantified, 40 day-cities):** the eventually-winning bucket's avg traded price is **45¢ @11am EDT → 54¢ @1pm → 67¢ @3pm → 90¢ @5pm.** Market is slow to price the obvious as obs come in.
- **Ratchet is RISKLESS but small:** buckets fully below an already-observed temp are guaranteed NO. 67 such buckets across 42 day-cities, **0 ever resolved YES** (1.5°F safety). But avg only ~5¢, and the eye-popping 30-80¢ "dead" prices are **stale trade-prints with no live bid** (today's dead buckets show yb=0.00 → not executable).
- **Pre-day, market ≈ NWS forecast** (live check Jun26: ATL/DEN/CHI modal bucket matched NWS; MIA market priced hotter than NWS point-forecast and is probably right via climatology). So the edge is NOT "NWS forecast vs naive market" — the price already embeds forecasts.
- **Where the edge actually is:** intraday **nowcasting** — beat retail's slow convergence in the ~12-4pm window using **1-minute ASOS data** (hourly METAR understates the true daily max by ~3°F — the market saw 83-84° while my hourly feed saw 81°) + a short-range model (HRRR/NBM). Scalable across ~10 cities × (high/low/rain) markets daily. Real, buildable, but needs the data pipeline — not free money.

## ✅ WEATHER FORECAST-BUY — first BACKTESTED POSITIVE edge (2026-06-25, `weather_fc.py`)
Strategy: bias-corrected **Open-Meteo** historical-forecast daily-max → predicted 2°F bucket → buy at Kalshi price at **9am EDT (13:00 UTC)**, hold to settle. Real Kalshi entry prices, fees in, leave-one-out bias (no same-day lookahead).
- **487 trades / 70 days / 8 cities: +6.17¢/trade, 45% hit, avg entry 37¢ (~+17%/trade).**
- Replicates in **6 of 8 cities**: MIA +18.1¢(62%), BOS +14.8¢(56%), ATL +8.4¢, NY +7.9¢(55%), PHX +4.9¢, CHI +3.8¢. Negative only DEN −4.8¢ & SEA −7.1¢ — the two cities with bad forecasts (corrected MAE ~3.9-4.0°F vs ~1.5°F elsewhere). **Safety filter: trade only cities with forecast-MAE < ~2°F.**
- **The data sources ARE the edge** (without them it's breakeven):
  - **Open-Meteo** `historical-forecast-api` — free, no key, archives the as-issued forecast (no lookahead), gives `temperature_2m_max`. Runs ~+1.5°F hot vs station; bias-correct per-city from history.
  - **IEM `daily.py max_temp_f`** — settlement-grade official daily max, multi-year, validated == Kalshi result. Used for bias calibration + outcome truth + the safety-margin study.
  - **IEM hourly METAR underestimates true daily max** (median +1°F, p90 +2°F, max +6°F) → why the pure observed-ratchet/floor-buy on hourly data is only breakeven; the model forecast beats it.
- Open caveats before sizing: confirm Open-Meteo archive uses the morning-of (00Z) run not a later-updated one (defensible at 9am entry, lock down live); 70-day window = one season, bias may be regime-dependent → forward-test; entry-price uses last-trade (executability vs live ask TBD).

## WEATHER — four sharpening tests (2026-06-25) — 3 of 4 negative; they pin the mechanism
Edge per-city ∝ forecast accuracy: **corr(forecast-MAE, EV) = −0.934** → city filter (trade MAE<~2.7°F, drop DEN/SEA) is a-priori mechanism, NOT P&L cherry-picking. Tests:
1. **Polymarket independent validation — DEAD.** PM runs only sporadic one-off London/Toronto temp markets, no daily series. Can't validate; forward paper-trade is the only independent test.
2. **No-forecast favorite-underpricing — NEGATIVE.** `fav_underprice.py`: buying the market's modal bucket nets −1.37¢; full calibration is clean (50-60c→57%, 60-70c→66%, 90-100c→95%; 70-80c slightly OVER). **Market buckets are well-calibrated → no standalone bias.** The earlier "82%/+13.8¢" was forecast-confirmed EARLY entries, not a generic favorite edge → **the edge genuinely needs the forecast.**
3. **Intraday nowcast — NO IMPROVEMENT.** `weather_nowcast.py`: at 3pm-ET lead, forecast/obs-floor/nowcast all ≈ breakeven (−0.2/+0.1/+0.1¢). By mid-afternoon the market has converged; observed temp adds nothing. **The edge is the 9am early-entry forecast, period.**
4. **Weather-condition gate — ONE GOOD FILTER.** `weather_cond.py`: **dry days +8.04¢ vs wet (forecast-precip) days +0.10¢** → skip precip days (rain makes highs unpredictable). Cloud mild (clear +6.97 vs +5.38); wind no effect; condition→forecast-error corr weak.
**Refined model:** early 9am entry · bias-corrected ENSEMBLE forecast · city-MAE filter · skip-precip filter · don't fade the market (contrarian-cheap buckets lose, 23% hit). Confidence filter (low model-disagreement + bucket-centered) ~+10¢ but built on noisier components.

## WEATHER — 2-YEAR reliability map (`multiyear_mae.py`, 2024-25, 731 days/city) — validates the city filter
Prediction side uses years (Open-Meteo archive + IEM); only Kalshi *entry prices* are capped (~2mo trade retention). Forecast corrected-MAE by city (annual), ranked:
MIA 1.03 · ATL 1.32 · NY 1.54 · BOS 1.65 · PHX 1.71 · CHI 1.85 · **DEN 2.65 · SEA 2.51 (untradeable).**
- **The city ranking is STABLE over 2 years and matches the 70-day backtest ordering** → city filter is real, not a one-season fluke. DEN/SEA bad in every season (mountains/marine).
- **Seasonality is CITY-SPECIFIC (answers summer-vs-winter): no universal pattern.** NY best in fall (MAE 1.09, ~60%) worst summer; ATL best in summer (1.04); MIA good year-round; DEN/SEA bad always, worst in spring/summer. → city filter should be **season-aware** (trade a city only in its low-MAE seasons).
- Why entry price is ~37¢: 2°F buckets are narrow vs ~1.5°F forecast error, so no single bucket is a favorite at 9am — we buy a ~45%-likely bucket for 37¢ (thin statistical edge, not a near-lock).
- **Remaining gap:** the PRICING edge (bucket underpriced vs prob) rests on ~2mo of Kalshi prices — extendable only FORWARD, or sideways by adding more cities/low-temp/rain markets in the live window.

## WEATHER — is it real? staleness check + mechanism + better data (2026-06-25)
- **Entry prices are FRESH, not stale** (the main artifact worry): median age of last-trade-before-9am = ~6 min (mean 0.2–0.7h; only ~4% >6h) across NY/MIA/ATL. Live spread ~1¢. So +6.17¢ is NOT a stale-print mirage; survives ~1¢ execution haircut → ~+5¢.
- **Mechanism (NOT a better model):** Open-Meteo just serves the same ECMWF/GFS the MMs already have. The edge = three stacked pieces: (1) **per-station bias-correction** (raw Open-Meteo ~+1.5°F hot; correcting it is what beats the market — NY MAE 2.07→1.52), (2) **city+season selection** (only forecastable cities/seasons), (3) **early 9am entry** before the market converges (by 1pm it's calibrated → buying the market's own modal then LOSES). Likely amplified by multi-bucket dispersion bias in thin retail flow.
- **Why NOT free money:** gap is small (45%-likely bucket at 37¢), MMs have the models so it can't get big, and **liquidity caps size** to hundreds–low-thousands $/market. Real capacity-limited niche; survives because it's too small/illiquid to bother arbing. Pricing side still only ~2-mo-validated.
- **More accurate FREE sources to upgrade it:** NWS **NBM / MOS / LAMP** (official, already station-bias-corrected — likely sharper than Open-Meteo+crude bias); **full ensembles (GEFS/ECMWF-ENS)** to bet the whole per-bucket probability distribution vs the market (the right tool for a 6-bucket market, not a single point forecast); HRRR for intraday. Next upgrade = ensemble-distribution betting, not a fancier point forecast.

## OTHER LANES — scoped (2026-06-25)
- **Gas (KXAAAGASD/W/M):** settles on **AAA national avg regular** (daily). Cumulative "Above $X" ladder. Mechanism: AAA avg is slow/trending (ran 4.55 May→3.90 Jun ≈ −2¢/day downtrend); a trend-aware predictor should beat a market slow to re-price the drift. **Blocker:** need a clean **AAA daily historical feed** for a rigorous backtest — reconstructing AAA from Kalshi YES/NO crossovers is too noisy (threshold granularity varies). Source for live/forward: gasprices.aaa.com fuel-gauge (today/yesterday/week/month). Same forecast-buy playbook as weather once the feed exists.
- **Mentions (KXTRUMPSAY, KXWCMENTION, KXLASTWORDCOUNT):** "what will X say / how many times." Pure base-rate/Poisson modeling — retail bets vibes. **Data source = transcripts** (whitehouse.gov / Roll Call for Trump; closed-caption archives for broadcasts; word-frequency base rates). Backtest needs a historical transcript+wordcount corpus → heavier NLP build, but high edge potential (no quant competition).

## NEXT (concrete)
0. **Build the weather nowcast bot:** 1-min ASOS feed + HRRR, compute live P(bucket) each afternoon, trade Kalshi when it diverges from the (slow) market. Forward-test across 10 cities. Closest match to "many near-locked trades/day."
- Other untested retail lanes by the same logic (external data decisive + slow retail): **KXAAAGAS** gas-price-tomorrow (AAA daily, sticky/predictable → near-lock), **Mentions** (KXTRUMPSAY/KXWCMENTION — Poisson/base-rate modeling from transcripts), econ data-release consensus.
1. **Live cross-venue / latency bot (forward test — the one real lane).** Poll Kalshi + Polymarket order books on the same live games; when Kalshi quote diverges from the sharp venue past spread+fee, take the stale Kalshi side; paper-trade & log fills. Also test "new-market-open staleness" (price before MMs arrive) — pure microstructure timing, forward-only.
2. **Live cross-venue bot** (forward test, the real edge): stream Kalshi + Polymarket books for the same live games (start: World Cup — 64M Kalshi vol, deep on both); when Kalshi quote diverges from Polymarket beyond spread+fee, take the stale Kalshi side. Size small, log fills.
3. Scripts (all in `~/kalshi_data/scripts/`): catalog.py, events_catalog.py, me_arb.py, favcal.py, favcal_pool.py, analyze_fav2.py, xvenue_wc.py, xpath.py, xbtc.py · weather: weather_bt.py, weather_conv.py, weather_ratchet.py, weather_bt2.py, weather_fc.py, weather_sharpen.py, weather_cond.py, weather_nowcast.py, multiyear_mae.py, fav_underprice.py. Data: events_catalog.json, events_all.json, favcal_pool.json, xbtc_rows.json, weather_fc.json, weather_sharpen.json, weather_bt2.json, fav_underprice.json, weather_nowcast.json.
4. **Best next step (agreed direction): forward paper-trade the weather forecast-buy** (daily 9am job logging forecast→bucket→live price→settlement) to confirm out-of-sample edge + real fills; and **upgrade to ensemble-distribution betting** (NBM/MOS or GEFS/ECMWF-ENS) across all temp cities + low-temp/rain markets to widen the recent priced sample sideways.

Related: [[00 - START HERE]] · [[03 - All Strategies Tested]] · [[07 - Overfitting & Validation Discipline]]
