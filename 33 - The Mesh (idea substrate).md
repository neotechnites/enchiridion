# 33 - The Mesh (idea substrate) — the context that makes ideas

> Ryan's order (2026-07-24): *"if my context helped you ideate, you need to be that input for these claudes."* This note IS that input — the working memory of the Claude whose batches built the slate, written at full density. Ideas are **collisions between specific facts**; summaries don't collide. Every ideator reads this COMPLETELY before its first idea. It is a living document — every session that learns a colliding fact ADDS it here.

## THE METHOD (how the winning ideas actually happened)
1. **Read the contract's fine print before the market.** Settlement SOURCE, averaging window, rounding, tie rules, close trigger. The lock edge existed because Kalshi crypto settles on a **60-second BRTI average** while Poly point-settles — one sentence of fine print, whole edge. TRUMPMENTION died because it closes at speech-end; LASTWORDCOUNT paid +39.5¢ because it doesn't. Same door, opposite outcomes, decided by rules text.
2. **Ask "who is on the other side, and why are they wrong or forced?"** No named fish = no idea. The fish list below is earned, not guessed.
3. **Collide two unrelated facts.** Fee quadratic × favorite-longshot bias → the extremes are the cheap casino. 60s-average × last-minute variance → lock. Weekly storage report × trend persistence → gas AR(1). Force the collision: pick one STRUCTURE fact and one BEHAVIOR fact and ask what breaks.
4. **A kill is a beginning.** Three of five live systems are rescued conditional kills (gas trend-gate, calm-clock wings, streak's ≤44¢). When something fails unconditionally, immediately slice by regime/clock/family/day — the gate IS the strategy.
5. **Prices are evidence.** "Cheapness is information" — a side priced 8¢ genuinely loses ~92% unadjusted; the edge is never "buy cheap," it's finding where the price formation process is structurally lazy (stale after spot moves, wide because thin, sticky at listing).

## WHO IS THE FISH (by family — earned knowledge)
- **Crypto 15m/hourly:** retail momentum-chasers. They overpay continuation after streaks (streak edge), overpay lottery tails 5-35¢ in calm regimes only (calm-clock gate), and the book reprices bursts in 1.9s (latency dead). Deep tails 1-3¢ are FAIR — the fish isn't there.
- **Commodity dailies (gold/silver/brent/gas):** thin books, sticky quotes that lag spot intraday, wing overpricing amplitude the LARGEST of any family (oil>commodity>index>crypto), Monday richness (night-rep find, unverified). The fish is the absent market-maker.
- **Weather:** nobody models ensembles vs the settle station; forecast-error is city-specific and calibratable; books go one-sided/degenerate. Fish = whoever quotes without reading NWS.
- **Index dailies:** priced efficiently EXCEPT event days — CPI/PCE/NFP mornings misprice the realized-move distribution (buy wings T-15min); earnings gaps flow into NDX bins next morning.
- **Sports:** pre-game = sharps + bots, arbed at every size (proven at all tiers). In-game = the open question (tonight's fixed watcher decides). Fish, if any, lives only in the seconds after events.
- **Politics/mentions/counts:** settlement is RULES-LAWYERING. COUNT-type contracts reward literal reading; the crowd trades vibes. New COUNT listings are the watchlist trigger.
- **Everything at 90-99¢:** the fee-cheap zone (fee ≈0.3¢ at 95 vs 1.75¢ at 50). Ryan's dream shape lives here: "when X, resolves this way 99%, priced 95."

## STRUCTURE FACTS (the collision fuel)
- Fee = ceil-per-ORDER of 0.07·P·(1−P) — quadratic, maximal at 50¢, near-zero at extremes; maker 25%/often exempt (but maker = pickoff, see graveyard). Sizing has fee-floors: tiny clips at extreme prices pay 5× (C* floors).
- Settlement identities: 15m and hourly-rung settle on the SAME print when thresholds order correctly (T≤K / T≥K guards) — dutch locks; range-buckets overlap ladders; any two contracts sharing a settle source are a potential book.
- 60s settlement averaging (crypto) = a variance filter that makes late leads stickier than the book prices. Poly's point-settle lacks it. ANY venue-pair settling the same event by DIFFERENT rules is a free option catalog — this generalizes beyond crypto.
- Kalshi kills API surfaces without warning (410'd the order endpoint July 2026); most public tooling is silently broken RIGHT NOW — competitors running dead endpoints is itself an edge window.
- New listings are sloppy for ~48h: wide books, unmodeled rules, no bots. The mention-ratchet lived there. A listing monitor is a strategy-generator.
- Trades retention ~10 weeks; books unrecorded by the exchange — CAPTURED data is proprietary. What athena records, nobody else has.
- Clock gates are real and family-specific: crypto wings pay only 22-12 UTC (calm hours); oil richest 13-21 UTC; entry windows (first-60s) and expiry minutes have their own microstructure.
- Calendar autocorrelation: weekly government prints (EIA gas) trend-persist 69-83% when |4wk-trend|≥median, 46% flat — the GATE made it; the same shape should exist in other weekly/monthly official series (CPI components, jobless claims, drilling counts).

## GRAVEYARD, COMPRESSED (never re-litigate, always collide against)
Maker/resting orders = informed-flow magnets (91-100% pickoff, 3 proofs) · latency plays dead (book beats you in 1.9s) · pre-game sports arbed at every size · deep tails 1-3¢ fair · >35¢ efficient · midnight reversal = 2yr coinflip · parlays = RFQ-only, SIG quotes them · pooled cross-asset significance = fake (BTC/alts share regime) · candle-lookahead + stale-quote entries manufacture 71%-win mirages · one-event edges (all P&L from one print) = PROMISING never TRADE.

## WHERE NOBODY IS LOOKING (standing open doors)
Cross-venue settlement-RULE differences (same event, different fine print) · new COUNT/ratchet listings · LP/incentive programs and fee promos · RFQ surfaces where quoting isn't adversely selected · the first 48h of any new series · official-data autocorrelation beyond gas · in-game sports seconds-after-events (verdict pending) · holiday/session-boundary quirks in thin families · markets where the settle source updates on a KNOWN lag the price doesn't respect.

*Add to this file or you didn't learn it. — The Mesh*
