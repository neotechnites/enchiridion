# 35 - Archetype Night (2026-07-24) — synthesis judge's night note

> Merged 22 lane ledgers (13 archetypes × 2 bursts, minus single-burst BUFFETT/EVENT-VOL/INFO-CHANNEL/JOBS/MUSK/THORP/VENUE-MECHANICS). Deduped, applied the trap checklist (note 15 / Mesh graveyard), ranked survivors by expected $/day at the $1k stage, per note 32 §4 (real-money law) / §5 (demand-led capture) / §6 (per-archetype ROI ledger).

## Verdict tuple
**132 ideas generated · 108 distinct after cross-lane dedup · TRADE 0 · PROMISING 5 · CONDITIONAL 48 · DECAY-BENCH 4 · DEAD 51 · survivors 53.**

The signature of a **capture-bound night** (note 32 §1 pt 4, predicted): the ideas are good, the data isn't on disk. 0 TRADE because every strong candidate is gated on a live fill-probe, a scout pull, or a cross-era window that the harness could not supply this night (no spawn tool — see Pipeline Problems).

---

## SURVIVORS RANKED BY EXPECTED $/DAY AT SMALL BANKROLL

Nothing is TRADE — all five leaders are gated. Ranked by realizable $/day *if the named gate clears*, with the gate stated.

### 1. BEZOS B9 — metal+gas daily wing-sell, Mon–Wed, DROP oil  *(deployable NOW; a correction to a LIVE system)*
The only survivor that improves money **already flowing** (the live unified volbook), so highest near-term $/day.
- **Rule:** sell OTM commodity-daily wings (buy NO at (1−y)·100+1¢, band y3∈[0.05,0.35)) ONLY on family∈{metal, natgas} AND weekday∈{Mon,Tue,Wed}; **exclude oil entirely.**
- **Numbers:** METAL Mon–Wed fade-NO EV **+9.1 / +9.8¢** both era-halves (nd=14/15); GAS **+5.5 / +7.6¢** both halves (nd=10/12); OIL era-fragile (+7.2 → **−5.4¢**, dies era-2). Realized wing-touch metal 1.5–3.5% / gas 2.4% vs the ~13–16% the flat premium implies. Placebo = oil (the failed cell); mechanism = thin books quote a flat OTM vol premium blind to family & weekday.
- **Traps cleared:** era-split both-halves (not one-event), own placebo (oil), named mechanism. **Residual risk:** within-day rungs correlated → true n is nd (10–15 days/cell, thin); 2-mo window not cross-era. **Gate:** 2yr confirm via the B4 harvester. B9 also *corrects* the live volbook's amplitude-ranking, which was backwards for the SELL side (oil's large amplitude = fair price, not a fat sell).

### 2. HOUSE H10 — econ point-ladder maker house (sell overpriced "Exactly X%" CPI points)
Richest per-fill door of the night; the discrete-point edge that 4 other lanes killed on the taker side **survives as the maker.**
- **Rule:** rest SELL-side quotes on the far "Exactly X%" points of KXCPIYOY-family ladders where retail overpays specific outcomes; collect spread + the overpricing.
- **Numbers:** n=20,585 trades / 34 settled ladders; passive markout **−0.91 to −0.98¢** all horizons (taker systematically WRONG); adv% 24–28%; 2¢ median spread; **naive maker net +1.47 to +1.54¢/fill.**
- **Traps cleared:** two independent series confirm (poll + point-ladder), markout on prints not stale mid. **Residual:** trades within a ladder correlate (real n ≈ 34 events); capacity thin & ~monthly. **Gate to TRADE:** live fill-probe (`work/probe-house.md`) — can't sim maker fills from prints.

### 3. HOUSE H9 — political spread-capture house (KXAPRPOTUS)
The taker-only doctrine's **first proven exception** — a slow, mean-reverting underlying where passive quoting is NOT adversely selected. Higher capacity than H10 (~194k contracts/wk this one series).
- **Rule:** two-sided quote KXAPRPOTUS in-band (10–90¢) ONLY when resting spread ≥2¢ AND outside catalyst windows (T±15min around scheduled poll drops).
- **Numbers:** n=35,335 in-band fills; naive maker net **+0.52 / +0.60 / +0.43¢/fill** (60/300/1800s), fees in; adversarial-hardened (symmetric ±20¢ tails = mean-reversion not one-sided adverse; jump-fills only +0.116¢, not catastrophic; 41% of adverse mass in top-1% of ticker-hours = avoidable). Era-split held (both halves ±0.27¢). Capacity ~$100–300/wk this series, generalizes to the slow-political family.
- **Traps cleared:** era-robust, adversarial jump-decomposition, placebo = crypto's one-sided-positive markout. **Load-bearing risk:** the spread — at 1¢ net collapses to +0.10¢ (noise); the ≥2¢ gate is mandatory. **Gate to TRADE:** same live fill-probe as H10.

### 4. INFO-CHANNEL IC2 — KXFEDMENTION spoken-count ratchet  *(live, dated, one-event-class)*
The first *live* vehicle since LASTWORDCOUNT (dormant) for the fattest per-contract edge ever recorded (+39.5¢).
- **Rule:** on Jul 29 (FOMC presser, Q&A ends ~19:30 UTC) → Jul 30 14:00 UTC close (**~18.5h post-lock window, ruled open by close_time**), buy YES sub-95¢ on words the live tally unambiguously heard (≥2-count safety margin); sell NO on words priced >5¢ clearly not said. Settle = lagged Fed transcript; fish = slow transcript readers on a scheduled event. 43 rungs, OI ~76k, two-sided.
- **Trap flag:** the proven mechanism has n=1 prior event (LASTWORDCOUNT) → **one-event-class = PROMISING-ceiling, never TRADE** on a single fire. Actionable as a paper-capture + tiny live clip Jul 29; the value is proving the live mechanism, not a recurring $/day.

### 5. VENUE-MECHANICS V2 — thin DOGE MAX-ladder top rungs overpriced (sell NO)
Tiny capacity but placebo-clean and structurally distinct.
- **Rule:** on a re-issued thin-coin MAX ladder, buy NO on the top rungs (x ≥ 1.6× spot). DOGE "Above $0.15" mid 0.455 vs implied-vol-fair ≈0.20 → buy NO @0.57, fair ≈0.80, **~21¢ edge net of ~1.7¢ fee.**
- **Numbers/placebo:** naive realized-vol model FAILED its placebo (fired +13¢ on arbed BTC too); the **relative per-rung implied-vol** test is placebo-clean — BTC 0.44→0.76 smooth smile, ETH/SOL smooth, **DOGE 0.46→1.55 pathological flat-anchored ramp.** Capacity ~$50–150 (OI 0–200).
- **Trap flag:** **one-issuance / one-coin / ~2 independent rungs = one-event.** Alt-hypothesis (rational meme fat-tail) unresolved without realized settlements. **Gate:** multi-issuance realized backtest (V8 monthly-MAX cadence is the frequency vehicle) + fill probe. PROMISING, not TRADE.

**Best stage-fit CONDITIONAL just below the line — BENTER-4 (Manheim → used-car CPI proxy):** the only door clearing BOTH the two-factor law (public BLS settle × lazy crowd) AND the fast-recycle stage. 5 live markets exist. Gated purely on a Manheim history scout — the cheapest promotion on the board.

---

## PER-ARCHETYPE SCORECARD (note 32 §6 ROI ledger — ideas / survivors, this night)

| archetype | ideas | survivors | promising | headline finding |
|---|---|---|---|---|
| ACKMAN | 10 | 6 | 0 | every liquid catalyst is anchored (Fed→futures, index→SPX opts, LLM→static incumbent). Only live door: A6c LMArena transition-timing (capture-gated). |
| BENTER | 10 | 6 | 1 | the two-factor law (public/reconstructable settle × lazy crowd) kills the consumer-price ladder family at a glance. Survivor: **#4 Manheim proxy** (best stage-fit). |
| BEZOS | 12 | 8 | 0 | falsified its own B1 data-calendar gate; found stronger **B9 metal+gas Mon-Wed** + spec'd the **B4 harvester**. |
| BUFFETT | 9 | 2 | 0 | favorite-buying DEAD across crypto (15m+hourly, n≈4700) & commodity. Only open: B4 approval / B5 Fed-hold (need pulls). |
| EVENT-VOL | 8 | 6 | 0 | BUY-the-jump priced out on every liquid ladder; seam = discrete uniform-priced point-ladders (EV4) + unrepriced thin surfaces (EV8). |
| HOUSE | 14 | 8 | 2 | **the night's two richest doors** — taker-only is fast-underlying-specific; slow/discrete-settle family is the house's native habitat (H9 +0.6¢, H10 +1.5¢/fill). |
| INFO-CHANNEL | 8 | 6 | 1 | observable-lock ratchet DEAD (29/29 rungs ≥98¢); edge survives ONLY in spoken-count/lagged-transcript subset → **IC2 live Jul 29-30**. |
| JOBS | 8 | 5 | 0 | liquid econ ladders efficient; mispricing lives only in 0-OI unfillable books. Best lead: #7 FOMC-move ladder (one pull from a verdict). |
| MUSK | 14 | 7 | 0 | 12,176-series census: liquidity = known slate; but the COUNT/MENTION/VOTE mega-family (679 series, 9M OI) is LIVE. Top new door: M7 monotone-accumulator feeds. |
| RENTEC | 13 | 3 | 0 | **flagship RETRACTED** — burst-1 R1 (+12.7¢) was a KBT source-frozen-book artifact (−12.5¢ de-contaminated). Forward doors all need a live-book capture. |
| SOROS | 10 | 7 | 0 | intra-venue behavioral overshoot is taker-dead (S3 entry-delay kill); only cross-venue S2 (Poly→Kalshi politics lag) survives, capture-gated. |
| THORP | 8 | 3 | 0 | 0 free structural locks across 4,740 liquid ladders; thin-liquid frontier is vast but INFORMATIONAL not structural → routed to BENTER. |
| VENUE-MECHANICS | 8 | 6 | 1 | MAXY ladders measure from-issuance & re-issue mid-period; thin-coin MAX ladders anchor flat → **V2 DOGE top-rung sell.** |
| **TOTAL** | **132** | **73** (53 deduped) | **5** | |

*Grading note (§4): NO archetype is culled — zero ideas have reached nestor deployment, so there is no real-money signal yet. This scorecard is a track-record accrual only. Best token-ROI this night: HOUSE (2 promising from an adversarial self-test) and BEZOS (a deployable live-slate correction). Barren-so-far: RENTEC (flagship retracted — but the retraction is honest, high-value work), ACKMAN & BUFFETT (decisive negatives, no live fruit). None dead weight yet.*

---

## CAPTURE-DEMAND SHORTLIST (note 32 §5 — demand-led, merit-ranked, NOT a reflex to the slate)

Ranked by (lanes demanding × verifies-an-unproven-edge × cost). Every item below earned its place from a *demonstrated* dead-end in the ideation, not speculation.

1. **Live KXAPRPOTUS + KXCPIYOY book-depth capture** (the HOUSE fill-probe, `work/probe-house.md`). Demanded by H9 + H10 — the **two PROMISINGs**. The ONE thing trade prints cannot answer (real two-sided fill realization at the 2¢ spread). Gates the night's richest doors straight to TRADE. **TOP PRIORITY.**
2. **B4 cross-family settlement-calibration harvester** (`work/build-harvester.md`, SPEC'D & READY). Reconstructs ~10wk post-hoc from the trades endpoint (no daemon), ~3–8 min/day, proprietary by construction. Unblocks B9's 2yr cross-era confirm + THORP/BENTER feature-model labels + BUFFETT/B8 certainty-carry + B11/B12. Compounds everywhere; cheapest high-leverage build.
3. **Far-dated (>30d) political/econ book-depth snapshots + post-print mid-drift.** Demanded by HOUSE H11 + BEZOS B3/B8 — one daemon serves the far-dated house arm AND certainty-carry.
4. **Manheim used-vehicle value index history** (scout, not daemon). Demanded by BENTER-4 — the single door clearing both the two-factor law and the fast stage. Cheapest promotion of a CONDITIONAL to a verdict.
5. **KXFEDMENTION post-presser book capture — Jul 29 20:00 UTC** (immediate, dated). Demanded by IC2. Paper-capture the live spoken-count ratchet window vs a live word-tally; proves the +39.5¢ mechanism live.
6. **capture_kbt.py book-liveness fix** — stamp each snap with a churn/frozen flag (or capture the live `/markets/{t}/orderbook` for a few BTC/ETH 15m+hourly). Demanded by RENTEC R10–R13. Without it the entire near-close crypto book lane is a stale-quote mirage (R1 retraction is the proof). **Data-quality fix, not a new feed.**
7. **New-listing / wake-up monitor over the 12,176-series catalog** (extend `listing_monitor.py` with price-PATH + settlement-outcome logging). Demanded by 5+ lanes: MUSK M1/M6/M8, THORP T8, VENUE V3, SOROS S6, EVENT-VOL EV8. A strategy-generator (Mesh: "first 48h of any new series").
8. **Live commodity-daily book/quote fill capture.** Demanded by SOROS S1/S1b (maker-overshoot complement) + the standing A6/A3 verification gap (note 32 §5). Verifies an *unproven* maker edge.
9. **Monotone-accumulator feed capture** (FlightAware/FAA/TSA/USGS-rain vs Kalshi book). Demanded by MUSK M7 + INFO IC7 — orthogonal to the entire slate, W2 mechanism generalized. Gate MUST require a fresh two-sided quote (the W2/stale trap).
10. **FOMC-day realized |SPX/2Y/DXY| move history** (one public pull, before Jul 29). Demanded by JOBS-7 + EVENT-VOL EV6 — closes the FOMC-move-ladder calibration.

---

## PIPELINE PROBLEMS (the meta-findings the harness must fix)

1. **No scout/spawn tool in the harness — the single biggest defect.** EVERY one of the 13 lanes reported it: `SendMessage` needs a pre-existing teammate; none exists; the note-32 §2 fix ("restore the scout sub-agent layer, ≤5 concurrent `researcher` scouts") was NOT delivered. Consequence: ~40 of the 48 CONDITIONAL survivors are off-disk (foreign OIS, Manheim, Poly tape, leaderboard timestamps, poll cadence, realized-print histories) and are **unkillable and un-promotable** this night. The architecture's own diagnosis (§1 pt 4, §2 pt 5) predicted exactly this failure; the harness re-created it. **Fix: expose the scout layer, or route a Ryan-run pull queue.**
2. **The frontier is genuinely capture-bound.** The cheap on-disk fruit is picked (§1 pt 4). 0 TRADE / 5 PROMISING is the correct, honest signature — not a creativity failure. The next tokens should go to **captures (the shortlist), not another idea night**, until the fill-probe + harvester + KBT fix land. A second archetype night before then would just re-derive tonight's CONDITIONALs.
3. **The capture pipeline is INJECTING a trap (KBT source-frozen books).** RENTEC's burst-1 flagship (+12.7¢ depth-imbalance, the steer's "top promotion candidate") was a stale-quote artifact — ~48% of KBT markets carry a frozen orderbook field; the "edge" was fictional sub-bid entry prices. Caught only by burst-2 widening + de-contamination. **R13 doctrine (→ Mesh): every KBT price-taking backtest MUST pre-filter churn>1 at entry.** This retro-indicts any prior KBT "book" edge. Fix the capture (item 6) or keep manufacturing mirages.
4. **The two-burst steering loop works — for disk-testable ideas only.** The correction cycle (note 32 §7) produced the night's best work: BEZOS falsified its own B1 and found the stronger B9; HOUSE hardened H1 through an ordered adversarial self-test into H9/H10; RENTEC honestly retracted its flagship; SOROS killed S3 with an entry-delay discriminator. Honest self-kills are the system functioning as designed. But steering can only sharpen what has disk data — it cannot reach an off-disk door without scouts (see #1).
5. **The discrete econ point-ladder edge keeps getting re-killed on the wrong side.** FIVE lanes hit it independently: BENTER-1 / EVENT-VOL EV4 / THORP T5 / JOBS-1 killed it as a TAKER (one-sided book, unfillable); HOUSE H10 found it PROMISING as the MAKER (+1.5¢/fill). The pipeline should auto-route "one-sided book + overpriced points" to the HOUSE maker frame instead of re-killing it as a taker idea four times.
6. **Cross-lane dedup is manual and heavy (~24 duplicate rows).** The Mesh transfer works (lanes cite each other), but the same edge — point-ladders, Poly↔Kalshi lag (SOROS S2 = VENUE V7 = HOUSE H2), spoken-count (INFO IC2 = MUSK B2/B3), the new-listing monitor (5 lanes) — is re-derived 3–5× each. Per §6 the tracking scaffold should carry an **edge-ID** so dedup is automatic and per-archetype attribution stays clean.

---

## MESH FACTS TO PROMOTE (append to note 33)
- **Taker-only is fast-underlying-specific, not universal** (HOUSE): passive markout ≈0 on slow/mean-reverting/discrete-settle underlyings (KXAPRPOTUS +0.6¢, KXCPIYOY +1.5¢/fill), one-sided-adverse only on fast BRTI crypto. The house's habitat = slow/discrete-settle, spread≥2¢, catalyst-avoided.
- **Commodity-daily wing-overpricing is FAMILY-structured, not data-calendar-structured** (BEZOS): metal+gas Mon-Wed = durable sell; oil = era-fragile (its large amplitude = fair price). The volbook's amplitude-ranking is backwards for the SELL side.
- **KBT source-frozen books manufacture fake edges** (RENTEC R13): mandatory churn>1 active-book filter on every KBT price-taking backtest.
- **BENTER two-factor law** (already in Mesh, reconfirmed): public/reconstructable settle × lazy crowd; the consumer-price ladder family fails one factor systematically.
