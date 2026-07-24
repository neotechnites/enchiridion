# 31 — Night Loop Rep 4 · 2026-07-24

TOP MIND close of one ideator rep (playbook note 20, steps 4-7). Consolidates Lane A (structure/venue) + Lane B (attention/weather) from `implementation/night-loop-A.md` and `night-loop-B.md`. Opus/medium, capped rep. 0 pulls, 0 sub-agents; 1 cheap on-disk confirmation (KXHIGH data absent → W2 genuinely capture-gated).

## What ran
- **Ideators ran 3 decisive on-disk kills:** S1 (settlement-identity dutch, n=54), S4 (15M-ETH new-listing first-print, n=997), W1 (weather hourly-temp longshot, n=163). All three DEAD with frozen numbers.
- **TOP MIND this rep:** applied the note-20 TRAP LIST to every non-DEAD survivor and downgraded where tripped; confirmed no on-disk data for the top survivor (W2) so it remains gated, not runnable. No cheaper decisive test than the ideators' existed for any survivor — all are pull/capture-gated or off-venue.

## Counts
17 ideas across both lanes. **8 DEAD** (S1, S4, W1 fresh on-disk; S2/S3/S5 structural-cited; R1/X1 dead-cited) + **1 structural-fact** (F0). **8 CONDITIONAL/research-door** survivors, all capped at CONDITIONAL after trap pass. **0 TRADE, 0 PROMISING, 0 holdout cleared.** Two lanes converge on the same doctrine: the on-disk-testable fresh channels close with numbers; every surviving door is gated behind a pull or a capture loop.

## Consolidated ledger

| id | idea | verdict (post-trap) | frozen number / gate | trap flagged |
|----|------|--------------------|----------------------|--------------|
| S1 | Range-bucket↔15M settlement-identity dutch | **DEAD (structural)** | fresh(≤5s) max +0.63c, 0/54 opps ≥2c; fat dutch all stale 6-30s | stale-quote artifact (confirmed by built-in fresh/stale placebo) |
| S2 | Index daily↔hourly settlement dutch | **DEAD-cited** | over-round 1.180 < spread+~12×ceil-fee | — |
| S3 | 15M↔daily cross-horizon dominance | **DEAD-cited** | coherent within fees when fresh (=S1 class) | stale-quote |
| S4 | 15M-ETH new-listing first-print | **DEAD-as-edge** | n=997, calib err ≤+1.7pp; FOLLOW −0.34c, FADE −2.65c | [60,80) +6pp flicker one-sided n=68 → forking-paths noise, not promoted |
| S5 | Min-tick-vs-fee-floor being-house | **DEAD-cited (cost fact)** | quoted inside fee floor; taker-only adverse | — |
| S6 | Kalshi maker-rebate/LP program | **CONDITIONAL → SURFACE to Ryan** | none on disk; Ryan-only inquiry | venue door, unwalkable by agent |
| S7 | Thin commodity-daily families (Platinum/Copper/Cocoa/Wheat) | **CONDITIONAL-research** | 1 market-list pull <15min → cwing_analyze | **correlated-sample:** new families co-move w/ existing 5 wings → "independent bets" capacity may be illusory; gate must check wing-residual corr, not just count |
| S8 | Being-house on Poly-US crypto ladder (maker fee 0) | **CONDITIONAL-research** | needs Poly fill/trade tape (off-disk) | off-venue, unfillable-until-proven |
| S9 | KBT 15M first-quote sloppiness | **CONDITIONAL (prior lowered by S4)** | first-snap-vs-settle n≥60 live book | one-event/thin (n~19 now); S4 powered-down the analog |
| W1 | Weather hourly-temp longshot (KXTEMPNYCH) | **DEAD (structural)** | tail 2-10c n=163 realY 0.0%, sellYES +1.46c; 1421/1440 degenerate 0/1 book | fee-noise deep-tail-fair (=rep27-A5); no tradeable pre-settle window |
| W2 | Weather daily-HIGH monotone-lock / METAR-lag (KXHIGH*) | **CONDITIONAL-research (top survivor)** | **no KXHIGH data on disk (confirmed)**; gate = frac locked buckets buyable ≤98c at METAR-post snap, n≥60 | **stale-quote (critical):** W1 proved sibling KXTEMPNYCH book is 99% degenerate 0/1 mid — if KXHIGH is likewise, "locked-but-buyable" = unfillable artifact; gate MUST require a fresh two-sided quote, not a degenerate mid. Also **one-event** risk: few locked buckets/day |
| W3 | Cross-city heat-dome correlation | **CONDITIONAL (capacity fact, not edge)** | pairwise corr of daily-high residuals, obs 5 cities Apr-Jul | **correlated-sample by construction** — it *reduces* independence of a multi-city wing basket; a caveat on S7/wing capacity, never a standalone edge |
| X2 | Weather-heat × commodity-wing cross-condition | **CONDITIONAL (low prior)** | split cwing EV by city heat-anomaly vs L5-Monday + unconditional base + placebo | **regime-fake:** must beat unconditional baseline & L5 marginal (D8 failed same test); condition likely collapses to I1 |
| N1 | Netflix/entertainment Top-10 stale-list | **RESEARCH-DOOR** | 1 pull, rank-vs-price on settle week | **one-event:** weekly settle = few independent events → PROMISING-at-best even if it prints |
| X1 | "Sell wings while different family calm" | **DEAD-cited** | note 30: collapses to I1; D7 0 off-diagonal | pooled cross-asset / regime-fake |
| R1 | Weather taker-flow reflexivity | **DEAD-cited** | reflexivity graveyard B7/I3/D4 + underpowered | one-event + reflexivity-graveyard |
| F0 | KXTEMPNYCH hourly reading efficient | **structural fact** | n=1421, realY 55.7% @ mid 0.500 | — (supports W2's "the edge, if any, is the all-day HIGH ladder") |

## Ranked survivors (small-bankroll first)

1. **S7 — thin commodity-daily family scout.** *Mechanism:* thinner daily ladders (Platinum/Copper/Cocoa/Wheat wings) carry sloppier uninformed wing flow; if they exist and are thinner than the current 5, they extend live cwing wing-sell (A6/L5) capacity with more bets. *Frozen:* unrun; one <15min market-list pull → `cwing_pull.py`/`cwing_analyze.py`. *Verdict:* **CONDITIONAL-research.** Cheapest forward thread and directly small-bankroll-capacity-aligned. **Trap caveat:** kill-gate must add a wing-residual correlation check vs the existing 5 (W3 shows synoptic/commodity co-movement) — raw count of new wings overstates independent capacity.

2. **W2 — weather daily-HIGH monotone-lock (KXHIGH\*).** *Mechanism:* daily high settles on the cumulative running max (monotone non-decreasing); once observed METAR max crosses a bucket floor the YES is mathematically LOCKED all day, yet the thin all-day weather book has few watchers refreshing hourly METARs. Orthogonal to every crypto/commodity edge. *Frozen:* **no number — confirmed no KXHIGH data on disk this rep;** gate = fraction of already-locked buckets still buyable ≤98c at the next METAR-post snap, n≥60. *Verdict:* **CONDITIONAL-research.** **Trap caveat (critical, from W1):** the sibling KXTEMPNYCH book is 99% degenerate (1421/1440 markets 0-bid/1-ask, mid 0.500) until the near-certain last trade. If KXHIGH is the same, "locked-but-buyable" prints are unfillable stale-quote artifacts (the exact class that killed S1). The capture gate MUST record a fresh two-sided quote, not a degenerate mid, or the door reduces to W1.

3. **S6 — Kalshi maker-rebate / LP program.** *Mechanism:* a rebate could flip uninformed thin-book maker spread from adverse-selection trap → being-house edge. *Verdict:* **CONDITIONAL → SURFACE to Ryan** (Ryan-only inquiry, unwalkable by agent). The one genuinely un-walked venue door.

4. **S9 — KBT 15M first-quote sloppiness.** CONDITIONAL, **prior lowered** by S4's powered n=997 first-print-efficient result. Needs live-book capture n≥60.

5. **N1 — Netflix Top-10 stale-list.** RESEARCH-DOOR, 1 pull. **One-event capped at PROMISING** even on success (weekly settle → few independent events).

6. **S8 / X2 / W3** — off-disk or conditioning-baseline-gated; low priority. W3 is a capacity caveat, not an edge.

## Pipeline notes
- **Two capture/pull builds unblock four survivors:** (a) a KXHIGH intraday best-quote capture keyed to METAR post-times (~:51/:15) unblocks **W2** — add it to the standing "one book-capture unblocks several survivors" queue alongside A3/A5/A8, and record fresh two-sided quotes so it can't degenerate into W1. (b) One <15min market-list pull unblocks **S7**.
- **Doctrine reinforced:** every on-disk-testable fresh channel this rep (settlement-identity dutch, 15M first-print, weather hourly-temp) closed with numbers. The remaining slate is entirely gated behind a pull or a capture loop — the loop is now capture-bound, not idea-bound.
- **Two recurring traps did the downgrading:** stale-quote artifact (S1, W1, and the live risk on W2) and correlated-sample illusion (S7 capacity, W3 by construction). Ranking survivors below TRADE is correct — none cleared a holdout.
