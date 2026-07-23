# 09 - Novel Trade Lanes (find-more-weathers) — 2026-06-25

> ⚠️ **Reconciliation (2026-07-23) — do not let entry #1 confuse you about the live vol book.** The 'longshot overpricing FAILED OOS, DO NOT TRADE' verdict below killed the UNIVERSAL-bias version, and that kill stands (correlated-sample illusion, caught by held-out series). The live **unified vol book** ([[18 - LIVE STATE (2026-07-23)]]) is the CONDITIONAL rescue of the same raw observation: overpricing is real only family-by-family (oil>commodity>index>crypto), only in the 5-35¢ band (deep tails 1-3¢ are FAIR), and only inside clock gates — the richest-10 selection IS the gate, and it cleared its own OOS bar (4.17% EV/$). Same pattern for entry #2: the commodity-lag observation matured into the gated commodity systems. This note is the Kill Taxonomy (note 15) working correctly: universal claim executed, conditional survivor promoted.

Playbook (what made weather work): retail-dominated Kalshi market whose outcome is driven by a measurable external variable + a free authoritative data feed retail doesn't exploit + a model genuinely sharper than retail gut + backtest w/ real prices+fees+no-lookahead + use Polymarket/feed-archive for long history.

## ❌ Netflix Top 10 (entertainment) — NOT weather-grade
Data: official `netflix.com/tudum/top10/data/all-weeks-countries.tsv` (5yr, 260 wk US, clean) + 76 settled Kalshi weekly events/series (KXNETFLIXRANKSHOW/MOVIE/GLOBAL).
- **#1 churns:** repeats wk-to-wk only 30% (Films)/37% (TV). Best signal = brand-new #1 repeats ~50-54%; **43-50% of each week's #1 is a NEW off-chart release** (needs release calendar, which retail also hypes). Chart history doesn't decisively beat retail. Verdict: murky/efficient, skip.

## ❌ Politics favorite-longshot (Polymarket resolved, `polifls.py`) — efficient, no edge
27 resolved multi-candidate races, candidate price at 30d/90d before resolution vs outcome (688 obs).
- Dominated by longshots priced ~0 that **correctly almost never win** (0-3¢ → 0.3%; well-calibrated). **Polymarket political longshots are SHARP** — not the overbet-longshot pattern of retail sportsbooks. Mid-band (15-50¢) too few obs + noisy; favorite band (>50¢) nearly empty. No clean edge; Kalshi likely mirrors. Skip (longshot bias not present here).

## ✅ MLB strikeout props (KXMLBKS) — STRONG candidate, mechanism validated
Market: per-pitcher K ladders (2+/3+/4+/5+/6+/7+), settle on game box score. **High frequency** (~9-15 games/day × 2 SP × ~5 thresholds = dozens/day, the many-"cities" structure). Feed: **MLB StatsAPI** (free, no key) — probable pitchers + full historical game logs.
- **Mechanism VALIDATED** (`strikeout_val.py`, 2304 start-thresholds, 2026 season): trailing-stats Poisson model (prior-starts K/BF × BF/start) **beats naive base-rate** (Brier 0.210 vs 0.221), well-calibrated 30-70%, **under-confident in tails** (P6%→13% actual; P93%→84%) = Poisson underdispersion → fix w/ negative-binomial / lambda-shrink + opponent-team-K% adjustment.
- K-rate is one of the most stable pitcher stats; retail bets pitcher name recognition / ignores matchup+innings-limits. Ladder = temp-bucket structure.
- **EDGE TEST RESULT — NEGATIVE (`strikeout_edge.py`, 6,069 pitcher-thresholds, recent priced games):** model P(K≥n) vs Kalshi price vs outcome. **Market beats model: Brier 0.158 (price) vs 0.170 (model);** trading divergences LOSES −4 to −6¢/trade. **MLB K-props are efficient** — they're a mainstream sportsbook prop; sharp books model them (lineup/park/umpire/form) and Kalshi mirrors those lines. No retail-naïveté. A fancier model is unlikely to beat sharp books net of costs. Skip.

## ❌ Mentions — "Will Trump say X this week?" (KXTRUMPSAY, `trumpsay_edge.py`) — market beats model
42 settled weeks, 316 word-markets, 36 recurring words w/ stable base rates (Obama 100%, Golden Dome 10%). Base-rate model (prior weeks, expanding, shrunk).
- **Mechanism real:** base-rate Brier 0.186 vs naive 0.249 (n=231), cleanly calibrated (0-10%→8%, 90-100%→94%).
- **But MARKET BEATS MODEL** (n=70 priced): Brier price 0.165 vs model 0.231; trading divergence LOSES −3 to −12¢. (A tiny n=15 early-week sample looked +11¢ — small-sample noise.) **The crowd is news-aware**: a normally-40% word that's topical this week is correctly priced ~90%; the static base rate misses context → fades it and loses. Efficient vs simple model. Mild price-overpricing in 0.4-0.7 band (n small, not tradeable).

## ✅✅ METHOD CHANGE THAT WORKED — brute-force mispricing sweep (`broad_mispricing.py`)
Stopped guessing one model per category; instead swept settled markets across all retail categories, priced each at MID-LIFE (open..close midpoint, where slow-convergence lives) vs outcome (1,597 obs). Two real inefficiencies fell out:
1. **LONGSHOT/UNDERDOG OVERPRICING — ❌ FAILED OUT-OF-SAMPLE. DO NOT TRADE.** In-sample (`broad_mispricing2.py`, original ~25-series sweep) it looked robust: buy-NO on .08-.40¢ contracts +3.7¢, positive in all 5 categories + both regime halves. **But the held-out test (`heldout_longshot.py`, a DIFFERENT set of series never used to find it) REVERSED it: underdog-NO = −11.84¢, favorite-YES = −12.67¢.** Held-out calibration flips sign (8-25¢ underdogs UNDERpriced: priced .11-.19 → win .24-.37). By category: Financials −36¢ (index underdogs UNDERpriced/fat tails), Sports-prop −4¢, only held-out Commodities still +15.5¢ (= the directional commodity-trend-lag, not a universal bias). **Lesson: the in-sample "robust across 5 categories + both regimes" was a CORRELATED-SAMPLE ILLUSION** — the original sweep was dominated by falling-commodity/gas markets sharing one distortion; the sign of mispricing is market-structure-specific, NOT a universal longshot bias. Caught by true-OOS held-out-series test (the discipline in [[07]]). The mid-life calibration sweep is still a useful *hypothesis-finder*, but every cell needs held-out confirmation before trust.
2. **COMMODITY DAILY markets LAG SPOT TREND (weather-like, directional).** Favorites "above $X" overpriced where commodity FELL (Brent 85¢→62% actual, Silver 84¢→56%) but UNDERpriced where it ROSE (Natgas 87¢→94%); Gold ~fair. Raw "buy NO on commodity favorites" = +8¢ but that's a trend artifact of this period. Real signal = **daily commodity markets under-react to observable intraday/recent spot drift** (vs crypto-hourly which was efficient — commodities are thinner). Verify: current spot vs strike at mid-life should beat Kalshi price (the weather test). Feeds: Coinbase PAXG=gold; oil/silver via Yahoo/other. Directional/regime → trade WITH the spot trend.
Other cells flagged: Entertainment 35-50¢ underpriced (+.17, n=31 small); rain underpriced (n small).
**NEXT: (a) regime-split + execution-aware EV on the longshot-NO strat (per category); (b) commodity spot-vs-strike verification.**

## META-LESSON (why weather won and these didn't)
Weather temp markets win because **(a) no sharp professional pricer** (Kalshi MMs + retail, NO sportsbook equivalent) AND **(b) a world-class free quant model (NWS/ECMWF) that beats casual guessing.** The 3 failures each break a condition: Netflix=unmodelable churn; politics=Polymarket already sharp; MLB-K=mainstream sportsbook prop, Kalshi mirrors sharp lines.
→ **Hunt rule: target markets with NO sharp betting-line equivalent + a public quantitative feed.** Avoid anything books actively price (mainstream sports, financial, Fed). Best remaining fits = **weather-ADJACENT environmental** (rain, low-temp, wind, snow, AQI, hurricanes — NWS/NOAA/AirNow feeds, niche, retail-set) and other no-book niches.

## ❌ GPU compute prices (KXH100W/KXB200Q, Science) — DATA GATED
Settles on **Ornn (ornnai.com)** GPU price index. Right profile (observable index, trending prices, no sharp pricer) BUT ornnai.com returns 307 on all paths — gated/auth-walled, not freely accessible. Can't model/backtest without the index. Blocked unless an Ornn API/key is obtained.

## SESSION VERDICT (5 non-weather categories tested → all ruled out)
Sports (K-props: sharp), Elections (Polymarket sharp), Entertainment (Netflix: unmodelable churn), Mentions (TrumpSay: crowd news-aware, beats base rate), Science/GPU (data gated). **Weather remains the only clean win.** Honest meta-finding: **Kalshi is NOT broadly "ripe with weather-likes" exploitable by simple public-data models** — in most categories the crowd/market is efficient relative to a naive model, OR a sharp book prices it, OR the outcome is unmodelable, OR the data is gated. Weather is special: superhuman free model (NWS/ECMWF) + no sharp pricer + lazy crowd + accessible data + high frequency.
**Realistic paths to MORE edges (all require real investment, not a free lookup):** (a) a real-time news/NLP model to beat the crowd's context-awareness (Mentions); (b) obtaining a gated data feed others ignore (Ornn GPU prices); (c) live in-game sports latency vs Polymarket (forward-only, [[08]]); (d) low-frequency accumulation-ratchet niches w/ public feeds (disease counts via CDC, SpaceX launch count) — real but tiny capacity.

## ✅ LOW-TEMP markets (KXLOWT, ~15 cities) — edge TRANSFERS (verified prediction side)
Same NWS/Open-Meteo+IEM pipeline as highs, just `temperature_2m_min` / IEM `min_temp_f`. Forecast MAE 2025: **MIA 1.43, ATL 1.42, PHX 1.19** (tradeable, ~= highs); NY 2.07/CHI 2.03 (a bit harder — overnight microclimates); DEN 2.58 (skip). By the −0.93 MAE→EV mechanism the +EV edge transfers in low-MAE cities. **Deploy now via the existing forecast-buy bot; ~doubles the weather book.** (Plus the ~7 high-temp cities beyond the original 8 — DC/Dallas/Houston/Vegas/SF/Philly/etc.)

## CANDIDATE MENU — markets fitting the hunt rule (no sharp betting line + public quant feed)
Ranked by weather-likeness / confidence:
1. **Daily RAIN (KXRAINNYC + city monthlies)** — NWS/Open-Meteo PoP (calibrated public feed) vs Kalshi; binary, retail-set, backtestable (Open-Meteo precip-prob archive + IEM precip + Kalshi prices). Genuinely different from temp. ← test next.
2. **Intraday temp (KXTEMPNYCH "temp at 3pm")** — forecast + nowcast; same feed.
3. **SpaceX launch count (KXSPACEXCOUNT)** — observable manifest/schedule + within-month accumulation ratchet; no sharp pricer; novel. Niche volume.
4. **Disease case counts (KXMEASLES/KXTBCOUNT)** — CDC public trend extrapolation; monthly, slow.
5. **Hurricane/tornado counts (KXTORNADO/KXHURCTOT/KXTROPSTORM)** — NOAA seasonal + climatology; low freq.
Avoid (have sharp pricers / efficient): mainstream sports props, financial up-down, Fed/CPI (futures), politics (Polymarket sharp).

Related: [[08 - Broad-Kalshi & Cross-Venue]] (weather) · [[07 - Overfitting & Validation Discipline]]
