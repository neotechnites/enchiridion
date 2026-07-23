# 07 - Overfitting & Validation Discipline

> ⚠️ **HOW THIS NOTE COEXISTS WITH THE KILL TAXONOMY (note 15, 2026-07-23) — they are two halves of one machine, and a future Claude must hold both without confusion.** THIS note governs whether an effect is REAL (vs luck/overfit). The TAXONOMY governs the VERDICT once you know. Composition: (1) Test-to-kill stays ruthless — placebo cells, OOS, split-half exist to execute FAKE edges fast; that speed IS the funnel; nothing here is softened. (2) A REAL effect that fails unconditionally but works in a slice is NOT dead — conditional → mandatory gate-hunt ('the conditions should always find a way to be met' — Ryan). (3) **The rescuing gate must itself clear THIS note's bar**: named mechanism, its own placebo/split, ideally cross-asset/era — a gate found by sweeping 50 conditions is the 9-of-10 coin all over again. So: kill-testing targets the CLAIM, the taxonomy classifies the CORPSE, and gates get the same trial as any edge. Live proof both halves work together: gas-trend, calm-clock, streak-≤44¢ = real gates that cleared the bar; vol-filter, depth-4 = fake gates this note's defenses rightly executed.

**The single most important methodology note for this project. Read before adding any new feature/indicator.**

## What overfitting means
Overfitting = your rule has learned the **random noise** in your sample instead of a **real, repeatable pattern**. It looks brilliant on the data you tested it on, then falls apart on new data — because the noise it memorized won't happen again.

Core idea: **every dataset is real signal + random luck mixed together.** Search hard enough and you'll find a rule that fits *both*. The part that fit the luck is worthless going forward.

## The coin-flip intuition
Flip 1,000 coins 10 times each. By chance, some land 9/10 heads. Concluding "those coins are biased!" and betting on them loses — the 9/10 was luck, not a property of the coin. **The more rules/coins you test, the more fake "edges" you find.** (This is "multiple comparisons.")

## Where WE did exactly this (cautionary record)
- **Volatility filter:** looked amazing at 66.7% win on n=276 → on the full sample **51.8%, gone.** The classic "9-of-10 coin."
- **Streak depth:** depth-4 spiked to 57.5% while 5 & 6 were coinflips — non-monotonic spike = noise fingerprint.
- **The ≥8 breadth threshold:** picked because it looked best *in the sweep*. Choosing the best-looking cut out of several inflates it — part of the +12%/trade is selection luck, so we discount it.

## Why this project is especially exposed
- **Small sample** (Kalshi = 2 months; ≥8 signal n=135).
- **We tried ~20 strategies / many thresholds / many filters** — every extra try is another coin flipped, more chances at fake gold.
- **A thin true edge** is easily mimicked or masked by noise.

## The defenses (mandatory before trusting anything)
1. **Out-of-sample test** — build the rule on one period, test on another it never saw. Held → probably real. Died → overfit. This is the gold standard.
2. **Split-half** — must hold in *both* halves independently. (Breadth did: 57.1% / 57.7%. Vol/depth did not.)
3. **Use Polymarket's 50k-market / 6-month data as the LAB** (don't trade it — learn on it). More data → noise averages out, only real patterns survive. Validate features there, port survivors to Kalshi.
4. **Add features one at a time**, keep only those that hold OOS. Regularize. Walk-forward.
5. **Forward paper-trade** — the ultimate test; the only thing that fully exposes real-vs-memorized.

## CASE STUDY (2026-06-25): the favorite-underpricing edge — how within-sample split-half fooled us
- Found: at 180s-before-close, 80–90¢ favorites won ~93% (priced ~85¢) → +6–7¢ net at low fee. Looked like the best edge yet.
- It passed FOUR checks: tick-weighting (one/market), staleness (fresh prices), **split-half**, and up/down symmetry. All looked great.
- **But all four were inside the SAME ~30-day window** (`all_ticks` = May25–Jun24). The split-half was two halves of one month = within-sample, NOT out-of-period.
- **True out-of-period test (older Apr18–May24 @180s, `fav_oos.py`): it COLLAPSED.** 80–90¢ went −4.7¢ (up) / −0.2¢ (down); 90–95¢ down flipped to −1.3¢. Only 95–99¢ stayed weakly positive (~+0.5–0.9¢, too thin to trade).
- **Lesson: within-month split-half ≠ out-of-sample.** Both halves shared the same favorable regime. The +6–7¢ was a recent-month artifact. Only a *different period / different platform* is a real OOS test.
- This is why **breadth-reversal is more trustworthy** — it held on Polymarket's independent 6-mo/50k data, a genuinely separate dataset. Favorite edge held only within one Kalshi month.

## STRUCTURAL LESSON (2026-06-25): "cheapness is information" — why off-50 directional edges are hard
Tried to take a validated OUTCOME edge (gold×BTC-drop predicts UP ~59% at the open) and enter it OFF-50 by waiting to "buy UP on a dip" to ~35¢. Result: **win 0% — catastrophic.** Reason: when the UP side dips to 35¢, it's because BTC *genuinely fell* → the market resolves DOWN → UP loses. The cheap price *is the market correctly pricing that the prediction is failing this time.* The conditional probability flips from 59% (at open) to ~0% (after the dip).
- **Implication:** an off-50 directional edge CANNOT be "predict the outcome, then buy the predicted side cheap." It must be either (a) buy the side that's *winning* (favorite, underpriced) — but favorite calibration failed OOS — or (b) a **price-movement scalp** (capture a move, not the outcome). Directional outcome edges live at ~50¢ (at the open, before the market reveals direction); they can't be relocated off-50 cheaply.
- This is why every attempt to move the breadth/gold outcome edges off-50 failed, and it narrows the search for Ryan's off-50 Kalshi edge to: favorite-underpricing (failed OOS so far) or a scalp.

## TESTED (2026-06-25): cutting losers early does NOT help an outcome edge
Idea: on the gold edge, cut losers near close (only when BTC clearly off-target / contract at a low extreme) to boost earnings. Backtested (n=135, `cut_losers2.py`): **every cut variant LOWERED EV and compounded return** vs hold-to-settlement ($246 baseline → $126–223 cut), variance ~unchanged. Why (fundamental):
- Martingale: in a calibrated market the price already = fair estimate of settlement → exiting early is EV-neutral at best.
- You pay an **exit fee** → EV-negative.
- The edge wins 58%, so "losers" near close **recover often** — cutting forfeits those (cuts winners-in-disguise); the more aggressive/earlier the cut, the worse (≤45¢ cut → worst, $126).
- Negligible variance gain (clear losers are already near-binary by cut time).
- **Conclusion: hold outcome bets to settlement.** Can't improve a fair bet by exit-timing; only smaller sizing reduces the (intrinsic) drawdown.

## The honest status this implies
- **Direction of the edge (breadth reversal): validated** — survived fresh data (both Polymarket halves, 5 assets). Real.
- **Exact magnitude (+12%/trade Kalshi ≥8): partly in-sample** — measured on the same data we tuned, so some is coin-flip luck and will shrink live. Only forward paper-trading settles how much.

Related: [[00 - START HERE]], [[02 - The Confirmed Edge - Streak Reversal]], [[06 - Sizing & EV Math]].
