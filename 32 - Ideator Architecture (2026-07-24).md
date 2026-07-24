# 32 - Ideator Architecture: why Session 3 won + the archetype design (2026-07-24)

> Written after the first overnight autonomous ideator run (notes 27-31) produced **0 TRADE / 0 PROMISING** vs Session 3's interactive sprint that produced the *entire live slate*. This note diagnoses the gap and specifies the next-gen ideator system. Design proposed 2026-07-24; roster + capture targets pending Ryan's confirm before the mechanism is built.

## 1. WHY SESSION 3 PRODUCED WINNERS (the diagnosis, from notes 13/15/20)
The interactive "creativity sprint" turned dozens of ideas into ~10 TRADE/PROMISING findings and 5 live systems. The autonomous run did not. Five differences, ranked by leverage:

1. **Door breadth, not slate variants.** Session 3 walked genuinely NEW markets — weather ensembles, sports cross-venue, mention-count ratchets, earnings wings, MOC, box office. The autonomous run collapsed back into the slate's own markets (BTC 15m, commodity dailies) every rep. *The playbook's own bar: "a batch that's just variants of the existing slate is a failed batch."* This is the single biggest miss.
2. **Effort.** Session 3 was Opus at high effort with Ryan steering; the loop ran `researcher-med` (Opus **medium**). Creativity is effort-sensitive.
3. **The conditional gate-hunt.** 3 of 5 live systems are *rescued conditional kills* (gas 46%→69-83% via trend gate; crypto wings via calm-clock; vol-book via family-amplitude). The autonomous run mostly stopped at DEAD/CONDITIONAL without the mandatory gate-hunt where winners actually hide.
4. **Data breadth on disk.** Session 3 had ~380 scripts + pulled data, so most doors had an immediate cheap kill. When data was missing (weather, cross-venue) a CAPTURE was launched. The loop went "capture-bound" precisely because the cheap on-disk fruit was already picked — **the frontier now needs new data (see §5).**
5. **Steering.** Ryan caught bugs (the threshold-ordering guard) and pushed creativity in real time.

**Honest split of causes:** (1)(2)(3)(5) are fixable in the harness/prompts. (4) is real — some of the next edges require new athena captures, not just harder thinking.

## 2. THE FIX (next-gen ideators)
- **Model:** `researcher` (Opus **high**), never `researcher-med`, never Fable (org-cap-burn incident, note 15). 3× last night's budget (~4M tokens), ~140 ideas/night (~20 per archetype), per Ryan 2026-07-24.
- **Specialized archetype ideators** (roster §3) — each a distinct METHOD *and* a distinct NEW-market mandate, replacing the two generic lanes.
- **Slate rule (Ryan 2026-07-24): NOTHING about ideation is hard-banned — nothing.** Re-skinning the existing slate is a *weak* batch, so the slate is **de-focused / steered away from**, never prohibited: an ideator may revisit any market or strategy that has a genuinely new angle. Familiar markets (BTC 15m, commodity dailies) are capped, not banned — a deliberate minority still throws at 15m crypto (the "10%-every-time" premise). The steer shapes *emphasis*, never *scope*.
- **Mandatory conditional gate-hunt** on every non-structural kill before any DEAD verdict (note 15 taxonomy). This is where Session 3's winners came from.

## 3. ARCHETYPE ROSTER (open — nothing fixed, nothing exhaustive, nothing banned)
Archetypes are *ways of thinking* — drawn from great minds AND from strategy patterns, deliberately broad and NOT limited to quant traders. Ryan named a starting persona set (2026-07-24); more exist that he didn't state, including strategy-based (non-person) ones. The roster steers **style**, never restricts **scope** — an archetype may ideate anything.

**Persona archetypes** (each = that mind's edge-finding style, applied to prediction markets):
- **Musk** — first-principles; contrarian big bets; ignore "impossible."
- **Jobs** — taste; what the crowd wants before it knows; the un-obvious market nobody built.
- **Buffett** — durable moats, patience, mispriced *certainty* at the odds' extremes (the 95¢-resolves-99% dream shape).
- **Ackman** — concentrated, high-conviction thesis; one asymmetric bet pressed hard.
- **Bezos** — long-horizon, optionality, flow obsession; own the boring pipe nobody wants.
- (+ more personas — the set is open, add as they suggest themselves.)

**Strategy archetypes** (not people): thin-capacity structural (Thorp), event-vol / scheduled discontinuity, weak-signal stack (RenTec), be-the-house where not adversely selected, venue-mechanics / new-listing sloppiness, information-channel nobody reads, reflexivity (Soros).

Each archetype: bans nothing, steered onto NEW ground, slate de-focused (re-skinning = weak batch, not illegal). Per §4 they compete on real-money-per-token; none is pre-flagged for death.

## 4. ARCHETYPE LIFECYCLE (Ryan-ordered 2026-07-24)
- **Judged by REAL money, not paper.** An archetype earns its continued token spend by producing ideas that make money **proven in nestor** (deployed, live) — NOT ideas we *think* will work, not backtest/paper winners. Paper-survival is necessary but never sufficient to grade an archetype.
- **Cull = token-ROI.** If an archetype (e.g. Soros) never yields a nestor-proven winner, stop spinning it up. Tokens are finite; a persistently barren archetype is dead weight.
- **NO killing yet.** We have not implemented far enough — no ideas have reached nestor deployment, so there is no real-money signal to grade archetypes on. **For now every archetype lives; we only ACCRUE a per-archetype track record.** The kill mechanism activates only once ideas are actually deploying to nestor and live P&L exists. Building premature kill-logic is forbidden.
- **Archetypes are born, live, and die by this same law** — new archetypes can be added the moment a door suggests one; they compete on the same real-money-per-token basis.

## 5. ATHENA CAPTURE EXPANSION (data feeds the ideas — but DEMAND-LED)
Session 3 won partly on data breadth. We expand athena's captures — but **capture follows demonstrated demand from the ideation run, never speculation, and never a reflex back to the slate.** The archetype run surfaces which NEW markets it couldn't kill ideas against for lack of data; *those* become captures.

Standing candidate (verification, NOT slate-deepening): live commodity-daily book/quote capture to VERIFY the unproven maker survivors (A6/A3/S7, notes 28-31) — CONDITIONAL and un-provable without live fills. That's the one pre-identified gap, and it earns its place by verifying an *unproven* edge, not by feeding a winner.

Cut from the earlier draft: weather-book + new-listing captures pre-listed as "targets." That was reflexively backfilling known territory (weather is already a PROMISING slate strategy) — exactly the trap this note fixes. Ryan caught it. Let the run lead.

## 6. TRACKING SCAFFOLD (so archetypes can eventually be graded)
Every idea carries its **archetype tag** from birth. The ledger records: `archetype · idea · mechanism · kill-test · verdict · [gate] · → deploy-to-nestor? · → live P&L`. Per-archetype rollups accrue in a standing note so that, once nestor deployment begins, the token-ROI cull (§4) has real numbers. No idea is anonymous; no archetype's track record is guessed.

## Build order (pending confirm)
1. ~~Diagnosis + design~~ (this note). 2. Ryan confirms roster (§3) + capture targets (§5). 3. Build the archetype ideator harness (`researcher`/high, per-idea archetype tagging, gate-hunt enforced, slate-forbidden/markets-capped) + the tracking scaffold. 4. Build capture daemons in priority order. 5. Run the first archetype night at 3× budget.
