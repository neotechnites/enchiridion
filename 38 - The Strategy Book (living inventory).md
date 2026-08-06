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
- **STREAK — DEAD 2026-08-05 AT TRADEABLE PRICES. Do not run at $1,000.**
  **LIVE MONEY: STREAK HAS LOST −$65.22, profitable in 5 of 19 settled windows**
  (source `/home/ubuntu/all_setts.json`, verified). Jun 24 early config −$39.13 (1/3);
  **Jul 24–29 live era −$26.09, 4/16 = 25% win rate against a 41.7% breakeven** — and that era
  is entirely AFTER the 2026-07-25 retry binary, so **the retry did not help.** Last fill
  2026-07-29, nothing since. **The old "$106.03 banked" line is not this strategy's P&L and no
  source for it could be located** (there is no `state.json` on the VPS). *(An intermediate
  claim of "+$3.20 / state.json.peak / 40% win / bankroll $78.94" was itself unverified and is
  FALSIFIED — do not cite it. Recorded because it was briefly written here.)*
  **Live is WORSE than the backtest and in the same direction** — 25% live win rate vs the
  recent backtest's 39.8% — so the kill rests on real money, not a model.
  Measured at prices volume actually crossed, net of real fees, day-clustered:
  Jun 25–Jul 22 **+6.44c, t=+1.48** (n=221, CI [−2.10,+14.98]) · **Jul 20–Aug 6 −2.95c,
  t=−0.91** (n=123) · pooled +3.08c, t=+1.03. **It never cleared t=2 in any period** — this is
  "never established, now negative", not "was real, then died". Independent book-tape method
  agrees: −1.09c (n=27). Recent weekly EV monotone negative: −1.81c, −2.52c, −6.51c.
  **The underlying reversal rate broke:** 4-streak reversal 55.27% (n=474) → **48.28%**
  (n=408) at Jul 20, **z=+2.07**, replicated independently on SOL/XRP/DOGE (54.85% → 50.46%,
  z=+2.04). Last three weeks all below 50%. The vault's 55.8%/56.0% anchors are stale.
  **Our own fills corroborate the kill** — live 25% (4/16) against the recent backtest's 39.8%
  (n=123). The backtest is if anything optimistic; nothing ever predicted the vault's 54.7%.
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
  SECOND INDEPENDENT DEFECT — the price source: live orderbook capture measured `/markets`
  reading LOW vs the real book by median +1.30¢ in 93–97¢ (p90 +8.0¢; **29% of moments exceed
  the entire claimed edge**) and +5.00¢ in 90–93¢ (61% exceed 3¢). Corroborated on the tape by
  next-print drift (median +0.8¢ at 95–97¢, +1.0¢ at 90–93¢, cheap bands worst).
  **THE STRUCTURAL KILL (independent, and the cleanest — live books, 2026-08-06).** The
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
