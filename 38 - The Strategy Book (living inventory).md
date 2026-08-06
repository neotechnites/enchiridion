# 38 - The Strategy Book (living inventory)

> THE single source of truth for the pipeline. **The pipeline IS Ryan's four columns
> (canonical, 2026-07-25): CREATED → TRIAL TESTED → TESTED BY FABLE → WORTH IMPLEMENTING (or
> dead at any stage).** Every idea sits in exactly one column. The weekly Fable review is the
> column-3 process: it takes trial-test survivors and moves each to column 4 or the grave.
> Post-compaction Claudes: this frame was lost once and Ryan had to restore it by force — it
> lives HERE now; never answer pipeline questions in any other structure.
>
> **EVIDENCE CLASSES (Ryan's rulings, 2026-07-25 — CORRECTED same day, his words):** Class A =
> full multi-year backtest; Class B = short history. *"dont assume strategies with less
> backtesting are more likely wrong, assume they are more likely to STOP EXISTING… if we see
> something thats been going on for 2 months, we dont need to be more cautious about it, it will
> likely continue, but it will likely continue for less time."* Handling differs by LIFETIME,
> not confidence: Class B edges are harvested at full appropriate size WHILE ALIVE, with tight
> decay tripwires and low weight in long-horizon projections. Class A terrain gets the hunting
> effort because durable edges compound. (Any new strategy's first live days start small for
> EXECUTION validation — that's mechanics, class-independent, and temporary.)

## COLUMN 4 — WORTH IMPLEMENTING (cleared Fable testing)
- **VOLBOOK** — **BENCHED 2026-08-05: THE METAL GAP DIED. Do not fund.** Tested on real book
  capture (54 signals / 18 series-day clusters, 0 phantom — every in-band rung had a real
  resting bid, which is more than most candidates manage). Executable **+2.52c, clustered
  t=+0.49**, bootstrap 95% CI **[−8.03, +11.58]c, P(EV<0)=0.29**; mid +2.96c, far touch
  +4.24c — **1.7c of the claimed edge is spread.** The claimed +8.61c did not survive.
  **The decay is the finding:** implied-vs-realized touch gap **+8.3pp in week 1 (Jul 27–29)
  → +0.04pp in week 2 (Aug 3–5)**; gold alone −14.53c (n=15), carrying 4 of 5 touches, *in a
  window where its own p90 tail was 56% below normal*. Capacity binds anyway — $1k buys ~1,150
  contracts against a median metals day-total top-of-book of **$1,048**: $1,000 IS the book.
  $/day flips sign on allocator detail (+$55.60 water-fill vs **−$8.25 sequential**, maxDD
  −$459) because it rests on 5 touch events. **CORRECTION: `data/volbook.jsonl` does not
  exist — volbook has NEVER emitted a paper decision. The "paper-shadowing" claim above was
  aspirational.** This is the Class-B lifetime law behaving exactly as Ryan ruled: harvest
  while alive, expect it to stop existing.
- **ENERGY WINGS (Brent+NatGas) — NOT FUNDABLE YET, capture running.** Same phenomenon alive
  where metals' used to be (+11.8pp gap today; n=74, +9.39c, touch 1.35% vs implied 13.16%).
  Refused for three reasons: **selected on the same 7 days that test it**; those days sat in a
  tail regime 16–20% quieter than normal, which biases every touch rate low; and the Mon→Thu
  extension rests on **exactly one Thursday**, into a family where "EIA-day wings −6..−8¢
  every family" is already on the DEAD list (EIA natgas storage is a Thursday release).
  **Gate: 3–4 more weeks → ~15–20 clean OOS Mon–Wed clusters, then re-test. Never fund on the
  window that generated it.**

## GRADUATED (implemented, live)
- **STREAK — LIVE-MONEY KILL RETRACTED 2026-08-06. The strategy is UNDECIDED at n=6, not dead.
  Restarted the same day at $4/trade to accrue the evidence that was never collected.**
  🔧 **THE −$65.22 / 19 SETTLED WINDOWS IS FALSE — DO NOT CITE IT.** Ryan rejected it on sight
  ("we didnt even make it into 19 streak killer markets") and the primary tape says he is right.
  Complete ledger, `nestor/data/streak_week1.jsonl` + the two state files, every fill that has
  ever existed: **19 `streak_derive` · 12 `streak_signal` · 6 LIVE FILLS** (+1 paper, Jul 23)
  · 5 signals MISSED on zero-fill IOC · 130 skips. The six settlements are
  −4.03 · −4.12 · −4.07 · +6.86 · +5.83 · +6.03 = **+$6.50, 3 wins of 6.**
  Bankroll reconciles to the cent: $100 → $100.47 (archive, 5 settlements) → re-seeded $100 →
  **$106.03** (`data/state.json`). So **"$106.03 banked" was the BANKROLL and the P&L behind it
  is +$6.50** — the line the kill said had "no locatable source" was sitting in the repo.
  **HOW THE ERROR WAS MADE, because the shape will recur: 19 is exactly the `streak_derive`
  count.** Derived signal windows were counted as settled positions and a P&L was attached to
  markets we never entered — most carry `used: false`. The cited source
  `/home/ubuntu/all_setts.json` exists nowhere on the machine and no host for it is in
  `~/.ssh/config`. **GATE THIS PAYS FOR: a live-money claim must name the fill tape and its
  row count, and the row count must be FILLS — never signals, derives, or candidate windows.**
  ⇒ **n=6 cannot kill or confirm anything.** Streak was never live long enough to be judged; it
  also never lost the $65 the kill was written on. *(An intermediate claim of "+$3.20 /
  state.json.peak / 40% win / bankroll $78.94" is also FALSIFIED — do not cite it.)*
  **STILL STANDING, and now the only open question:** the backtest claim that the underlying
  reversal rate broke (below). It is independent of the fill accounting — but it lives in an
  entry that also documents a book tape decoded backwards, so **re-test it independently before
  trusting it.**
  Measured at prices volume actually crossed, net of real fees, day-clustered:
  Jun 25–Jul 22 **+6.44c, t=+1.48** (n=221, CI [−2.10,+14.98]) · **Jul 20–Aug 6 −2.95c,
  t=−0.91** (n=123) · pooled +3.08c, t=+1.03. **It never cleared t=2 in any period** — this is
  "never established, now negative", not "was real, then died". Independent book-tape method
  agrees: −1.09c (n=27). Recent weekly EV monotone negative: −1.81c, −2.52c, −6.51c.
  **The underlying reversal rate broke:** 4-streak reversal 55.27% (n=474) → **48.28%**
  (n=408) at Jul 20, **z=+2.07**, replicated independently on SOL/XRP/DOGE (54.85% → 50.46%,
  z=+2.04). Last three weeks all below 50%. The vault's 55.8%/56.0% anchors are stale.
  ~~**Our own fills corroborate the kill** — live 25% (4/16)~~ **FALSE, RETRACTED 2026-08-06:
  there is no 4/16. The live tape is 6 fills, 3 wins, +$6.50 (see the retraction above). Our
  own fills corroborate NOTHING in either direction at n=6.**
  Capacity binds before capital: $1,000 at 41c is 2,440 contracts
  = **104–169% of ALL flow crossing the ≤44c gate in the first 60s**. At $25/trade the NEW era
  is **−$18.95/day, maxDD 50% of bank, 67% down days**; at $50/trade maxDD is 105% = ruin.
  Retry binary is UNEVALUABLE (first live fill 2026-07-25, so there is no pre-retry live P&L).
  **TWO METHODOLOGY DEFECTS FOUND — they invalidate other work, not just this:**
  (1) `work/verify-streak-retry.md` **decodes the book tape backwards** — it assumes
  "field1=yes_ask, field2=no_ask" but `scripts/capture_kbt.py:compact()` stores
  `yes_best_bid`/`no_best_bid` and DISCARDS the ladders. Its 30.7% entry universe, "median
  first-fire ask 42.0c", and the **+18pp fill-rate gain that justified deploying the retry**
  all rest on an inverted decode. (2) **Those bid fields go stale when a side empties**
  (`yes_bid=0.46` with `ytot=0.0`): unfiltered the pair sums to median **1.23** (p90 1.60), an
  impossible arbitrage, and 124/350 windows show a strictly non-decreasing yes_bid. Requiring
  `depth>0 AND yes_bid+no_bid ≤ 1.00` restores median 0.990. **Any study using these fields
  unfiltered is measuring prices nobody could trade.**
  *(Minor, verified against 26 live fills: the Kalshi taker fee rounds up to 1/100 cent per
  order, not the whole cent — the whole-cent assumption overstates fees ~3–4% at size 1.)*

## COLUMN 4 additions — FABLE REVIEW 2026-07-25 (primary ledgers read)
- **H10 econ point-ladder maker** + **H9 political spread-capture** — IMPLEMENTED SAME DAY
  (Ryan's blanket authorization): maker module + house probe sleeve built (nestor e1b989d),
  demo-proven mechanics (build-house-probe.md DEMO EVIDENCE: resting = good_till_canceled +
  future expiration_ts + taker_at_cross; expiration enforcement LAZY ~2-3min → worst-case
  orphan ~4min at 75s TTL, accepted at size 1; resting list eventually-consistent — responses
  are truth). ARMED (HOUSE_PROBE=1): quotes 1-lot two-sided when spread ≥2¢, −$20 in-code sticky
  stop, probe metrics via `nestor house-report`. Promote/kill per protocol after 2-3 days.

## COLUMN 3 — AWAITING FABLE TESTING (with tonight's evidence workers in flight)
1. ~~**SEED-PRIOR** listing harvest~~ — **DEAD 2026-08-05, see DEAD list. Gate #1 was the
   whole strategy and it FAILED: the flat 0.54/0.46 seed does not exist.**
2. **DOGE MAX ivol ramp** — RULING (verify-doge-ivol.md): real but NOT money. Persists across
   both retrievable issuances (4/4 NO wins, ~+33¢/contract first-hour) BUT DOGE-specificity
   FALSIFIED (BTC/ETH tops same pattern same months = quiet-regime artifact, rally month
   unsampled, n=2) and capacity ~$5/issuance vs $50-150 claimed. BENCHED; trigger = MAX-ladder
   proliferation (more issuances/coins → capacity) or a vol-regime gate.
3. **INFLATION-FLASH** — RULING (probe done, verify-inflation-flash-access.md): the front-run
   path is STRUCTURALLY DEAD (prelim-settled markets freeze trading 1 min BEFORE the release);
   access itself is solved (Eurostat flash = free keyless JSON). Surviving sliver: KXHICP
   (final-settled, ~17-day window, flash public) — narrowed gate: calibration check of the book
   vs the ≤0.1pp revision base rate, next burst.
4. **SPOKEN-COUNT** (MUSK family) — vehicle-gated; Tuesday's capture builds Warsh base rates.
5. **DUTCHBOOK** — ruling: stays 3; measured median 0.65¢ is sub-slippage. Gates: detector
   opposite-direction fix (queued athena change) + ≥1.5¢ + leg-quote-lifetime capture accrual.
6. **FED-HOLD** — stays 3; n=1, nothing reviewable until forward capture accrues.
7. **ZERO-FEE SERIES** — stays 3; trigger DEFINED: any of the 13 series showing OI>1k or a
   two-sided book (queued: add to listing_monitor watch).
8. **RENTEC forward doors R10-R12** (coin-trajectory model on fresh channel / hourly books /
   cross-coin breadth) — capture-gated: need LIVE-orderbook capture (queued athena demand).

## REVIEW CORRECTIONS + CAVEATS (2026-07-25 Fable pass)
- **RENTEC R1 flagship: DEAD, RETRACTED by its own burst-2** (KBT source-frozen-book artifact;
  de-contaminated EV −12.5¢ uniform; the +31¢ leader idea was stale-book lookahead, 224/225
  frozen). My earlier Book entry "PROMISING held" propagated an R127 log-compression ERROR —
  the primary ledger says the opposite. Lesson: the Book links to ledgers; reviews read ledgers.
- **KBT FROZEN-BOOK CAVEAT on streak-retry numbers:** ~half of KBT-captured books carry a stale
  book field (R13 doctrine, lane-RENTEC-burst2). Lane 1's flicker/fill-rate projections (0.926 /
  88.5%) did not churn-filter and may be inflated. The retry FIX stands (our own live post-miss
  Kalshi-API snapshots showed the ask present — independent of KBT), but projected fill rates
  are UNVERIFIED until live fills accumulate. Live tape supersedes.

## OUTSIDE THE FUNNEL (Ryan's pre-senate slate — alive, own tracks)
- **PCE/GDP index event wings** + **MSFT/META gap wings** — calendar-scheduled Jul 30 (adjacent-
  kill flag from EVENT-VOL noted; INDEX family unaffected).
- **WEATHER** — tested 2026-08-05, three legs, one survivor and it is small.
  · **Ensemble/bias forward OOS: DEAD — it FAILED its own named gate.** 14 days, 78 (city,day)
  clusters: **−0.8 to −2.3c/ct** executable, clustered t −0.30 to −1.20, ~0 even at the mid.
  Fitting per-city bias *on the test set* rescues it only to +0.77c (t=0.30), **$4.03 total
  over the entire forward window**. The 8-city in-sample calibration does not transfer.
  · **Deterministic floor, TAKER: DEAD.** Mechanism is real and better than claimed —
  **1,401/1,401 signals correct, 0 wrong**, 624 city-days (CLI high ≥ METAR spot max on
  728/730). But **98.9% of signals had ZERO fillable volume**: $310 gross over 73 days × 10
  cities = **$4.25/day for the whole universe**, 64% of it one city-day. And the 20–45min
  api.weather.gov lag is not an opportunity — the market is at 1c/99c *before* METAR locks
  (median pre→post price change **0c**, p10/p90 0/0). It prices the nowcast, not the report.
  · **Deterministic floor, MAKER: the only survivor, ~$8/day at $1k.** 7.26M contracts of
  wrong-side post-lock flow but **99.9% of it at exactly 1c** — so the trade is rest NO at 99c
  for 1c on 99c collateral per ~12h = 1.01% ROC, maker fee 0. $1k → ~$10/day gross, ~$8 net of
  the 0.21% error bound. Payoff is **+1c vs −99c** and queue position is **unmeasurable** (no
  weather depth capture exists). Not worth a build alone, but it is the right SHAPE — maker,
  favorite side, capacity-rich — and matches the ratified "residual must land on favorites".
- **AAA GAS LAG — DEAD at the premise (2026-08-05).** The claimed inputs do not exist: no
  wholesale gasoline series anywhere on the VPS, `aaa_gas_2yr.json` holds **18 days**, total
  AAA history available is **82 days**, and KXAAAGASD's earliest event ever is 26MAY25. The
  reproducible model is AR(1) on AAA's own lags at **OOS R²=0.505**, not 0.64 from wholesale.
  **The market beats it**: Brier 0.0473 market vs 0.0796 model (n=660). The +18.54c "edge" is
  entirely **outlier-print pickoff** — same trades at our-side VWAP +3.33c (t=1.25), skip one
  print +0.40c, skip two **−0.13c**. Entry print median **10 contracts = $3.24**; forcing $1k
  in gives maxDD **−$2,733** on a $1,000 bankroll. *(Note 53's "4:34pm intermediate CLI pins
  settlement 85.5%" is the WEATHER/CLI family — it was never an AAA claim.)*
- **LOCK** — ~~DECAY-benched: +1.72¢ → −1.07¢ on the recent kill-scan; the market closed it.~~
  **DEAD 2026-08-06 (SECOND VERDICT, SAME DAY) — the edge does not survive correction. −0.38¢
  pooled. DO NOT TRADE, DO NOT DEPLOY.** Sequence, all one day, because it is the lesson:
  (a) the 2026-07-23 decay kill was retracted — the −1.07¢ genuinely does not reproduce;
  (b) but the re-scan that retracted it carried a **60-SECOND LOOKAHEAD BUG**, and its clean
  reproduction of the published control proved only that the control shared the same defect.
  **Reproducing a pipeline exactly is fidelity, not validation.**
  THE BUG: Coinbase 1-min candles are labelled by bucket START; `lockrescan` stored bucket
  CLOSE, then `spot_at(t)=bisect_right(ts,t)−1` returned a price up to 60s in the FUTURE at a
  checkpoint 30–240s from settlement. Verified 3 ways (close[T−60]==open[T] exactly on live
  data; no-lookahead read matches real `floor_strike` MAD 1.77bp vs 3.99bp; settlement-side
  agreement 95.9% vs 89.6%). Smoking gun: Z≥4 flip rate at 120s before close reads **0.017%**
  buggy vs **0.362%** corrected — 0.017% two minutes out is physically impossible.
  THE CORRECTED NUMBERS, 93–97¢ Z≥4, 73 days: **+3.08¢ → +0.84¢** (lookahead removed) **→
  −0.38¢** (price-source error +1.3¢). Per-asset no-lookahead pre-adjustment: BTC +0.42¢
  (t=0.43, n=331, NOT significant) · ETH −1.61¢ · SOL +0.40¢ · XRP +2.67¢ · DOGE +1.74¢ —
  only XRP survives and it does not clear the +1.3¢ adjustment with margin. No rescue gate
  exists: a min-vol gate and a raw-distance floor were both tested, neither improved EV.
  **DEFECT LOCATED EXACTLY (2026-08-06 recon lane):** `lockrescan_pull2.py::do_spot()` does
  `have[c[0]] = c[4]` — Coinbase `granularity=60`, `c[0]` = bucket START, `c[4]` = CLOSE, so the
  row stamped T carries the price at T+60. `lockrescan_analyze.py::spot_at()` then reads it as
  the price at T (mean +30s ahead). The July study used `bisect_right(ts, t−60)−1` (mean −30s).
  **Which alignment is right, proven empirically** (n=154,395): at seconds where the market is
  47–53¢, spot must sit at K. Mean |spot−K| by row lag: −60s → 25.70 · **0 (re-scan) → 16.07**
  · **+60s (July) → 14.13** · +120s → 17.43. Minimum at +60s ⇒ the July alignment is correct.
  **CORRECTION TO AN EARLIER READING: the price source contributed 0.00¢.** Per-second bars and
  trade prints give bit-identical answers (the "per-second bars" were the SAME `/markets/trades`
  API, just fuller pagination — never a second source). The whole gap is spot alignment. The
  "finer data ⇒ worse edge" story was wrong about the mechanism.
  **NEITHER PUBLISHED NUMBER IS RIGHT.** The re-scan's +3.39¢ is future-shifted; the July
  holdout's −1.72¢ used a CONTINUOUS per-second scan, which enters on transient dips INTO
  93–97¢ (touching the band from above = the favorite is deteriorating) and costs −3.83¢ that
  nestor never pays, since production polls discrete checkpoints. Trustworthy config = honest
  spot + discrete checkpoints: **POOL n=840, 97.14% win, +0.89¢ @0.5¢ spread, −0.33¢ @ the
  live-measured 1.80¢.** Breakeven spread **+1.45¢** vs real **+1.80¢** — short by 0.35¢ before
  slippage. The lookahead had erased **17 of 24 losses** and inflated EV 3.7×.
  Per-asset Z≥4 @1.8¢: BTC −0.03 (n=199) · ETH −3.83 (n=155) · SOL −0.27 (n=148) · **XRP +1.57
  (n=205)** · DOGE +0.32 (n=133). Only XRP clears, and Z≥6 "rescues" are post-hoc and fail on
  ETH/SOL/DOGE.
  **THE ONE LIVE CAVEAT (honest bracket):** LAGGED spot is 0–60s stale (mean t−30s); FRESH is
  0–60s ahead (mean t+30s). **A live WS spot feed sees exactly t — better than either**, and is
  unavailable in 1-min close bars. So the true live value is BRACKETED in **[+0.89¢, +3.29¢]**
  (Z≥4, 0.5¢ spread) and cannot be narrowed from data on disk. **Highest-value outstanding
  measurement if this is ever revisited: log real-time WS spot alongside each signal for ~2
  weeks, then re-run the ablation with a third arm.** Note this cuts toward the edge, not away.
  **RAW-DISTANCE GATE: TESTED, FAILS — and the mechanism closes the question (2026-08-06).**
  A raw |spot−strike| floor on top of Z≥4 makes EV monotonically WORSE: floor 0 → −0.38¢,
  10bp → −0.57, 15bp → −1.33, 20bp → −1.59, 30bp → −1.76 (all @1.80¢, conv=end). **Losses sit
  at HIGHER raw distance than wins** (median 14.2bp vs 12.1bp) — the exact reverse of the
  hypothesis. A *ceiling* is what helps (dbp<5 → +1.05¢), i.e. the gradient runs the other way.
  WHY, and this retires the idea: inside a 93–97¢ band **the market has already pinned Z**
  (p10 4.07, p50 4.64), so `dbp = Z·mv·√min` varies almost entirely through volatility —
  **corr(log dbp, log mv) = +0.823** vs −0.170 with log Z. **A raw-distance floor IS a
  minimum-volatility gate wearing a different hat**, and a min-vol gate is also monotone
  destructive (0 → −0.38; 1.0bp → −0.72; 2.0bp → −1.12). Both together = worst cell (−1.96¢).
  ⇒ **The 2-year low-vol failure mode does NOT transfer to the traded set.** That 8.5× decile
  spread was measured on the UNCONDITIONAL Z≥4 universe (all prices, no band, no on-side
  filter). Conditioning on *the market pricing the favorite at 93–97¢* removes that population
  entirely — in calm regimes the market is over-cautious in the band, making decile 1 the BEST
  traded cell (flip 1.449%, +1.31¢). Within-asset vol terciles show flip rate RISING with vol
  (1.96% → 3.02% → 3.88%), opposite to the synthetic study. The earlier "raw-distance floor
  didn't help" note was right, for a reason nobody had stated until now.
  Only positive floors are the 40/50bp tail: n=41, 0 losses, but **Wilson95 UB on 0/41 = 8.57%
  vs 2.63% breakeven** (3.3× over) and 22 of 41 are DOGE. Not a result. Converse cell (Z<4 &
  large distance) fails split-half: H1 +2.01 (n=189) vs **H2 −4.56 (n=44)**.
  **XRP, pre-empted by this lane — passes split-half, fails everything else.** n=331, 4 losses,
  +1.46¢@1.8. But day-clustered **t=1.92** (doesn't reach 2); **Wilson95 UB on its flip rate
  3.07% vs 2.67% breakeven — does not clear**; **selection Monte Carlo: P(best-of-5 ≥ observed
  | one shared true rate) = 13.5%**; the advantage is flat across distance buckets (an asset
  effect, not a mechanism); and **structurally it goes the WRONG way** — XRP's book is THINNER
  (median 626 ticks/market vs BTC 1,921; 31% of next prints ≥2¢ vs BTC 11%), so its true cost
  is plausibly ABOVE the pooled 1.80¢, which would eat the entire +1.46¢. Establishing it needs
  **n≈441 ≈ 98 more days**. The whole per-asset difference is 4 loss events.
  ⚠️ **UNRECONCILED:** the recon lane counts ~20 signals/day pooled (n=840 / 6wk) on lagged
  spot, while the `lock-dryrun` on LIVE books + real-time spot found **0 in ~25 window-series**
  (max Z below 97¢ = 3.46). Both are credible and they disagree on whether the entry population
  even exists. Resolving this is prerequisite to any revival.
  SECOND INDEPENDENT DEFECT — the price source: live orderbook capture measured `/markets`
  reading LOW vs the real book by median +1.30¢ in 93–97¢ (p90 +8.0¢; **29% of moments exceed
  the entire claimed edge**) and +5.00¢ in 90–93¢ (61% exceed 3¢). Corroborated on the tape by
  next-print drift (median +0.8¢ at 95–97¢, +1.0¢ at 90–93¢, cheap bands worst).
  🔧 **CORRECTION 2026-08-06 (adversarial XRP lane): THE "STRUCTURAL KILL" BELOW IS FALSIFIED
  AS AN ABSOLUTE. Do not cite it.** Re-run with 7 windows instead of 5: **BTC FIRED — 28 rows
  in one window, max Z 7.82 below 97¢.** Reconstruction predicts P(fire|window)=4.80% for BTC;
  observed 1/7 = 14%, fully consistent. **The dryrun's zero was a small-sample zero, not a
  structural impossibility, and the UNRECONCILED conflict is resolved in favour of the tape.**
  The entry population exists. LOCK still dies — on the lookahead and on execution cost — but
  on TWO kills, not three, and I over-claimed this one. Per-asset max Z below 97¢ across 7
  windows: BTC 7.82 (fires) · SOL 3.48 · ETH 3.22 · DOGE 3.21 · **XRP 2.75 (lowest of five)**;
  XRP 0 fires in 7 windows is **UNDERPOWERED — P(0|7)=70.8%, cannot reject**. ~60 windows
  (~10h of recorder time) makes it decisive.
  ~~**THE STRUCTURAL KILL (independent, and the cleanest — live books, 2026-08-06).**~~ The
  `lock-dryrun` replay over 4,338 in-window rows / 25 window-series: **721 rows had a favorite
  in 93–97¢, 480 rows had Z≥4, and ZERO had both.** Not a small-sample zero — **max Z over
  every favorite priced under 97¢ is 3.46**, the cheapest Z≥4 favorite is **98.20¢** (median
  **99.80¢**), and median Z rises monotonically across every 5¢ bucket. By the time the coin is
  4 normal moves clear, the book has already priced the lock at ~99.8¢. **The entry condition
  does not exist on real books.**
  ⇒ **THIS IS NOTE 09'S KILL, VERBATIM.** [[09 - Deep-Favorite Lock (DEAD)]] argued in June:
  *"if the coin is truly 4+ moves clear, the market has already priced it to 98-99¢, not 95-97¢
  — '4 moves clear' and 'still 95-97¢' contradict each other."* Note 08 claimed the Z filter
  was "the whole trick" that escaped it. **It never escaped it — the 60s lookahead manufactured
  the escape**, inflating Z at cheap prices by reading future spot. The same structural
  contradiction killed this idea twice, a month apart, and the second time we nearly funded it.
  **90–93¢ BAND = ARTIFACT, RESOLVED.** Its apparent +6.4¢ superiority was the mislabelled
  cheap price. Priced correctly the ranking INVERTS (+1.34¢ vs +1.87¢; under no-lookahead
  −4.56¢ vs −0.38¢), and it is negative in all five assets and both halves. **The 93¢ floor
  stands** — note 08's original lesson was right.
  ~~DECAY KILL RETRACTED 2026-08-06 — the −1.07¢ does not reproduce; the edge measures ALIVE.~~
  Full re-scan on complete data (34,459 markets, real `floor_strike` all 5 assets, retention
  floor 2026-05-25 → 08-05): **every week positive, pooled and BTC-alone.** Control reproduced
  note 13 to the digit three ways incl. a fresh independent pull (n=138, 1 loss, 99.28%,
  +3.25%/trade). The kill's OWN "last 4 weeks" window (06-25→07-23) measures **+3.39¢/contract
  pooled (n=752)**, not −1.07¢; BTC +2.82¢ (n=191). Post-kill 07-23→08-05: **+3.13¢ (n=428)**.
  −1.07¢ is arithmetically unreachable: at ~96.4¢ entry it requires a **4.44% flip rate**;
  measured Z≥4 flip rate is **0.181%** (90/49,747, Wilson UB 0.222%), monotone across Z. Could
  not be reproduced under any cost assumption tried (1¢ spread, per-contract ceil fee, no
  backfill, 1.5¢+ceil → worst case still +1.67¢). **What produced −1.07¢ is unknown** — a
  dropout artifact was hypothesised and MEASURED FALSE (old `forward_lock_ticks.json.gz` is
  missing 0 of 1981 markets). Frequency now **31.0/day pooled, 6.9/day BTC** (~2× the vault's
  old ~16/day). Per-asset last 6wk all positive: BTC +3.20 · ETH +2.57 · SOL +3.99 · XRP +3.29
  · DOGE +4.19% — not BTC-specific.
  **STATUS: NOT a green light to trade.** The one untested link is unchanged since note 08 and
  is unbacktestable: real fills/depth at 93–97¢ in the final 2–4 min (all numbers above are
  last-trade + 0.5¢). At +3.3¢/contract, >3¢ of slippage kills it. That needs forward book
  capture, not another backtest.
  **NEW, contradicts a documented lesson — needs its own test:** the "hard floor at 93¢, below
  it the edge inverts" claim NO LONGER HOLDS. Last 4wk the **90–93¢ band is the best cell**
  (+6.4% pooled n=233 / +6.5% BTC n=66 at Z≥4; +8.2% at Z≥5, 100% win on n=102). The old floor
  was set price-only-ish on 30 days. Do not move the operating point on this until tested.
  **TRAP for any future pull:** `limit=1000` on the trades endpoint now reaches only ~137s
  before close on BTC (was 293s in July) — it silently starves the 240/180/150s checkpoints.
  `lockrescan_backfill.py` pages around it (median span 328s; ≥240s coverage on 6677/6890 BTC).
  **A re-pull that skips the backfill is not comparable to the control.** Also: Kalshi trade
  retention now ends **2026-05-25**, so the kill's "first 6 weeks" arm was really ~4.4 weeks.
  Artifacts: `~/kalshi_data/lockrescan_*` ; scripts `lockrescan_{pull2,backfill,analyze,
  dropout_audit}.py`. Re-entry test still in-code (`nestor backtest-lock`).

**WEEKLY REVIEW STANDING DUTIES (added 2026-07-25 after Ryan's "are we killing real edges"
challenge):** every weekly review must (a) run `backtest-lock` re-entry scan, (b) check weather
forward-capture maturity, (c) sweep the DECAY bench and every column-3 trigger — a bench whose
re-entry checks never run is a graveyard with extra steps.
- **POLYLAG** — auto-gated: analyze only tapes containing a ≥3¢ Poly move (daemon running).

## COLUMNS 1-2 — CREATED / TRIAL TESTING
Continuous (ideator bursts + verify lanes). ~200 raw ideas processed to date; trial-test
mortality is the funnel working (cheapest decisive kill first). Survivors surface into column 3.

## DEAD (with numbers — money the kills saved)
**SEED-PRIOR (killed 2026-08-05)** — the +26.4¢ was two compounding errors. (1) PRICING BUG:
`scripts/seed_prior_build.py:14` sets `SEED_NO_PRICE = 0.46`, but on Kalshi `no_ask = 1 −
yes_bid`, so against a 0.54/0.46 seed buying NO costs **0.54, not 0.46** — both sides cost 54¢
and the 8¢ was the spread. Correcting it: blind fade +8.84¢ → **−1.16¢**; confident subset
+26.45¢ → +21.39¢. (2) THE SEED REGIME IS GONE: **0 of 39 live mention markets** sit at
0.54/0.46 and only 14 of 107 captured new listings do (median new-listing spread **83¢**;
46% over 20¢, i.e. untradeable). Kalshi's mention MM now prices **per word** (corr(prior,mid)
=+0.52). And the prior has **zero coverage of new listings** — every new earnings-mention
series is a brand-new family, so all 107 fell through to the global 0.4516 backoff, where
|0.4516−0.50|<0.15 means **the signal never fires**. ~60% of the validated P&L came from
**delisted** series (WC 30.2%, MLB 16.2%, LoveIsland 5.8% — all 0 markets on the API).
At real executable prices: **1 confident signal in 12.4 days, ~$28 of capacity, $0.00/day on
$1,000.** The word prior itself IS a real time-validated predictor (+17.9¢, t=15.3, fit-early
/test-late) — it just has no trade surface, because the counterparty it beats doesn't exist.
*(`build-seed-prior.md` and `verify-seed-prior.md` both carry the 0.46 error — do not trust
their numbers.)*
**GATE THIS PAYS FOR: any backtest must state the side it buys and prove that price is the
real touch. `no_ask = 1 − yes_bid`; a "cheap" NO is a dear YES.**

FOMC-move ladder buying (0/12 recent clear the priced move) · EIA-day wings (−6..−8¢ every
family) · crypto favorite-buying 95-98¢ (n≈4,700) · xvenue MLB arb (pre+in-game; artifacts) ·
deribit hourly gate (tenor artifact) · earnings-MENTION lag (closes-on-occurrence) · naive
count-lag (29/29 priced) · buy-the-jump on liquid ladders (n≈1,576) · thin-frontier structural
locks (4,740-event scan).

## FAVOURITE-BIAS MAKER (crypto 15m) — ☠️ **REFUTED 2026-08-06 11:33 MDT, same day it was found**
> **THE RULE IS DEAD. A DATA-BUILD BUG MADE IT.** `calib_build.py` stored trade price as
> `ps.astype(np.uint8)` — but the Kalshi crypto tape above ~1¢ is a **0.1¢ grid**, and only
> **16.9%** of prints in the 96–98¢ band sit on a whole cent (BTC **10.3%**). The floor()
> did three things: (a) it made the fill test `floor(P_k) ≤ floor(f)` count a trade at 96.9¢
> as filling a bid at 96.0¢ → **phantom fills**; (b) it turned floor() on the YES side into
> ceil() on the NO side → the side-asymmetric sampling bug, visible as 51.1/48.9 YES/NO fill
> split vs 49.6/50.4 once fixed; (c) it did *not* much move the print-level phenomenon
> (buy-favourite-always +1.842¢ → **+1.835¢**, unchanged — that part stands).
> **On the true 0.1¢ grid the identical rule is +0.165¢, t=+1.64** — below its own
> significance bar before any selection correction. Requiring the trade to print even **one
> 0.1¢ tick through** our resting level: **+0.123¢, t=+1.20**. Through 0.5¢: **−0.112¢**.
> Through 1.0¢: **−0.543¢, t=−4.19**. Through 2.0¢: **−1.872¢**.
> Shrinking the assumed rest 60s→30→10→5s barely moves it (+0.249→+0.241¢) — **resting time
> was never the binding assumption; fill realism was.**
> Corroborated independently by the live book recorder: best-bid level lifetime **median <1s**,
> **0 clean touches in 11,146 resting windows**, median spread 0.2–0.6¢ above 91¢, BTC a
> locked 1¢ book. There is no cent of spread to capture at 96–98¢.
> **The residue is the pagination artifact again.** EV by market print count: **+1.99 / +0.93 /
> +1.11 / −0.11 / −4.69¢** across quintiles; corr(market prints, tape reach) = **−0.632**.
> The 300–420s sub-window (+0.769¢) is +0.99¢ in deep-tape markets and **+0.03¢** in
> truncated-tape ones. **The markets with a real book are the negative ones.**
> **One real conditioning survives and does not save it:** |Z|≥2 at 96–98¢ = +0.971¢ (t=+5.40,
> 5/5 assets, H1 +1.11 / H2 +0.83, Romano-Wolf p_adj 0.0001 over 50 cells), and its contrast
> holds *inside* every volume tercile (+0.25 / +0.84 / +1.20) so it is not purely the artifact
> — but **in the high-volume tercile |Z|≥2 is still −1.13¢**, and that is the only tercile with
> a book to trade against. Best honest dollars: **+$11.65/day on $1,000 at 2% sizing**, on a
> fill convention the live book already contradicts. **Do not build the queue-logging instrument.**
> Clock: nothing. MT 08–12 is −0.789¢ (p_adj 0.011) but 74 days gives ~10 obs/weekday and a
> **~1.1¢ corrected MDE** vs a 0.12¢ honest base — weekday is **unanswerable at this sample**.
> Artifacts: scratchpad `refine_px.py` (grid rebuild, 1:1 aligned), `refine_fill.py|.out`
> (strictness ladders), `refine_cover.py|.out`, `refine_honest.py|.out`, `refine_final.py|.out`.
> **LESSON (new, general): dtype IS a modelling assumption.** `astype(uint8)` on a price is a
> silent 0–0.9¢ market-structure change. Check the tick grid of any tape before simulating a
> passive order on it. This is bug class 5.

### Original (now-refuted) writeup, kept for the mechanism only
**Found by the calibration-surface sweep after LOCK died.** 28.9M prints, 34,440 markets, 5 assets,
74d (2026-05-25→08-05). Market-level YES rate **0.4944** (per-asset .4917–.4966) so there is no
bull/bear drift contaminating it.

**THE FINDING: the market systematically underprices the FAVOURITE, everywhere.** Buy-favourite-
always = **+1.842¢/contract, clustered t=+18.8**. Every surface cell above 55¢ is positive. This is
the favourite-longshot bias, finally measured cleanly on the whole surface rather than one band.

**BUT TAKER IS DEAD IN 62 OF 70 CELLS** — fee+spread 1.4–3.1¢ against an edge of 0.3–1.7¢. The
mispricing is real and smaller than the toll. *This is the day's governing arithmetic and it killed
eight strategies before this one.*

**THE RULE (maker-only, the sole survivor):** every 30s, per crypto 15m market, with **120–420s
left**, if the favourite's last trade is **96–98¢**, rest a buy on the favourite **at that price**;
cancel after 60s; one fill per market; hold to settlement; **never cross**.
- EV **+0.313¢/contract = +0.32%/trade**, win 96.8%, n=**21,769 fills**, 294/day, fill rate 0.899
- clustered **t=+3.02** (370 asset-day clusters) · split-half H1 +0.30 / H2 +0.33
- per-asset **5/5** (BTC .31 ETH .36 SOL .33 XRP .28 DOGE .28) · asset×half **10/10** · weekly **11/11**
- selection-corrected: 36-cell Romano-Wolf, cluster bootstrap 3,000 reps → p_adj ≈ **0.005–0.01**
- maker fee **$0.00**; the taker bar in this same cell is 1.51¢ = **4.8× the edge**

⚠️ **QUEUE POSITION IS THE WHOLE REMAINING QUESTION.** off1 (1¢ inside): FRONT **+0.772¢** ·
MID +0.579¢ · **BACK −0.423¢**. ~60% of the attractive version is queue rent that trade-print data
**cannot** verify. Only logging our OWN resting orders' queue position and fill outcomes resolves it.
⚠️ **SIZING: 10% BUSTS.** $100 bank at 10% → $9.21/day but daily sd $32.95, worst day −$59.10,
**max DD $134 on a $100 bank**. Honest sizing ≈2% → ~$1.8/day at $100. A 96.8%-win/−97¢-tail payoff
does not tolerate 10%.

**NOT PROVEN — a coverage artifact, do not trade:** fav 85–99¢ @ 420–870s (+1.7¢, +1.9%/trade,
t=+6.2) is the `limit=1000` pagination trap again. BTC's tape does not reach past ~300s (median
prints in 420–870s = **0**). Markets whose tape reaches 800s show +2.26¢, those that don't −0.93¢.
Retrieval gradient, not price gradient.

**INDEPENDENT CORROBORATION OF THE LOCK KILL.** Favourite edge by |Z| at 92–99¢/120–420s:
+0.60 (Z 0–1) · +1.19 (1–2) · +0.94 (2–3) · +1.03 (3–4) · **−0.49 (4–6, n=1,488)** · −0.67 (6–9).
**The edge dies exactly where LOCK traded.** Z≥4 selects the cells where the bias is gone.

**Ryan's three hypotheses, answered, none tradeable on its own:** (a) under/over-reaction — neither,
the favourite edge is FLAT across 60s velocity buckets (+0.87 to +1.57¢, 8 buckets) — a *level*
effect, not a *reaction* effect, so no conditioning value; (b) late-window settlement drift —
falsified in the expected direction, the bias SHRINKS into the close (+1.49¢ at 450–900s → +0.47¢ at
0–10s): the market gets MORE accurate as the 60s index average locks; (c) deep-lock band — see the
LOCK corroboration above.

~~**NEXT MEASUREMENT (live):** log our own resting-order queue position…~~ **CANCELLED** — the
0.1¢-grid rebuild answered it offline for free. The queue question was moot; the fills were phantom.
Artifacts: scratchpad `calib_*.py|.out|.npz` (build, surface, drift, rule, maker, valid, select,
probe, queue, econ).

## ⚠️ GATE 7 — RETRIEVAL-DEPTH INVARIANCE (new law, 2026-08-06, cost: two false edges in one day)
**Every sample must be shown invariant to how completely its data was RETRIEVED.** It is a one-line
split and it would have killed both of today's survivors in five minutes.

**The mechanism, measured, not theorised.** Kalshi's `/markets/trades` returns only the most recent
~1000 prints. Whether a market's early window is retrievable inside that cap is a function of its
**total print count over the whole 900s** — i.e. it encodes POST-PLACEMENT information. A market
retrievable back to 880s is one whose tape **went quiet, because the outcome became obvious.**
At the SAME anchor price (~83¢), unconditional favourite win rate:

| asset | natively deep (retrievable) | added by backfill (was invisible) |
|---|---|---|
| ETH | 83.7¢ → **94.6%** (+10.9) | 82.6¢ → 81.8% (−0.9) |
| SOL | 83.5¢ → **92.1%** (+8.6) | 81.5¢ → 67.7% (−13.8) |
| XRP | 83.3¢ → **91.1%** (+7.8) | 81.8¢ → 74.9% (−7.0) |
| DOGE | 82.9¢ → **91.3%** (+8.3) | 81.3¢ → 70.7% (−10.5) |

**The retrieval flag is worth ±10–20 points of win probability at a fixed price.** That is future
information relative to a tau-720 placement. It is lookahead wearing a coverage costume.

## EARLY-WINDOW POCKET (fav ≥76¢, tau 690–740s) — ☠️ DEAD 2026-08-06, same artifact
Backfilled all 5 assets past close-880s via `max_ts` backward paging (12,177 markets re-paged).
**+3.35¢ → +0.04¢ [−1.27, +1.34], t=+0.07, n=4,600.** Split by retrieval:
natively deep **+4.43¢ (t=+6.36, n=3,018)** · added by backfill **−10.99¢ (t=−6.76, n=1,160)**.
Post-backfill tau curve is negative and significant from tau 30–570 and indistinguishable from zero
from 630 out — **nothing crosses**. BTC never had a hump at all (negative/flat across 30 cells;
its native reach was only 337s — the trap).
**XRP WAS NEVER THE OUTLIER — it was the LEAST-CONTAMINATED ASSET.** Its −1.11¢ was three separate
flaws (non-passive fills, coverage selection, 2-slice window); repaired it matched SOL/DOGE exactly.
**IT PASSED AN HONEST SELECTION CORRECTION AND STILL DIED.** 12,960-cell maxT permutation with every
refinement inside the grid → **p=0.0159**. *The problem was never multiplicity. It was the sample.*
Split-half was perfect (H1 +5.04 / H2 +5.33) — **split-half cannot detect a bias present in both
halves.** Gate 4 (per-asset) is what caught it, and only AFTER the data was repaired.

**INDEPENDENT BUG:** `scripts/maker_favstrat.py` omits the passivity check its sibling
`maker_surface.py` documents (`YES BID at P: passive iff p0 > P`). **51–62% of the published
survivor's "maker fills" were orders already marketable at placement** — i.e. takes, not makes.

**KBT books did not settle it.** ~10M snaps 07-24→08-06, but the mandated staleness filter
(`depth>0 AND yes_bid+no_bid ≤ 1.00`) rejects **54–62%**, and in the 76–90¢ band at tau 690–740
essentially nothing survives (SOL 1 valid snap, XRP 25, ETH/DOGE/BTC 0, of ~73k in-window snaps
per asset). Field 0 is an **ISO string**, not ts_ms. `scripts/pocket_kbt.py` for whoever needs it.

**CRYPTO 15m IS NOW SEARCHED OUT.** ~10 strategies killed 2026-08-06: lock (twice), longshot fade,
barrier/touch (−11%), boxes (−2.3%), exit rules (flat), distance/vol gates (monotone worse), XRP,
favourite-bias maker (dtype), early-window pocket (retrieval). The market is calibrated to ~1¢
against a 1.4–3.1¢ toll. **The taker surface is negative everywhere; the maker surface is negative
at every tradeable distance (best cell −1.17¢) with adverse selection 4–9× the zero-fee advantage.**
Do not re-enter this market without a genuinely new mechanism — not a new gate on an old one.

## MENTION MARKETS — surveyed 2026-08-06. Headline claim FAILS; a small survivor clears its gates.
🔧 **DATA-INTEGRITY BUG, CONTAMINATES THE EXISTING MENTION CORPUS: `close_ts` IS OUTCOME-DEPENDENT.**
Within the same event, YES markets close on average **15,903s (4.4h) EARLIER** than NO markets;
**88.7% of 803 events** show YES closing first; only 1.7% share a single close time. **Any study that
anchored time-to-resolution on `close_time` leaked the outcome into its time axis** — that includes
the exit-policy study in HANDOFF §2 ("exit T−2h", "hold to settlement −0.82¢"). Correct anchor =
`max(close_ts)` per event, restricted to `t < min(close_ts)` so every market is still trading.
**Same class as gate 7: a field you thought was descriptive is actually post-outcome.**

🔧 **THE HANDOFF's SPREAD PROFILE IS INVERTED.** Measured: median spread **1.0¢ at T−0..2h**, 2–4¢ at
T−2..24h, **7.0¢ at T−48..73h** — books TIGHTEN into the event, they do not widen. And **65–77% of
hours beyond T−24h have ZERO volume** (median vol/hr: 937 at T−0..1h → 0.0 beyond T−24h).
**Therefore the "~3¢ overprice at T−24h" sits on a mid nobody can trade.**

**The 7.8¢ mid-band claim: right sign, fails 4 of 7 gates.** Per-market −6.87¢ (335 markets, 137
events, SE≈3.5¢) — but gate 2 FAILS (displaced anchors score BIGGER: −8.29¢ at +12h, −10.39¢ at
−24h vs −6.87¢ real ⇒ the time axis carries no information), gate 4 FAILS (early −3.04¢ vs late
−9.90¢; largest-n families flat — WC +0.37 n=24, BERNIE −0.63 n=19, NBA +12.80 n=17; the edge lives
only in n=8–12 earnings families), gate 5 NOT PASSED (degenerate maxT null), gate 7 FAILS (shallow
−4.38¢ vs deep −9.38¢). The obs-weighted −16.9¢ was within-market pseudo-replication.

**SURVIVOR — the 1¢ tick scalp. First candidate all day to clear every applicable gate.**
> When the book is `0/1`, T−0..48h, post a passive YES offer at **1¢** (= buy NO at 99¢). One fill
> per market. Hold to settlement. **Never cross.**
- **645 markets, 206 events, 53 series — settled YES: ZERO**
- EV **+1.000¢/contract** in-sample; 95% upper bound on p_yes = 0.463% ⇒ **conservative +0.54¢**
- **+1.01%/trade in-sample, +0.54%/trade conservative** — meets Ryan's 1–2%/trade at the bottom edge
- **$/day at $1,000: $10.10 in-sample, $5.42 conservative.** CAPITAL-bound at 1,010 contracts (1¢-tick
  flow is ~619,000 contracts/day, so flow is not the constraint — collateral is).
- Gates: 1 PASS · 3 PASS · 4 PASS (0 YES in both halves) · 6 PASS (1¢ integer grid verified) ·
  7 PASS (0 YES at every retrieval depth) · 2,5 n/a (mechanical, not scanned)
- **Stands WITHOUT the LIP program** — pure settlement edge, makers pay zero fees, no exposure to
  the 2026-09-01 sunset.
⚠️ **Why not to size into it:** 99¢ of collateral per 1¢ of premium; the whole result rests on zero
YES resolutions in 645 markets; **one bad settlement erases 99–183 winning contracts**; and this is
the deepest queue on the venue — it is precisely what LIP pays everyone else to quote. "1 turn/day"
is optimistic.
NOTE: `mentionstudy/`, `markoutstudy/`, `screen/`, `exitstudy/` are **VPS-only, not local**, so none
of HANDOFF §2's priors were re-verifiable in this pass. Source used: `survey_candles_mention.jsonl.gz`
(hourly candles, 1,500 of 14,998 settled markets, 62-day span) — still being written by another
session's capture; numbers firm up at its 3,797-market target.
Artifacts: `~/kalshi_data/mentionsurf/` (`mention_load.py`, `mention_final.py`, `mention_extreme.py`,
`mention_sim.py`, `mention_focus.py`, `mention_gated.py`, `mention_anchor.py`, `mention_FINAL.json`).

## ☠️ VENUE-WIDE VERDICT (2026-08-06): KALSHI IS CALIBRATED. The crypto result is a VENUE property.
Survey: **94 series · 1,332 events · 3,770 markets · 144,358 market-hours**, 30 non-mention families
+ 65 mention series, keyless candlesticks, event-clustered.
- **Venue-wide gap: −0.15¢ (se 0.93, t=−0.17, nev=1,010).** Blind taker EV: YES −2.73¢, NO −2.41¢.
  Mean spread 2.88¢, mean fee 1.52¢ ⇒ **toll ~3.0–3.5¢ round against a mispricing of ~0.**
- Full 6×7 price×time grid (42 cells): **max |t| = 2.74**, and expected max |z| over 42 null cells
  ≈ 2.7. **Nothing survives selection.**
- Zero-sum gate passes cleanly: all 20 bins sum to 0 ± 2.1¢ from the YES and NO sides independently.

🔧 **PRINT-WEIGHTING IS WHAT MANUFACTURED MISPRICING ALL DAY.** Same data, 70–75¢ bin:
**print-weighted −21.74¢ · event-clustered/market-weighted −0.53¢.**
**LAW: if a calibration surface is not event-clustered, it is not a surface.**

🔧 **THE MENTION 3¢/7.8¢ RETAIL-YES-OVERPRICE DOES NOT REPLICATE OUT-OF-SAMPLE.**
27 series held out by hash, 38 searched. Mid ∈[30,80): **SEARCH −6.16¢ (t=−2.32, nev=169) →
HELD-OUT −0.61¢ (t=−0.18, nev=108)**; series-clustered −6.13 → −1.10. Tradeable rule
"buy NO when mid ∈[40,90)": **+1.39¢ → −3.56¢**. Chronological split disagrees too.
The one bin whose sign replicated (5–10¢) is a lottery: only **15.2% of events positive**, median
event −10.2¢, mean carried by 5 events. **Treat HANDOFF §2's ~3¢/7.8¢ as IN-SAMPLE until rerun
with series held out.**

**FOUR FOR FOUR — every family's best in-sample rule is ≤0 held-out** (the `heldout_longshot.py`
kill, reproduced on four independent market structures):
| group | held out | best in-sample rule | in-sample | **held-out** |
|---|---|---|---|---|
| WEATHER | NY, PHIL | buy NO @70–80¢ | +5.01¢ | **+0.01¢** |
| METALS/ENERGY | BRENTD, NATGASD | buy NO @80–90¢ | +2.41¢ | **−4.49¢** |
| INDEX | KXNASDAQ100 | buy YES @0–5¢ | +4.63¢ | **−2.62¢** |
| SPORTS | WNBAGAME, MLBHR | buy YES @10–20¢ | +10.65¢ | **−5.72¢** |

**Liquidity is anti-correlated with apparent edge:** the two genuinely large markets (KXMLBGAME
61.3M contracts/day, KXWNBAGAME 9.2M) have the smallest/zero EXCESS; the families with the biggest
apparent calibration error are the illiquid ones. *That is what noise looks like.*

**⇒ THE ONLY POSITIVE NUMBER THAT SURVIVES ANYWHERE IS THE HALF-SPREAD TO A MAKER** (held-out
mention sell-YES-at-ask ≈ +2.97¢ gross ≈ half of the 3.59¢ spread, less the measured −0.45..−0.63¢
fill markout ⇒ ~+2.4¢ before fill probability). **That is the existing LIP/seats business.** This
survey neither adds to it nor subtracts from it, and it is NOT a taker edge.

⚠️ **POWER — the honest limit.** Kalshi hard-rate-limited at ~4,000 candlestick calls, so families
landed at ~100 events with cell SEs of **3–9¢**. **These nulls rule out ≥5¢ edges, NOT 2¢ ones** —
and 2–3¢ is exactly the size that matters against a 1.5¢ fee. Resolving ±1¢/cell needs ~10× the
events per family: a multi-day authenticated pull.
**STILL GENUINELY UNTESTED:** (a) **ECON** — KXCPI/KXCPIYOY/KXFED/KXPAYROLLS/KXU3/KXGDP have only
1–2 events in this pull; full history is 20–50 events each, one gentle pull, and release-timing is
the one wing where a structural mispricing is plausible a priori. (b) **SPORTS PROPS** —
KXMLBHR (3.2M contracts/day) plus the unsampled KXMLB{TB,HIT,RFI}: the highest-liquidity untested
surface on the venue. (c) a **maker-side** survey, which needs `λ_side · markout` per market and
cannot be done from bid/ask closes.
Artifacts: `~/kalshi_data/survey_*.{py,jsonl.gz,pkl,json}`.

## CROSS-VENUE / EVENT LANES — all dead 2026-08-06, plus a systemic API break
- **ZERO-FEE: the liquidity trigger FIRED — and it dies on CAPACITY, which is a stronger kill.**
  14 zero-fee series (not 13); 9 active. KXBTCY: **OI 6,801,825**, vol 30.2M, 28/28 two-sided,
  0.1–0.7¢ spreads on a 0.1¢ grid with **zero fees**. Priced the exhaustive MECE ladder lock on
  KXBTCY-27JAN0100 (28 bins, buy every YES → receive exactly 100¢): **SUM_ASK 98.90 = +1.10¢ lock**,
  persistent across 3 snapshots. Capacity walking all 28 books: N=1 +1.05¢ · N=10 +0.83¢ ·
  **N=100 +0.23¢ (peak $0.23 total)** · N=250 +0.09¢ · **N=500 −0.35¢**. **Max extractable ≈ $0.23,
  once, locked to Jan 2027.** The toll removal is real; the depth is not.
- **DUTCHBOOK: was real in July, correctly priced now.** Construction audited SOUND (threshold
  ordering enforced so the strike gap is a joint-WIN bonus; asks derived from opposite-side
  `/orderbook` bids, not stale summary fields; both legs verified settlement-identical — 60s average
  of BRTI/ERTI before 3 PM EDT). Historical paper Jul 22–26: 32/32 locks, median +0.62¢,
  **$23.14/day at $1,000**. **Live now: BTC net −28.09¢ (n=3), ETH −18.79¢ (n=5).** The combo's fair
  value is **100 + 100·P(joint-win band)**, not 100 — ETH prices its 3.4bp band at 15.5¢, implying
  ~8.8bp of 7-minute vol, internally coherent. Rebuild via bucket-vs-threshold: **0 locks** on all
  five coins. The watcher works; the opportunity decayed.
- **POLYLAG: DEAD ON ITS OWN GATE.** 22,486 paired obs / 1,742 minute-snapshots / 14 markets.
  Qualifying (≥3¢ Poly move) events: **0 at 1m, 0 at 5m, 0 at 15m.** Kalshi max move 0.50¢; Kalshi
  spread a flat 3.0¢ at p10/p50/p90. The divergences are **static levels, not lags** (GNEW +10.2¢,
  range 8.7–10.3 over 12 days; AOC +9.6¢; KHAR −7.9¢) ⇒ **a rules mismatch, not an information
  edge**. **Recommend killing or repointing the daemon — it is accruing zero information.**

🔧 **SYSTEMIC API BREAK — ACT ON THIS.** Kalshi **removed the integer fields**
`yes_bid`/`yes_ask`/`no_bid`/`no_ask`/`open_interest`/`volume`/`volume_24h`/`liquidity`, replaced by
`*_dollars` / `*_fp` **strings**. **49 scripts under `~/kalshi_data/scripts/` still read the old
names, silently receive `None`, and write zeros.** This produced a false "all 9 zero-fee series are
dead, OI=0" reading that was exactly backwards (KXBTCY has 6.8M OI). Spot-checked HEALTHY (they use
`/orderbook` or ticker regex): `cwing_books_*.jsonl`, `deribit_gate_hourly.jsonl`, `ens_forward.jsonl`.
**Audit anything still running before trusting its output.**

🔧 **KXBTC15M IS NOW A *RELATIVE* CONTRACT** — "BTC price up in next 15 min?", target = the **60s
average at window OPEN**, published only once the window starts (`floor_strike` is null before then).
Not a fixed pre-announced strike. Any analysis assuming a static strike is describing an old product.

## WEATHER — ☠️ DEAD 2026-08-06 (was "parked, unverdicted"). The forecast is worse than the price.
**Settlement reproduction PASSED at 99.9%** — rebuilt from raw METAR (6-hourly `1sTxTxTx` max groups
+ RMK `T`-group tenths, exact C→F round-half-up), 11 stations, 814 city-days: **813/814**.
🔧 **THE NAMED DEFECT WAS MISATTRIBUTED — correct the record.** IEM `daily.json` is **NOT** biased:
mean **−0.02°F** vs CLI, disagrees on only **2.4%** of days. The series that IS 1–2°F low on ~68% of
days is **hourly METAR spot max** (`tenths − cli`: −1 on 491, −2 on 102, −3 on 14 of 880).
**So `engine/weather.rs` was never calibrating on the wrong target, and fixing the truth series
cannot rescue anything.** (Repro by candidate: cli 99.9% · g6 99.6% · **daily.json 99.1%** · METAR
body 59.3% · RMK tenths 62.0%.)
**THE KILL — the forecast is worse than the unconditional base rate.** Brier at lead-24h, n=2,008:
**market 0.1174 · base rate 0.1516 · model 0.1553 · market+25% model 0.1186.** Blending the forecast
INTO the price **degrades** the price. Incremental-information test (10 cells, within price bin,
split on model-vs-market direction): gaps −0.1 to −10.6pp; the largest-|t| cell is **−7.3pp,
t=−2.01 — the wrong direction.**
Per-city bias is **non-stationary** (fit→OOS r=0.607, slope 0.688) and bias-correction makes OOS MAE
WORSE for DEN/LAX/NY/SFO — that is the mechanism behind "the 8-city calibration does not transfer."
The spec's 1.03–1.85°F MAEs do not reproduce against correct truth (OOS d1 MAE 2.47).
**It loses MORE when it is more confident:** OOS ¢/ct at threshold 0/8/16¢ = **−3.50 / −5.02 / −7.80**
(t −2.77 / −3.27 / −4.25). Live morning-of forward evidence (`ens_forward.jsonl`, 20 events):
**−3.52¢/ct**, predicted EV **+21.02¢** vs realised **−4.07¢** — the raw ensemble spread is
drastically too narrow. (That capture DIED 2026-07-27 and only restarted 08-06, so it is NOT the
14-day set this note previously cited.)
Gates: 1 PASS · **2 PASS with a working placebo — feeding TRUTH (illegal) yields +22.94¢, t=44.49,
so the machinery finds an edge when one exists** · 3 PASS · **4 FAIL** (half A +0.46 t=0.39 vs half B
+1.73 t=3.57; 7/11 cities) · **5 FAIL** (68-cell grid, best t=1.98, permutation **FWER p=1.0000**) ·
6 PASS · 7 PASS (retrieved vs unretrieved P(yes) differs only +1.43pp, vs the 8–14pt trap elsewhere).
Best cell = +0.90¢/ct = 0.93%/trade, 6.42 fills/day, **$9.36/day at $1,000**, capacity ceiling
≈$3,400/turn — **capacity is not the constraint, the edge is.**
ADJACENT: **deep-favourite taker** 16 cells all "not established" (95% binomial UB on the loss rate
exceeds break-even everywhere; the apparent t=6–8 was a zero-variance-tail illusion).
**Hourly temperature** (`KXTEMP*H`, 14,868 settled markets): NO-taker at ≥98¢ is −0.66¢ to −3.45¢;
the 723k contracts of ≤2¢ late flow is real but 406k is taker-side YES, so the pocket is
**maker-only at 98–99¢ where queue position is unmeasurable**; 53% rate-limit-truncated so gate 7
cannot be cleared there.
🔧 **NEW PSEUDO-REPLICATION INSTANCE:** a row-pooled band scan showed **+5.5¢/ct net at the ask**
(60–75¢ favourite band); with **one fill per market and event-clustered t it inverts to −4.14¢
(t=−1.32)**. Pooling every hourly snapshot weights each market by how long it STAYED in the band,
which is outcome-correlated. Same family as the print-weighting law.
⇒ **Archive `implementation/01 - Weather Sleeve Spec.md`.** Its central claim — bias-corrected
early-entry forecast beats the market before it converges — is refuted.
Artifacts: `~/kalshi_data/wx_*.{json,pkl,jsonl,csv}`, `scripts/wx_*.py`.

## 🔑 THE RATE LIMIT WAS SELF-INFLICTED (2026-08-06) — this reopens the venue survey
**Keyless:** ~11 of 12 rapid calls return 429; ~4 successful pulls/min. That is what capped the
venue-wide survey at ~4,000 calls and left cell SEs of **3–9¢**, i.e. able to rule out ≥5¢ edges but
**not 2–3¢ ones — exactly the size that matters against a 1.52¢ fee.**
**Signing the same read-only GETs with the existing prod key: 500 calls/min sustained, 0× 429 across
~35,000 calls. ~100× throughput.** That is why the econ/props lane pulled 33,097 markets where the
survey managed 1–2 events per econ series.
**⇒ ALL future calibration work must authenticate. The venue survey should be re-run at full power
before its "calibrated to ~1¢" verdict is treated as final at the 2¢ scale.**

## ECON RELEASES — ☠️ STRUCTURALLY DEAD 2026-08-06. There is no repricing moment.
**Every scheduled-release series freezes trading BEFORE the print** — not just INFLATION-FLASH:
KXCPI closes 12:25Z vs a 12:30Z release (**−5 min**) · KXCPIYOY 12:29Z (**−1 min**) · KXFED 17:55Z
vs 18:00Z (−5 min) · KXPAYROLLS/KXU3/KXGDP all 12:29Z (−1 min). Confirmed from on-disk prints:
20,585 CPI trades, **last print 12:28:57.878Z, zero after.** `rules_secondary` says it outright:
*"The market will always close at 8:25 AM ET on the scheduled day of the data release."*
**There is no T+5m / T+1h axis to build. The front-run and the reaction are both structurally
impossible.** Residual pre-release calibration is unreachable: `/events` lists 61 CPI events back to
2023, but `/markets?event_ticker=` and `/events/{t}?with_nested_markets=true` both return **0 markets**
for anything older than ~2 months (verified authenticated, two endpoints). Usable: **11 event
clusters, 154 obs, SEs 5–30¢, MDE ≈15¢.** Closed; no amount of compute reopens it.

## SPORTS PROPS — ☠️ DEAD 2026-08-06. All five families null; capacity was never the constraint.
Anchor = scheduled first pitch parsed from the event ticker (outcome-independent by construction;
**`close_time` here IS outcome-dependent — YES closes ~117s later than NO — and was correctly
avoided**). Both-sides taker EV after real fees sums to −(spread+2·fee) **to the cent** in all five:
KXMLBHR −2.11/−0.19 · KXMLBTOTAL −2.28/−1.30 · KXMLBSPREAD −1.30/−2.40 · KXMLBTB −1.72/−3.20 ·
KXMLBRFI −0.87/−3.72. Best cell **KXMLBHR NO: −$1.07/day on $1,000** [−7.65, +5.50]; the rest
−$15 to −$37/day. KXMLBHR trades 3.0M contracts/day — **the edge is what is missing, not depth.**
Gates: 1 PASS · 2 PASS · 3 PASS · 4 PASS-as-kill · 5 PASS-as-kill (family-wise p 0.207–0.999) ·
6 ENFORCED · 7 CAUGHT A FALSE POSITIVE · 8 CAUGHT THE SAME ONE · 9 PASS.

🔧 **TWO MORE OUTCOME-LEAKING FIELDS — add to the field-hygiene list:**
- **`volume_fp` is measured AT SETTLEMENT.** A "low-volume NO" cell read **+4.41¢, t=+3.00**; with
  causal pre-anchor volume it is **+0.60¢, t=+0.52**. Low total volume ⇒ nothing happened in-game ⇒
  realized YES 6.5% vs 12.5%. Pure outcome leakage.
- **`last_price_dollars` on a settled market is POST-RESOLUTION.** In KXMLBHR, 99¢ appears exactly
  298 times = exactly the YES count. It is not a pre-close price.
- **Ladder size is outcome-dependent** — Kalshi adds strikes intraday, so "ladder-depth strata"
  showed +40¢ gaps; on pre-game-listed markets only, every game collapses into one stratum.
Artifacts: `~/kalshi_data/hunt/` (`kauth.py`, `ka.py`, `pull_all.py`, `drive.py`, `an_*.py`,
`K_*.jsonl` candles, `M_*.jsonl` markets — 33,097 markets, 1-min bid/ask, resumable).
