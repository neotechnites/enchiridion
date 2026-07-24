# Lane RENTEC — burst 1 (2026-07-24)

Archetype: RENTEC (weak-signal stack on ONE family). Native habitat: 15m crypto.
**Genuinely-new door:** L2 order-book **book-shape** signals from `kbt_books_*.jsonl` — 100ms full-book
snaps captured Jul-24 (03:45→22:00 UTC, 5 coins, ~237 live-book markets). This data did NOT exist during
the original ~60 15M tests (note 03), which is why every "conditioning variable" they killed (hour, day,
vol, funding, VIX, whipsaw, freshness, thinness, whale/minnow) was a PRICE/COIN feature — never book depth.
The Mesh flagged order book as "highest-value untested data" repeatedly; this burst opens it.

Snap schema: `[ts, yes_best_bid, no_best_bid, coin_price, ydep5, ndep5, ytot, ntot]` (depths = resting BID
contracts; `capture_kbt.py`). Near-50 band = yes_mid∈[0.40,0.60). One obs/market. Fee = 0.07·p·(1−p).

## Flagship: R1 — resting dollar-depth imbalance → direction (near-50, T−60s)
**Rule (plain English):** In a 15m crypto market that's a near-coinflip on PRICE (~40–60¢) with ~1 min left,
buy the side that has **more resting bid depth** (dollar-weighted). It wins ~62%.
**Mechanism / fish:** the book price near 50 is set by the coin's position (a genuine tossup); informed
money quietly stacks **resting bids** on the side it expects, and that depth predicts the settle beyond price.
The fish = whoever prices these off coin-position alone and ignores the depth ledger. Retail's own cheap-side
bids LOSE (see disambiguation), so this is informed flow, not lottery flow.
**Numbers:** n=106, **win 62.3%, netEV +12.7c**, avgEntry 0.482 (contract-depth variant 60.4%/+11.8c).
- Price-controlled 0.45–0.55 (near-flat price): imbalance still separates **+20.6pp** → it's DEPTH, not price.
- Coin-position control (coin within 0.05% of strike = true tossups): **win 66.7%, +28.1c, n=39**.
- Fill-delay robust: instant / +5s / +10s fill all = +11.8c → NOT a stale-quote / latency artifact.
- Within-day split: early +13.2c / late +12.1c (spans 18h, multiple sessions).
- Per coin (4/5 positive): btc +15.3 · eth +21.9 · sol +31.6 · xrp +4.9 · **doge −15.9** (lone reverser, thinnest).
- **Disambiguation (kills the obvious artifact):** buy-CHEAP-side near-50 = 39.6% win **−4.7c** (loses);
  buy-BOOK-HEAVY = +11.8c; heavy==cheap only 45% → depth ≠ cheap-side proxy. Contract-count artifact DEAD.
**Verdict: PROMISING (window-limited).** Only blocker = single Jul-24 capture window + correlated
cross-asset sampling → NOT cross-era verified (note 32 window rule). Small-bankroll suited (thin 15m books,
median 300-contract transactable depth). **Next gate (demand-led capture, note 32 §5):** let `capture_kbt.py`
accumulate ≥2 wks → per-coin BTC-alone **block-holdout by day** + fillable-depth filter (p25 depth=0, ~¼
unfillable). Cannot promote to TRADE on one day.
Files: `/tmp/rentec_deep.py`, `/tmp/rentec_trade.py`, `/tmp/rentec_cheap.py`, `/tmp/rentec_split.py`.

## R7 — same depth signal at market OPEN (+45s) → the built-in PLACEBO
**Numbers:** n=102, netEV **−11.4c** (win 48.0%). The identical depth-imbalance rule applied at the OPEN
(thin, immature book) is NEGATIVE. **Verdict: DEAD (structural placebo).** This is R1's validation: the signal
is a *mature-book* phenomenon (informed flow accumulates over the window), not a mechanical/definitional
artifact — an artifact would fire at open too. Confirms note-30's stale-open caution from the other side.
File: `/tmp/rentec_stack.py`.

## R4 — TIGHT-SPREAD gate on R1 (conditioning variable)
**Rule:** take R1 only when the best spread ≤3c (active/competitive book).
**Numbers:** gated n=48 **win 75.0%, +44.2c** (both within-day halves 75%/75%); wide-spread >3c = 51.7%,
**−13.4c**. Per-coin honest caveat: pooled +44c is INFLATED by tiny alt cells (eth n=7, xrp n=5, doge n=4);
**BTC-alone gated = +17.2c / 56.5% at n=23** (positive, modest, small-n).
**Verdict: CONDITIONAL gate (mechanism = active book).** Halves the tradeable set, ~doubles BTC edge.
Needs capture to power per-coin. File: `/tmp/rentec_gate.py`.

## R5 — book × coin-momentum AGREEMENT gate (X-while-A)
**Rule:** R1 is much stronger when depth imbalance AGREES with the coin's last-60s drift direction.
**Numbers:** agree +23.6c (n=56) vs disagree +0.4c (n=50). **Verdict: CONDITIONAL gate.** Same mechanism
family as R4/R6 (converges on "active, informed, mature book"). File: `/tmp/rentec_stack.py`.

## R6 — quote-flicker (churn) gate on R1
**Numbers:** high-churn (≥4 distinct bid prices in window) n=13 win 100% +84c; low-churn +2.6c.
**Verdict: CONDITIONAL but n=13 — DO NOT WEIGHT.** Third convergence on the active-book mechanism; record,
don't size. File: `/tmp/rentec_stack.py`.

## R3 — top-of-book (top-5) imbalance only
**Numbers:** n=98, +4.5c (vs full-depth R1 +12.7c). **Verdict: DEAD-as-standalone / subsumed by R1.** The
edge lives in the FULL resting book, not the top — informed depth sits behind best. Diagnostic, not a trade.
File: `/tmp/rentec_stack.py`.

## R2 — depth-tilt CHANGE (Δimbalance over last 60s)
**Numbers:** n=106, win 47.2%, **−0.3c**. **Verdict: DEAD (structural, no separation).** The static LEVEL
(R1) carries everything; the flow-acceleration derivative adds nothing. File: `/tmp/rentec_stack.py`.

## R8 — cross-coin book consensus (breadth sizing)
**Concept:** when ≥3 of 5 coins' books lean the same net direction simultaneously = macro crypto flow;
size R1 up. **NOT run as EV — flagged correlated-sample / pooled-cross-asset TRAP** (note 20 step 6): the 5
coins share regime, so this would re-detect one event as five. Legitimate only as a SIZING multiplier
(à la streak-breadth), never as independent confirmation. **Verdict: CONDITIONAL-research**, needs the
≥2-wk capture to test as a same-timestamp breadth overlay without double-counting.

---
### Burst summary
- **1 PROMISING** (R1, window-limited) — the flagship: an informed order-flow signal on the 15m family from
  a brand-new data channel, surviving price-control, coin-position-control, fill-delay, within-day split,
  cheap-side disambiguation, and a clean open-book placebo (R7).
- **3 CONDITIONAL gates** (R4 tight-spread, R5 momentum-agree, R6 churn) that all converge on ONE mechanism:
  the depth signal is real only in **active, tight, mature books** — the convergence is itself validation.
- **1 CONDITIONAL-research** (R8 breadth, trap-flagged).
- **3 DEAD** (R2 tilt-change no-sep; R3 top-only subsumed; R7 open-book placebo — the good kind of dead).
- **Scouts:** none spawned (economy — one lane nailed beats a weak second door; the "scout" here is the
  already-running `capture_kbt.py` machine accumulating the cross-era window).
- **Capture demand raised (note 32 §5):** extend `kbt_books` to ≥2 weeks — this is the one concrete
  demand-led capture this burst justifies, to power per-coin BTC block-holdout on R1 and its gates.
