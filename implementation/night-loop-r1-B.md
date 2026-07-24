# Night Loop — Rep 1, Lane B (Attention & Information)

- **UTC:** 2026-07-24 05:48
- **Rep:** 1 · **Lane:** B (attention/information, cross-condition, reflexivity)
- **Runner:** Opus/medium, capped overnight rep (~8 ideas). Zero pulls, zero sub-agents. 3 inline harness passes over on-disk hourly BTC/ETH wing obs (`hourly/*_obs.jsonl`, n=48k) + commodity wing obs (`cwing_obs_*`).
- **Prior tonight:** note 27 (rep 0 A+B). This rep walks FRESH doors rep-0 B did not: rep-0 covered mempool/DVOL/funding/kimchi/momentum-calm(same-asset)/velocity-reflexivity/PAXG. I do NOT repeat those.
- **Method:** wing-fade = BUY NO on 3–25¢ YES rungs, entry (1−px)·100+1¢, fee 0.07·P(1−P), one obs/market, dt=3300 fresh only. Each gate: low/high tercile EV + hash placebo + honest early/late era split. Predicted-direction-or-dead. Placebo floor (note 27 calibration): BTC ±0.84¢, ETH ±3.5¢ at these n.

> **Resume note (relaunch 2026-07-24 ~06:1x UTC):** I1–I8 + survivors below are the completed 05:48 rep, preserved intact. On relaunch I walked THREE fresh attention/calendar/clock doors (F1 weekend, F2 bid-ask spread, F3 equity-hours cross-condition) that neither rep-0 (note 27) nor the 05:48 rep covered — appended as the "RELAUNCH continuation" section at the bottom. Harness `scripts/laneB_r1_fresh.py`. Net: 0 new independent edges — all three collapse onto the already-known low-realized-vol / calm-clock factor (F1 proven collinear on disk).

## Ledger

| id | idea | mechanism (who's wrong) | kill-test | n | win% | EV-net | verdict | files |
|----|------|------------------------|-----------|---|------|--------|---------|-------|
| I1 | **rv24 realized-vol regime gate on wing-fade** | Wing buyers anchor on headline/implied vol; when trailing 24h *realized* vol has been quietly low, the wing is stale-rich — a slow attention channel nobody reprices intraday (distinct from DVOL/implied=dead-inverted, and from instantaneous \|ret60\|=B6) | low vs high rv24 tercile buy-NO EV + placebo + era split | BTC 720 (LOW) | 92.1 | **+2.30¢** | **CONDITIONAL (+gate: rv24 low tercile; BTC only)** | `scripts/laneB_r1.py` |
| I2 | Cross-asset momentum "X-while-A" (ETH wing gated on \|btc_ret60\|, BTC on \|eth_ret60\|) | Correlation nobody prices at the wing: the OTHER coin's move foreshadows this coin's coming wing vol → sell only when the anchor coin is calm | cross-coin \|ret60\| tercile + placebo | BTC 720 / ETH 321 | 90/92 | LOW +0.68 / HIGH +1.04 (BTC) | **DEAD** (predicted-direction fail — calm→worse, both coins; magnitude ⊂ placebo) | `scripts/laneB_r1.py` |
| I3 | Recent-15m streak magnitude reflexivity (\|streak15\|) | After a 15m settlement streak, chasers pile into wings → overpriced → buy-NO richer at high \|streak\| | \|streak15\| low/high tercile | BTC 960 / ETH 454 | 88/91 | BTC +0.67 vs +0.61 (flat); ETH HIGH −0.81 | **DEAD** (no separation BTC; wrong-dir + ⊂placebo ETH) | `scripts/laneB_r1.py` |
| I4 | EIA report-day calendar-attention gate (crude Wed, natgas Thu) | Uninformed commodity-wing sellers ignore the storage-report calendar; report-day vol → wing buyers win → sell wings only on non-report days | report vs non-report buy-NO EV, y3 3–10¢ | crude 24/91 · natgas 6/16 | — | crude REPORT +3.44 vs NON +0.71; natgas REPORT +5.39 vs NON −0.29 | **DEAD** (predicted-direction fail — report BETTER not worse; n⊂noise, day-of-week scatter ±4¢) | `cwing_obs_*` inline |
| I5 | Directional streak-chase reflexivity, per-tail (streak15 sign × strike-vs-spot) | Chasers overbuy the wing ALIGNED with recent momentum (up-streak→upper tail) → that specific wing overpriced → buy-NO on the chased wing wins | aligned vs anti-aligned buy-NO EV + placebo | BTC 1044/1113 · ETH 473/488 | 88/92 | BTC ALIGNED −0.05 vs ANTI +1.27; ETH +0.72 vs +0.16 | **DEAD** (BTC wrong-direction; ETH ⊂ 3.5¢ placebo) — reflexivity door re-closes, consistent w/ graveyard velocity=dead | inline |
| I6 | Poly→Kalshi crypto info-lead (cross-venue attention) | Poly daily-settle price as a feed Kalshi wing sellers don't watch | — (untestable from disk) | — | — | — | **DEAD-cited / research** — graveyard: crypto cross-venue = BASIS not dutch (BRTI≠oracle, 60s-avg≠point, strike-grid + timing offset); `poly_ladder_*` is sparse daily settle (810/791 rows, age-stamped), not intraday-alignable to hourly Kalshi | note 13 |
| I7 | Offshore-premium fade (okx_prem / bstamp_prem "wrong-ticker" attention) | Retail watches the wrong (offshore) ticker; fade contract chasing offshore-led moves | — (graveyard) | — | — | — | **DEAD-cited** — `wrongticker.py`: Coinbase/BRTI is the BETTER settlement predictor both eras; fading offshore-led = −3.3 to −6.5¢, direction-flipping | note 13 |
| I8 | IV−RV wedge gate ("vol-risk-premium feed nobody reads") | Wedge signals wings mispriced vs coming realized vol | coverage/dominance check | ~22% obs coverage | — | — | **DEAD-dominated** — cousin of rep-0 B2 DVOL (DEAD-inverted); realized side already captured by I1 survivor; not worth a run under cap | note 27 (B2) |

## Totals
8 ideas · **6 DEAD** (I2–I5 structural/predicted-direction with numbers; I6–I8 graveyard-cited/dominated) · **1 CONDITIONAL** (I1) · 0 TRADE · 0 cleared a holdout.

## Survivors (frozen kill numbers)

### I1 — rv24 realized-vol regime gate · CONDITIONAL (+gate: rv24 low tercile, BTC)
**Mechanism.** Hourly-wing buyers price off headline/implied vol and don't reprice on the *slow* trailing-24h realized-vol state. When realized vol has been quietly low, the standing wing curve is stale-rich; buy-NO on 3–25¢ rungs captures the overpricing. Distinct from the DEAD DVOL gate (implied, rep-0 B2, inverted) and the DEAD instantaneous-momentum gate (B6).

**Frozen numbers (BTC, buy-NO 3–25¢, fresh dt=3300, one obs/mkt):**
- LOW rv24 tercile (annualized ≤ **29.94**): **n=720, win 92.1%, EV +2.30¢**.
- HIGH tercile (≥38.28): n=720, EV +0.39¢. Spread **+1.91¢ > BTC placebo band 0.84¢**.
- Honest era split: LOW cell positive in BOTH halves (**+2.52¢ early / +1.82¢ late**); the HIGH cell is what decays (+2.23→−0.29). The gate's LOW-vol side is era-robust.
- **ETH version DEAD:** LOW +1.25 / HIGH −1.35 but LOW cell sign-FLIPS across eras (+4.03 early → −1.44 late) and the whole spread sits inside ETH's 3.5¢ placebo band. BTC-only.

**Blocker before it earns more than CONDITIONAL: collinearity with the calm-clock gate (already in slate system ①/④).** Low rv24 likely overlaps calm-clock/weekend hours (both = low realized vol). The gate must be re-tested *conditioned inside* the 22–12 UTC calm window to show it adds edge beyond the clock — that is its own overfit bar (note 07). If it merely re-selects calm hours, it is a cleaner parameterization of an existing edge, not a new one. Does NOT clear a holdout. Cap at CONDITIONAL.

## Doctrine touchpoints
- Reflexivity door (I3 magnitude, I5 directional-per-tail) re-closes with fresh numbers on the crypto hourly base — consistent with graveyard velocity-reflexivity=DEAD (corr −0.008, 100% regress-to-50). Two independent parameterizations, both dead.
- "X-while-A" cross-asset (I2) fails predicted direction — the other coin's move does NOT foreshadow exploitable wing vol; if anything mildly inverted, ⊂ placebo. Cross-asset lead-lag graveyard (1s) corroborates.
- Placebo calibration held: BTC sub-0.84¢ and ETH sub-3.5¢ "edges" correctly killed as noise (I2/I3/I5 ETH).
- Cost: 3 inline passes, 0 pulls, 0 sub-agents. Cap respected.

---

## RELAUNCH continuation — fresh attention/calendar/clock doors (2026-07-24)

Three doors neither rep-0 (note 27: mempool/DVOL/contagion/funding/kimchi/momentum-calm/reflexivity/PAXG) nor the 05:48 rep (rv24/cross-momentum/streak/EIA/directional-streak/Poly-lead/offshore/IV-RV) walked. Same wing-fade base (BUY NO px 3–25¢, entry (1−px)·100+1¢, fee 7·px(1−px)¢, dt=3300 fresh, one obs/mkt). Harness `scripts/laneB_r1_fresh.py`. Placebo floors held: BTC ±0.84¢, ETH ±3.5¢.

| id | idea | mechanism (who's wrong) | kill-test | n | win% | EV-net | verdict | files |
|----|------|------------------------|-----------|---|------|--------|---------|-------|
| F1 | **Weekend attention gate** — sell crypto wings on Sat/Sun (UTC) when informed/institutional desks are OFF, leaving a retail-only regime → wings stale-rich | weekend vs weekday buyNO EV + placebo + era split + **rv24 collinearity cross-tab** | BTC 800 wknd / 1357 wk | 91.8 | WEEKEND **+1.61** vs weekday +0.06 (spread +1.55 > 0.84 placebo) | **CONDITIONAL-collinear (SUBSUMED by I1 rv24; not a new door)** — disk cross-tab: weekend-fraction is **0.70 inside LOW-rv24 tercile vs 0.12 inside HIGH**, and weekend EV inside HIGH-rv24 = **−3.88**. Weekend IS the low-realized-vol regime wearing a calendar mask. Also era-fragile (early wknd +0.51<wk +1.51 INVERTED; only late era carries it +2.72 vs −1.38 = regime-fake trap). ETH weekend −0.46 wrong-dir. Adds nothing over the existing I1/calm-clock survivor. | `scripts/laneB_r1_fresh.py` |
| F2 | **Bid-ask spread as attention/thinness gate** — wide-spread wings = less-watched/abandoned book = stale-rich | spread-width tercile buyNO EV + placebo, both coins | BTC 775 tight / 1169 wide · ETH | 88.9 | BTC WIDE +1.27 vs TIGHT +0.12 (spread +1.15); **ETH +0.06** | **DEAD-as-stated** — BTC spread +1.15¢ barely clears the 0.84¢ placebo and is price/depth-confounded (wider spread = deeper/cheaper rung = mechanically richer wing already captured by the engine, cf. A1 thinness). ETH null (+0.06). Not cross-asset, confounded → no independent attention edge. | `scripts/laneB_r1_fresh.py` |
| F3 | **Equity-hours cross-condition ("wing-fade while TradFi awake")** — sell crypto wings only when US cash equities are CLOSED; during 13:30–20:00 UTC macro desks are watching and reprice the wing | eq-hours vs non-eq buyNO EV + placebo + era split, both coins | BTC 209 eq / 1948 non | 83.3 | BTC EQ-HOURS **−0.70** vs non-eq +0.78 (spread −1.48, predicted direction ✓) | **CONDITIONAL-collinear with calm-clock (BTC-only)** — the ONE fresh door that is era-robust: eq-hours adverse in BOTH halves (early −0.21 vs +1.28; late −1.17 vs +0.28). But eq-hours (13:30–20:00 UTC) is the exact inverse-image of the slate calm-clock window (22–12 UTC) — this re-derives the calm-clock exclusion from an independent (attention) direction rather than being a new edge. ETH null (eq +0.49 vs +0.42). Practical value = independent confirmation of the calm-clock, not incremental EV. | `scripts/laneB_r1_fresh.py` |

### Continuation tally
3 fresh doors · **1 DEAD-as-stated** (F2, confounded + not cross-asset) · **2 CONDITIONAL-collinear** (F1 subsumed-by-rv24, F3 re-skins calm-clock) · **0 new independent edges** · 0 TRADE, 0 holdout.

### Frozen kill numbers (continuation survivors)
- **F1 weekend — collinearity kill (the useful number):** weekend-fraction 0.70 (LOW-rv24 tercile) vs 0.12 (HIGH-rv24); weekend EV inside HIGH-rv24 = −3.88¢. Weekend ⊂ low-rv24 → the calendar effect is the I1 realized-vol regime, not a separate attention channel. Do not weight independently of I1.
- **F3 equity-hours — cleanest fresh number:** BTC eq-hours (13:30–20:00 UTC) buyNO EV **−0.70¢ (n=209)** vs non-eq **+0.78¢ (n=1948)**, era-consistent both halves. Reads as an independent (attention-axis) confirmation of the slate calm-clock gate: US-active hours are where crypto wings are NOT rich.

### Continuation doctrine touchpoint
**The wing-richness attention channel has ONE underlying factor.** Weekend (F1), equity-hours (F3), and the existing rv24 survivor (I1) / calm-clock (slate ①④) are all proxies for the same thing: crypto wings are rich only in the quiet, low-realized-vol, TradFi-asleep regime. Three independent parameterizations (calendar / clock / realized-vol statistic) converge on it and none adds orthogonal EV. This is a consolidation result, not a new edge — it argues the slate should keep ONE clean gate (rv24, already the sharpest, per note 28) rather than stacking collinear calendar/clock filters. Cost: 1 harness pass + 1 collinearity cross-tab, 0 pulls, 0 sub-agents.
