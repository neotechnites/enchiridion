# 27 — Night Ideator Rep (2026-07-23)

Closer: TOP MIND, Opus/medium. Enchiridion note 20 step 4–7. This rep was the machine test (notes 22/24), not an exhaustion run.

## What ran
- **Lane A (Structure & Venue)** — 8 ideas, 4 inline harness passes over on-disk `cwing_obs_*` (5 commodity-daily families, 5,856 rungs) + `idx_obs_*` (KXINX/NDX daily brackets). No pulls, no sub-agents. Ledger: `implementation/night-rep-A.md`.
- **Lane B (Attention & Information)** — 8 ideas, 5 run inline over hourly BTC/ETH ladder + commodity wing data (`scripts/laneB_attn.py`), 3 graveyard doors re-walked (not re-run). Ledger: `implementation/night-rep-B.md`.
- **Closer trap pass** — applied note-20 step-6 trap list to every non-DEAD survivor; ran one decisive recount on the top survivor (A6) collapsing to independent events + drop-one-event + per-family. Harness: scratch `a6_trap.py` over `cwing_obs_*`.

## Totals
- **16 ideas. 12 DEAD (structural, with numbers). 4 CONDITIONAL. 0 TRADE, 0 cleared a holdout.**
- Zero graveyard re-tests where cited as settled. Two fresh structural doors (commodity-daily monotonicity; index bracket partition dutch) both came back DEAD with numbers.
- Nothing here clears the "verified enough it won't reverse" bar. Verdicts capped at **CONDITIONAL / PROMISING**.

## Consolidated ledger

| id | idea | result | verdict (post-trap) | key numbers |
|----|------|--------|---------------------|-------------|
| A1 | Commodity intra-ladder monotonicity box | DEAD | structural | 3–7 fresh viols/fam, ~8¢/6wk; placebo lights → stale-print mirage |
| A2 | Index bracket partition sum-to-1 dutch | DEAD | structural/artifact | INX mid-sum 1.18 over-round; NDX under-round events thin (nq 1–9) = incomplete capture |
| A3 | Cross-family wing correlation | DEAD as edge | structural fact | multi-loss 3/38 ≈ indep 0.093 → ~5 independent bets (capacity fact, kept) |
| A4 | Be-the-house in commodity meat (25–75¢) | DEAD | structural | 60.5% rungs fresh (liquid) but buy-NO EV −4.6¢ (adverse) |
| A5 | Deep-tail (1–3¢) sell-longshot extension | DEAD | structural | realized YES 0.000 vs ~0.02; +0.46¢ = fee-noise |
| **A6** | **Maker wing-SELL on thin commodity dailies** | **CONDITIONAL** | **CONDITIONAL, narrowed by trap** | 3–10¢ band robust; 10–25¢ band fails correlated-sample (see below) |
| A7 | Far-rung staleness as edge | DEAD | no-counterparty | median last-print 1200–5000s but tails FAIR |
| A8 | New-rung mid-session sloppiness | CONDITIONAL-research | untestable from disk | needs per-rung listing timestamps; queue a watcher |
| B1 | Mempool congestion gate | DEAD | structural | feed degenerate — median fee=1 sat/vB, no dispersion |
| B2 | Deribit DVOL gate | DEAD-mechanism | inverted | LOW −0.90 / HIGH +2.59 (opposite of predicted); join n~520 |
| B3 | Cross-commodity tail contagion | DEAD | structural | lift only within same underlying (WTI\|Brent +27.8pp n=5); n≤6 unpowered |
| B4 | Funding-rate forced-flow | DEAD | graveyard | pooled −2.49¢ |
| B5 | Kimchi-premium ROC | DEAD | graveyard | inverted |
| **B6** | **Momentum "X-while-A" calm gate** | **CONDITIONAL** | **CONDITIONAL / DECAY-BENCH** | pooled calm +1.21 / active −1.21; fails honest split |
| B7 | Contract-price velocity reflexivity | DEAD | graveyard | 100% regress-to-50, corr −0.008 |
| **B9** | **PAXG risk-off gate on BTC wings** | **CONDITIONAL** | **CONDITIONAL, collinear w/ B6** | paxg-down +2.21 / up −0.64; ETH-absent |

## Ranked survivors (small-bankroll capacity first)

### 1. A6 — Maker wing-SELL on thin commodity dailies · CONDITIONAL
**Mechanism.** The one genuine crack in the taker-only doctrine, which was proven only on *informed* crypto momentum books. Commodity-daily wing buyers are structurally uninformed lottery flow; their systematic overpay is the edge, and a resting maker ask captures it fee-exempt and without the 1¢ taker slip. Adverse selection is absent because informed players don't quote these books.

**Frozen kill numbers (fresh prints age≤300s, 5 families pooled):**
- **3–10¢ YES band: n=133 rungs across 87 independent events. Rung-mean buy-NO EV +2.06¢; event-weighted (one obs/market) +2.42¢; drop-top-event +1.97¢.** Survives correlated-sample and one-event traps. Realized YES 0.023 vs priced ~0.07.
- **10–25¢ YES band: n=146 rungs, 111 events. Rung-mean +1.50¢ but event-weighted collapses to +0.27¢ (noise). FAILS the correlated-sample trap** — the apparent edge is clustered rungs inside a few events.
- **Cross-family dispersion (pooled-cross-asset trap):** edge is NOT uniform. NatGas is *negative* in both bands (−1.06¢ / −1.60¢). Precious metals carry it (Gold +5.28/+7.77, Silver +3.92/+7.86); crude modest-positive in 3–10¢, negative in 10–25¢.

**Post-trap verdict: CONDITIONAL, narrowed.** The bankable-looking region is the **3–10¢ deep-wing band on metals + crude (exclude NatGas)**. The 10–25¢ band is largely a correlated-sample illusion and should not be counted. Blocker remains **fill realization**, which is not disk-testable — needs a 2–3 day commodity-daily book-capture / maker-fill probe (note-15 live-fill doctrine). Capacity is small (thin dailies) — well-matched to a small bankroll. **Does not clear a holdout; cap at CONDITIONAL.**

### 2. A8 — New-rung mid-session staleness · CONDITIONAL-research
**Mechanism.** Ladder auto-extends when spot drifts; a freshly-listed far rung may inherit a stale/default quote before MMs arrive. **Untestable from disk** (needs per-rung listing timestamps). Trips no numeric trap because there are no numbers. Action: add a live watcher logging new-rung first-quote vs fair. Cheap to bolt onto a future capture loop.

### 3. B6 — Momentum "X-while-A" calm gate · CONDITIONAL / DECAY-BENCH
**Mechanism.** Sell hourly wings only when trailing |ret60| is calm; wing sellers don't condition on live momentum.
**Frozen numbers:** pooled BTC calm +1.21¢ (n=720, win 90.6%) / active −1.21¢ (n=719); clears hash-placebo ±0.84¢.
**Traps tripped: regime-fake + one-era + pooled-cross-asset.** BTC-early inverts (calm +0.05 / active +1.68); only BTC-late carries it (calm +2.37 / active −0.99). ETH absent (calm +0.30 / active +0.19) under a ±3.5¢ per-split placebo floor. Not tradeable. Re-test only on the deeper WTIH/commodity wing base with a same-asset momentum definition.

### 4. B9 — PAXG risk-off gate on BTC wings · CONDITIONAL (bench with B6)
**Mechanism.** Gold rally = risk-off = BTC downside tail; crypto wings don't price the gold feed.
**Frozen numbers:** paxg-down +2.21¢ / paxg-up −0.64¢ pooled BTC.
**Traps tripped: correlated-sample (same slice as B6, a PAXG move ⇄ a BTC move — not independent) + pooled-cross-asset (ETH-absent).** Not independent of B6. Bench together on the commodity base.

### Structural fact retained (not an edge)
**A3:** commodity wings behave as ~5 independent bets (multi-loss-day 3/38 ≈ independence 0.093). Lets the live cwing engine diversify across families rather than cap at one — supports A6 capacity, doesn't add edge. B3 corroborates the cluster-cap: WTI≈Brent = one bet, Gold≈Silver = one bet, NatGas independent.

## Pipeline notes (this rep was the machine test — notes 22/24)
- **The trap pass earned its cost.** A6 read as a clean pooled +2.13/+1.55¢ in Lane A's ledger. One cheap event-collapse recount showed the 10–25¢ band is a correlated-sample artifact (+1.50¢ → +0.27¢ event-weighted) and that NatGas is negative in both bands. Lesson: **every pooled-rung wing number must be reported event-weighted, not rung-weighted, and per-family, before it earns a CONDITIONAL.** Fold this into the harness output by default.
- **Placebo calibration held across both lanes.** Lane B established that at n≈1–2k a pure hash split spreads ±0.8¢ (BTC) to ±3.5¢ (ETH n≈960); sub-1¢ "edges" on those samples are noise. This correctly killed B6/B9 on their own splits. Adopt the per-split placebo floor as a standing gate.
- **Fresh-print discipline worked.** Age≤300s filter turned A1's 316–442 apparent monotonicity violations into 3–7 real ones (stale-print mirage), and the placebo that lit on fake cross-event pairs confirmed the test discriminates. Keep the stale-vs-fresh placebo as the default monotonicity/dutch guard.
- **Graveyard hygiene good.** 3 Lane-B doors (funding-clock, kimchi-ROC, reflexivity) were cited from note-13 numbers, not re-run — correct economy. No wasted re-tests.
- **Nit:** Lane B's summary line says "5 DEAD" but the table lists 6 (B1,B2,B3,B4,B5,B7). Reconciled here to 6.
- **Cost:** ~5 inline harness passes + 1 closer recount, zero pulls, zero sub-agents. Cap respected (16 ideas, target ~8–10/lane).
- **Standing gap:** two of the best-looking ideas (A6 maker-fill, A8 new-rung staleness) are both blocked on the same missing capability — **live commodity-daily book/quote capture**. That single probe unblocks both. Highest-leverage next build.
