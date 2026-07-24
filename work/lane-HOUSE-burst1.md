# Lane: THE HOUSE — Burst 1 (2026-07-24)

Archetype: THE HOUSE — be the quoter ONLY where NOT adversely selected. The taker-only
doctrine (maker = informed-flow magnet, 3 proofs) marks where pickoff lives; this lane
hunts its COMPLEMENT.

**The derivation (the lane's whole thesis):** every one of the 3 pickoff proofs is on
CRYPTO 15m/hourly books — a continuously-moving BRTI underlying where fills "cluster
exactly when locks weaken" (momentum continues → maker loses). Adverse selection =
f(how much fast/private info exists about the settle before it settles). So the door is:
**find markets whose underlying is SLOW and MEAN-REVERTING, where a resting quote hit on
a move reverts back into profit instead of continuing against me.** Political
resolution markets (RCP-average settle) are the native habitat — the poll-clock note
already proved KXAPRPOTUS is mean-reverting (autocorr −0.17; post-jump continuation
−7.8%@2h). That fact + the maker-death doctrine collide into H1.

Spin-up done (18→21→23→20→32→34→33). Graveyard grepped (13, archive/03, notes 27-31):
maker-on-crypto DEAD 3× · resting-sell vs extreme opens DEAD · KXMVE parlay = RFQ-only
DEAD but flagged "future bookmaking lane" · Poly-US maker FREE (0 fee) · fee-exempt maker
"on many markets" · one-sided which-X ladders (THORP: asks-only, Σbid≈0). H1 is NOT in
the graveyard — every prior maker test was crypto; slow-political maker is virgin ground.
Scouts spawned: 0 (flagship proven on-disk; off-disk doors marked, non-duplicative w/ VENUE V7).

## Flagship result (H1) — the taker-only doctrine's first proven exception
Markout / adverse-selection test on 62,762 KXAPRPOTUS trades (72 weekly tickers).
Markout = taker_dir × (price[t+h] − price[t]); >0 = taker right = MAKER ADVERSELY SELECTED.

| horizon | ALL trades | in-band 10-90¢ |
|---|---|---|
| 60s | −0.059¢ (adv 26.5%) | +0.043¢ (adv 31.8%) |
| 300s | −0.118¢ (adv 27.7%) | −0.036¢ (adv 32.6%) |
| 1800s | −0.019¢ (adv 31.8%) | +0.137¢ (adv 37.3%) |
| 7200s | −0.070¢ (adv 35.9%) | +0.056¢ (adv 41.8%) |

Markout hugs ZERO (±0.15¢) at every horizon; the taker is right FAR less than half the
time (adv% 27-42%). On crypto this number is large-positive (fills settle ITM 70-76%).
**Era split holds:** EARLY (JUN, n=14k) markout +0.18/+0.18/+0.27¢; LATE (JUL, n=21k)
−0.07/−0.18/+0.05¢ — both eras within ±0.27¢, neither shows crypto-style adverse drift.
Spread proxy (adj opp-side takers <120s): median **2.0¢**, mean 3.0¢. Maker fee @50¢ =
0.44¢/side. Capacity: 194k contracts/wk, ~$97k/wk gross notional on this ONE series.

## Ledger

| # | idea | mechanism / fish | kill-test | numbers (n, EV, split) | verdict | files |
|---|------|------------------|-----------|------------------------|---------|-------|
| H1 | **Political spread-capture house** — two-sided quote KXAPRPOTUS in-band strike, capture the 2¢ spread | Fish: retail takers chasing RCP-noise/news that MEAN-REVERTS (settle = arithmetic average, no fast underlying). Maker hit on a jump reverts back → house wins where crypto-maker dies. Placebo = crypto's large-positive markout. | Markout: after taker hits my resting quote, does price continue (adverse) or revert (benign)? | n=62,762 trades; in-band markout ≈0 (±0.15¢) all horizons; adv% 27-42% (<<50%); era-split holds (both halves ±0.27¢); spread 2¢ vs 0.44¢ maker fee → net ~+0.5-0.9¢/round-trip; cap ~$100-300/wk this series | **PROMISING** (gate to TRADE = live book fill-probe: do two-sided fills actually realize at the 2¢ spread? — trade data can't answer fill realism) | house_appr_markout.py, house_appr_split.py |
| H2 | **Poly-US free-maker spread harvest, Kalshi-hedged** — quote wide Poly-US political books (maker FREE), hedge net inventory on Kalshi | Fish: uninformed US-retail churning a wide Poly book. Maker = $0 fee → keep the FULL spread (vs Kalshi's 0.44¢ drag). Distinct from V7 (arb both sides); this = provide-liquidity-and-hedge. | Poly-US political book spread width + two-sided flow (off-disk) | Off-disk — no data. Prior: Poly-US = QCX DCM, maker FREE confirmed (note 13). | **CONDITIONAL-research** (scout: pull Poly-US CLOB/gamma political books for top-20 Kalshi-matched events; report median spread + daily two-sided flow; flag where spread>4¢ & flow two-sided) | — |
| H3 | Parlay bookmaking on KXMVE (WE become the house) | Fish: 22.1M contracts of retail lottery demand, 14-25% observed juice — but no accessible quoter | Does KXMVE have a resting book we can post into, or is it RFQ-only (SIG's private auction)? | `series_ticker=KXMVE` → **0 listed markets** on public endpoint; confirms RFQ-only, no book to quote into. We can't be the house — flow never touches a resting order. | **DEAD** (structural — RFQ closed to public makers; winner's-curse vs pros even if we could) | (api check) |
| H4 | House on decided-but-open COUNT markets (rest asks at truth, let vibes-buyers lift) | Fish: vibes-traders on a mathematically-decided outcome; zero adverse selection (outcome locked) | Is there spread to capture on decided rungs? | Mesh: decided/observable-count rungs already priced ≥98¢ (29/29 locked, zero free) → no spread. Only spoken-count transcript-lag subset (LASTWORDCOUNT) has a gap, and that's the proven TAKER edge — resting an ask adds nothing over taking. | **DEAD-cited** (no incremental house edge; decided rungs saturated; = MUSK COUNT taker) | — |
| H5 | Weather-bin house off the ensemble feed | Fish: quoters who don't read NWS (Mesh). Rest two-sided quotes on KXHIGH bins, re-price ONLY on NWS ensemble update → capture uninformed retail without being the picked-off one. Ensemble taker edge is +8.2¢ → big buffer to survive as maker. | Markout on KXHIGH trades around NWS release times (do fills cluster at forecast updates = adverse, or disperse = benign?) | Needs KXHIGH trade + NWS-release timestamps (not on disk). Adverse moments are PUBLIC & scheduled (~4×/day) → avoidable by quote-pull gate. | **CONDITIONAL-research** (= the KXHIGH weather-book capture already named merit in note 32 §5 W2; this is its maker-viability question) | — |
| H6 | Bid-side house on one-sided longshot which-X ladders (post the missing bid) | Fish: retail dumping a longshot into my bid | THORP found these ladders asks-ONLY (Σbid≈0). Who hits a bid? | The PROFITABLE house side (SELL longshots to lottery buyers) is already SATURATED by incumbent MMs — that's WHY books are asks-only. The empty bid-side is empty because buying a longshot YES = accumulating the 95%-loses side = adverse. The one-sidedness IS the market signaling which side is picked off. | **DEAD** (structural — profitable side saturated; empty side is empty because adverse) | — |
| H7 | Fee-exempt-maker × slow-underlying multiplier | Fish: n/a — venue subsidy. If a slow-political series has maker fee-exemption, H1's spread capture is nearly FEE-FREE (+0.9¢ vs +0.5¢/round-trip). | Is KXAPRPOTUS maker fee exempt? | Public market metadata does NOT expose fee/maker fields (fee-schedule/program question, like S6/V4). Would ~double H1 EV if true. | **CONDITIONAL → SURFACE to Ryan** (verify maker-exemption on political series via Kalshi fee schedule; upgrades H1) | — |
| H8 | Generalize H1 across the slow-political family + catalyst-avoidance gate | Fish: retail churning between scheduled catalysts (poll drops, debates) with no new info; pull quotes AT catalysts (the discrete adverse jumps), harvest dead-time churn | Which other slow-political series have KXAPRPOTUS-level liquid churn? (senate/midterm/generic-ballot were intraday-dead per note) | KXAPRPOTUS is the only currently-liquid one (194k/wk). Gate = "series must clear a churn-volume floor." Catalyst-avoidance is a pure-upside gate on H1. | **CONDITIONAL-research** (scout: scan Kalshi political/econ series for two-sided daily volume ≥ KXAPRPOTUS floor; the family's TRADE surface) | — |

## Verdict summary
- **1 PROMISING** (H1 — political spread-capture house; markout≈0, era-robust, placebo=crypto's positive markout; the taker-only doctrine's FIRST proven exception. Gate to TRADE = live book fill-probe, modest capacity ~$100-300/wk this series but generalizes to the whole slow-political family).
- **2 DEAD** (H3 KXMVE RFQ-closed structural; H6 one-sided-ladder profitable-side-saturated structural).
- **1 DEAD-cited** (H4 decided-COUNT = existing taker edge, no house increment).
- **4 CONDITIONAL-research** (H2 Poly free-maker scout; H5 weather-maker = note-32 W2 capture; H7 maker-exemption Ryan-surface; H8 political-family churn scout).

## Net-new HOUSE fact for the Mesh
**The taker-only doctrine is family-specific, not universal.** "Passive orders are informed-flow
magnets" was proven ONLY on crypto (fast BRTI underlying, momentum continuation). On a SLOW,
mean-reverting underlying (KXAPRPOTUS RCP-average) passive quoting is NOT adversely selected:
markout ≈0 across all horizons, taker right <50% of the time, era-robust. The house edge lives
wherever the underlying has no fast/private info and mean-reverts — political/econ resolution
series are the native habitat. Adverse selection concentrates at scheduled public catalysts
(poll drops) which are AVOIDABLE by a quote-pull gate. → Opens the political market-making door.

## Capture-demand surfaced
- **H1→TRADE:** live KXAPRPOTUS (+ political family) book-depth capture — measure real two-sided
  fill realization at the quoted 2¢ spread (the one thing trade data can't answer). This is the
  fill-probe, not an efficacy test — efficacy (non-adverse-selection) is proven above.
- H2: Poly-US political CLOB book snapshots (spread + two-sided flow).
- H5: KXHIGH weather-book capture (already note-32 §5 W2 merit).
