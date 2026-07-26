# Verify: MM competition on Kalshi small/thin markets

Question: Are Kalshi's SMALL/THIN markets — econ point-ladders (CPI "at exactly X.X%" rungs)
and slow political markets (presidential approval weeklies) — already served by professional
market-making bots, or does automated quoting concentrate only on flagship markets?

Date: 2026-07-25. Research ~40 min, public evidence only.

---

## 1. Kalshi's official MM / liquidity-provider programs

**Designated institutional market makers (invitation / Market Maker Agreement):**
- **Susquehanna (SIG)** — first dedicated institutional MM, onboarded **April 2024**
  (via subsidiary Susquehanna Government Products). Kalshi CEO Tarek Mansour: SIG's entry
  "changed everything." SIG's own page says it provides two-sided liquidity across
  "finance, crypto, elections, culture, weather, sports, and more" — i.e. the broad flagship
  categories. SIG built the first dedicated prediction-markets desk (2023).
  - https://www.businesswire.com/news/home/20240403664852/en/Kalshi-Onboards-Its-First-Dedicated-Institutional-Market-Maker
  - https://sig.com/predictions/
- **Jump Trading** — **Feb 2026**: taking small equity stakes in Kalshi AND Polymarket in
  exchange for providing market-making; recruited ~20 staff for the prediction-market desk;
  goal explicitly framed as keeping "two-sided quotes available and tradable through major
  news cycles." Completed Kalshi's first block trade (Apr 2026). => flagship / high-volume focus.
  - https://www.coindesk.com/business/2026/02/10/jump-trading-to-take-small-stakes-in-polymarket-kalshi-bloomberg
  - https://www.theblock.co/post/389086/jump-trading-to-serve-as-polymarket-and-kalshi-market-maker-in-exchange-for-stake-bloomberg
- **Kalshi's own trading arm** seeds markets with bids/offers; a class-action alleges Kalshi
  itself set prices unfavorably. Consistent with *platform seeding* on new/thin listings rather
  than external MM quotes. (Could NOT independently confirm the uniform 0.54/0.46 seed prices
  in any public source — unverified.)
  - https://igamingbusiness.com/sports-betting/class-action-suit-against-kalshi-market-makers/

**Retail / semi-pro incentive programs (the "who fills the thin markets" question):**
- **Liquidity Incentive Program (LIP)** — runs **Sept 15 2025 – Sept 1 2026**. Pays for resting
  orders scored by size, proximity to best bid/ask, and consistency (per-second snapshots).
  Target size band 100–20,000 contracts. Daily rewards **$10–$1,000**. "All Kalshi markets
  potentially eligible; active reward periods marked on each market page." Critically,
  **explicitly EXCLUDES members who hold a Market Maker Agreement** (i.e. SIG/Jump) — this
  program is aimed at retail/non-designated makers.
  - https://help.kalshi.com/en/articles/13823851-liquidity-incentive-program
  - CFTC filings: rules09082530054.pdf (Aug 2025), rules02112639183.pdf (Feb 2026 update)
- **Liquidity Provider Program** — Kalshi designates "Incentivized Series" + "Incentive Periods";
  MM-Agreement holders bid (auction) for the reward. Series are chosen by Kalshi, not blanket.
  - https://help.kalshi.com/en/articles/15410219-liquidity-provider-program
- **Combo Incentive Program**, **Sportsbook Hedging Rebate** (Feb 2026) — sports-focused.
- General maker/taker structure: makers pay ~1/4 of taker fee (4:1), small maker rebate on most
  markets. https://help.kalshi.com/en/articles/13823805-fees

Interpretation (key): Kalshi is **actively paying to bootstrap liquidity provision** via LIP/LP
programs launched only in late-2025 — the "garage-band market maker" thesis. The existence and
timing of these programs is itself evidence that many series (esp. niche categories) were NOT
adequately quoted by the designated pros, and Kalshi wants semi-pro/retail bots to fill them.
  - https://ufoholdings.substack.com/p/kalshis-new-liquidity-incentives (article thesis:
    program designed to attract "semi-professional liquidity providers with subject-matter
    expertise in niche categories"; speculates Kalshi could allocate ~$5M/month.)
  - https://fiftycentdollars.substack.com/p/kalshis-new-liquidity-incentives

## 2. Trading firms / HFTs on prediction markets 2024–2026

- SIG (2024, first), Jump Trading (2026). SIG + Robinhood JV "Rothera" prediction exchange;
  SIG+Robinhood acquired LedgerX. Sector hit >$50B monthly volume (World Cup driven).
  - https://www.panewslab.com/en/articles/10145921-3e57-45d3-95e6-c3910ddcb961
  - https://www.ingame.com/robinhood-susquehanna-own-prediction-market/
- Where they quote: reporting consistently ties the pros to **major markets / major news cycles**
  (elections, sports, macro headline events). No public statement of any pro firm committing to
  quote econ point-ladder middle rungs or approval weeklies specifically.

## 3. Community / practitioner + spread anecdotes

- Robinhood-integrated flagship sports (NFL/NBA/MLB): buyer/seller within **1–2c**. Same source:
  "on less active contracts that gap can stretch to **8 or 10c**."
  - https://www.si.com/prediction-markets/reviews/kalshi (Robinhood integration / 1-2c)
- General: "thin markets can have spreads of $0.05+; deep markets trade inside $0.01." Beginners
  on niche markets "sometimes trading with a 10–15% spread disadvantage."
  - bettorsinsider.com/predictions/reviews/kalshi ; oddsassist.com/prediction-markets/bids-and-asks/
- Practitioner bot guides frame **thin Kalshi markets as an OPPORTUNITY for a well-tuned MM bot**
  to "be the counterparty" — i.e. treated as *unclaimed spread*, not already-arbed:
  - https://www.botforkalshi.com/blog/kalshi-trading-strategies-guide
  - GitHub kalshi-trading topic: mostly hobbyist bots; econ-event bots pull FRED data for CPI/Fed.
    https://github.com/topics/kalshi-trading
- Could NOT surface direct r/Kalshi threads via search (search returned no reddit hits;
  site:reddit.com blocked/empty). Gap in the community-anecdote leg.

## 4. Quantitative / academic analysis

- **Bürgi, Deng & Whelan, "Makers and Takers: The Economics of the Kalshi Prediction Market"
  (GWU WP 2026-001, Jan 2026)** — transaction-level data, 46,282 contracts / 300k+ prices,
  2021–Apr 2025. Strongest quant source.
  - Even **top-decile** Kalshi markets had avg final volume of only **$526,245**; "at any point
    in time the amount of liquidity available is far smaller." Explicitly: even largest-volume
    markets are "small relative to the kinds of markets professional investors will typically be
    willing to participate in."
  - Figure 2 shows the **April CPI order book is thin** (small amounts resting) — this is a
    flagship econ event and it is still shallow. Large would-be makers "may have to post prices
    less advantageous" and some maker orders "would not be matched."
  - Bid-ask spreads are endogenous to the maker/taker matching; makers earn higher returns than
    takers (favorite-longshot bias). Politics category has smaller effects / lower volume.
  - https://www2.gwu.edu/~forcpgm/2026-001.pdf  (mirror: https://www.karlwhelan.com/Papers/Kalshi.pdf)
- Category volume: Politics/Gov ~**0.6%** of Kalshi volume ($15.3M in one snapshot) — political
  markets are a tiny slice; sports/macro dominate.
  - https://defirate.com/prediction-markets/ ; https://lycheedata.com/guides/kalshi-volume
- Risk.net: "Liquidity on Kalshi, Polymarket too thin for institutional use" (headline; body
  paywalled). CNBC: "Prediction markets mostly have thinly traded contracts" (Jul 2026).
  - https://www.risk.net/markets/7963633/ (404 on direct fetch, headline indexed)
  - https://www.cnbc.com/2026/07/02/prediction-markets-mostly-have-thinly-traded-contracts-.html

---

## VERDICT (for our two vehicle types)

### Econ point-ladder rungs (CPI "at exactly X.X%", 10-90c) — **MIXED**  (confidence: moderate)
The parent CPI *event* is a flagship macro release: pros (SIG) cover the "economics/financials"
category and quotes tighten hard around the print. BUT the deliverable is individual middle-of-
ladder rungs, and the GWU paper's own April-CPI order book (Figure 2) is thin even for this
flagship. Off the release window and away from the modal rung, expect wide/uneven quoting and
requote gaps — space for a bot, not a saturated one.
- Strongest PRO-BOTS: (1) SIG explicitly covers economics/finance; (2) headline CPI cited as
  among the tightest-spread, deepest Kalshi markets around releases; (3) FRED-driven econ bots
  are a common practitioner pattern (GitHub) => automated interest exists.
- Strongest UNSERVED: (1) GWU Fig 2 shows thin April-CPI book even at the flagship; (2) even
  top-decile markets avg only ~$526k volume, liquidity "far smaller" intraday; (3) per-rung
  ladders fragment that already-thin volume across many strikes → tail rungs starved.

### Political approval weeklies — **LIKELY-UNSERVED / thin-MIXED**  (confidence: moderate)
Approval is not an election flagship; Politics/Gov is ~0.6% of platform volume and these are
slow weekly re-lists. Designated pros (SIG/Jump) are documented aiming at "major news cycles"
and high-volume series. Kalshi's late-2025 LIP/LP retail-maker subsidies exist precisely because
such niche series lacked organic quoting.
- Strongest PRO-BOTS: (1) SIG lists "elections/politics" in coverage; (2) LIP makes "all markets
  potentially eligible" so a rebate-farming bot *could* appear; (3) Kalshi runs its own seeding
  arm so a book is rarely fully empty.
- Strongest UNSERVED: (1) Politics ~0.6% of volume, approval is a sub-niche of that; (2) pros'
  public commitments target major news cycles / flagship, not slow weeklies; (3) the very
  existence of the garage-band LIP (excludes SIG/Jump, targets niche subject-matter makers,
  launched Sept 2025) is an admission these series were under-quoted.

### Overall read for our lanes
Automated quoting on Kalshi **concentrates on flagship / high-volume / major-news series**
(sports, elections, headline macro). Our two vehicles sit at the thin end: the CPI ladder is
adjacent to a flagship but fragments into thin rungs (MIXED); approval weeklies are squarely
niche (LIKELY-UNSERVED). Both plausibly leave exploitable spread/requote-lag room, with approval
weeklies the more clearly open lane. Retail/semi-pro competition is the *rising* risk in both,
because Kalshi is now paying (LIP, $10-1000/day) to draw exactly this kind of bot into thin books.

### Evidence gaps (do not over-trust)
- No live order-book snapshot of our exact series (CPI rungs, specific approval weeklies).
- 0.54/0.46 uniform seed price NOT confirmed in any public source — treat as unverified.
- No direct r/Kalshi / HN practitioner thread surfaced (search returned none).
- Risk.net + several help/CFTC PDFs were paywalled / image-only, read via headline + secondary.
