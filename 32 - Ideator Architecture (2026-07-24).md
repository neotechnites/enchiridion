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
- **Slate rule (Ryan 2026-07-24):** existing slate *strategies* are **hard-forbidden** (re-skinning them = a failed batch). Familiar *markets* (BTC 15m, commodity dailies) are **capped, not banned** — a deliberate minority still throws at 15m crypto (Ryan wants signal there, the "10%-every-time" premise), but the bulk is forced onto new venues/categories.
- **Mandatory conditional gate-hunt** on every non-structural kill before any DEAD verdict (note 15 taxonomy). This is where Session 3's winners came from.

## 3. ARCHETYPE ROSTER (PROPOSED — confirm/edit before build)
Each archetype ideator gets: its method, its new-market mandate, the door it owns. All forbidden from re-skinning slate strategies.

| # | Archetype | Method | New-market mandate |
|---|---|---|---|
| 1 | **Thorp** (thin-capacity) | structural mispricing a small bankroll can *own* | obscure/new Kalshi categories too small for size — NOT the slate families |
| 2 | **Event-vol desk** | scheduled discontinuities the ladder underprices | macro releases, earnings gaps, expiries (proven door — event wings) |
| 3 | **RenTec** (weak-signal stack) | many individually-weak signals combined on one family | a NEW domain; may take the deliberate 15m-crypto slice |
| 4 | **Benter** (feature model) | exhaustive public-feature model on one rich-but-unmodeled domain | a sport / weather / politics market, not crypto |
| 5 | **House / Jane St** | be the quoter/RFQ where nobody quotes — *only where not adversely selected* (taker-only doctrine constrains this; find the narrow non-pickoff door) | maker-fee-exempt / new-listing / RFQ surfaces |
| 6 | **Venue-mechanics** | promos, incentive/LP programs, new-listing sloppiness | venue-specific, off-slate |
| 7 | **Information-channel** | a feed/source nobody reads at the moment it updates | "pizzas to the Whitehouse"-class sources |
| 8 | **Soros** (reflexivity) | market moves that change the settled outcome | self-fulfilling settings — **flagged: highest no-yield risk → first token-ROI cull candidate (§4)** |

## 4. ARCHETYPE LIFECYCLE (Ryan-ordered 2026-07-24)
- **Judged by REAL money, not paper.** An archetype earns its continued token spend by producing ideas that make money **proven in nestor** (deployed, live) — NOT ideas we *think* will work, not backtest/paper winners. Paper-survival is necessary but never sufficient to grade an archetype.
- **Cull = token-ROI.** If an archetype (e.g. Soros) never yields a nestor-proven winner, stop spinning it up. Tokens are finite; a persistently barren archetype is dead weight.
- **NO killing yet.** We have not implemented far enough — no ideas have reached nestor deployment, so there is no real-money signal to grade archetypes on. **For now every archetype lives; we only ACCRUE a per-archetype track record.** The kill mechanism activates only once ideas are actually deploying to nestor and live P&L exists. Building premature kill-logic is forbidden.
- **Archetypes are born, live, and die by this same law** — new archetypes can be added the moment a door suggests one; they compete on the same real-money-per-token basis.

## 5. ATHENA CAPTURE EXPANSION (data feeds the ideas)
Last night went capture-bound; these unblock multiple survivors and give the archetypes fresh on-disk doors. Model each on the named existing script; deploy under the launchd machine set (note 18) only after Ryan confirms targets.

1. **Live commodity-daily book/quote capture** — a `capture_kbt.py`-style L2 daemon for KXBRENTD/GOLDD/SILVERD/NATGASD (+ KXWTIH hourly). Unblocks A6 maker wing-sell, A3 staleness, S7 capacity (notes 28-31). Highest priority.
2. **KXHIGH / KXTEMP*H weather-book capture** — fresh two-sided quotes (note 31: W1 proved the sibling book is ~99% degenerate 0/1, so capture must record live quotes or it collapses).
3. **New-listing sloppiness monitor** — a series-catalog watcher (like `sweep_catalog_2026.py`) that flags NEW Kalshi listings within hours of going live, feeding archetypes 5/6.
(Others to be prioritized from the archetype outputs — capture follows demonstrated demand, not speculation.)

## 6. TRACKING SCAFFOLD (so archetypes can eventually be graded)
Every idea carries its **archetype tag** from birth. The ledger records: `archetype · idea · mechanism · kill-test · verdict · [gate] · → deploy-to-nestor? · → live P&L`. Per-archetype rollups accrue in a standing note so that, once nestor deployment begins, the token-ROI cull (§4) has real numbers. No idea is anonymous; no archetype's track record is guessed.

## Build order (pending confirm)
1. ~~Diagnosis + design~~ (this note). 2. Ryan confirms roster (§3) + capture targets (§5). 3. Build the archetype ideator harness (`researcher`/high, per-idea archetype tagging, gate-hunt enforced, slate-forbidden/markets-capped) + the tracking scaffold. 4. Build capture daemons in priority order. 5. Run the first archetype night at 3× budget.
