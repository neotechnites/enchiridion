# B9 widened re-test — metal+gas Mon-Wed wing-sell at ~10wk scale

Date: 2026-07-25 · Lane BEZOS night build+verify · Decision document for correcting the LIVE unified volbook.

**Bottom line up front:** At full-retention (~9.5wk) scale, event-weighted, fees in, the B9 edge **survives for METAL and collapses to ~zero for GAS**. Burst-2's headline "metal+gas Mon-Wed, both era-robust" was half right: metal is rock-solid and era-stable; **gas's edge does NOT replicate** once you weight by event instead of by rung and use a common era split. Oil is weakly positive throughout (not the era-death burst-2 reported). **Live-volbook correction: concentrate wing-sell weight on METAL dailies Mon-Wed (gold+silver primary, copper half-weight); cut GAS to a small monitored sleeve; keep OIL minimal.**

---

## Corpus (widened, reconstruction-grade)
Trades endpoint, T-3h last-print +1c entry, fee 0.07·p·(1−p)/contract, one obs/rung, wing band y3∈[0.05,0.35), age≤4h.

| series | family | rows | dates | span |
|---|---|---|---|---|
| KXGOLDD | metal | 1309 | 39 | 26MAY18–26JUL23 |
| KXSILVERD | metal | 1347 | 39 | 26MAY18–26JUL23 |
| **KXCOPPERD** (new) | metal | 825 | 39 | 26MAY18–26JUL23 |
| KXNATGASD | gas | 1687 | 31 | 26JUN02–26JUL23 |
| KXBRENTD | oil | 862 | 39 | 26MAY18–26JUL23 |
| KXWTI | oil | 902 | 47 | 26MAY18–26JUL24 |

**Retention floor reached.** Corpus starts 26MAY18; today −10wk ≈ 26MAY16. Trades older than that are purged, so this IS as far back as reconstruction allows — the window cannot be widened further, only accrued forward (that is exactly what the B4 harvester now does). Widening vs burst-2 = added COPPER (a 3rd metal series, genuine within-retention breadth) + topped the tail to 26JUL23/24. NatGas only spans from 02JUN (series listed later) → gas H1 is structurally thin.

Method change vs burst-2 (why numbers move): **event-weighting.** One datapoint per settlement event = mean fade-NO pnl over that event's wing rungs, then average across events. Burst-2 averaged per-rung, which inflates n and lets a few fat within-day-correlated rungs dominate. `nd` = distinct dates, `ne` = distinct events = the honest n. Global era split @ 2026-06-22 (median of the pooled dates), applied identically to every family.

## Fade-NO EV (¢/contract, event-weighted)

| cell | metal | gas | oil |
|---|---|---|---|
| Mon-Wed ALL | **+8.61** (ne=87, nd=29) | +0.08 (ne=22, nd=22) | +2.78 (ne=58, nd=29) |
| Mon-Wed H1 | **+8.52** (ne=42, nd=14) | −13.18 (ne=7, nd=7) | +4.22 (ne=28, nd=14) |
| Mon-Wed H2 | **+8.69** (ne=45, nd=15) | +6.27 (ne=15, nd=15) | +1.44 (ne=30, nd=15) |
| Thu ALL | +5.49 (ne=27, nd=10) | +1.67 (ne=8, nd=8) | +0.80 (ne=20, nd=10) |

## Wing calibration gap, Mon-Wed (implied = mean y3; realized = touch rate) — the clean, direction-neutral evidence
| family | implied touch | realized touch | GAP |
|---|---|---|---|
| **metal** | 13.7% | 3.6% | **+10.1pp** |
| gas | 13.3% | 5.3% | +8.1pp |
| oil | 13.6% | 10.1% | +3.6pp |

## Per-metal-series Mon-Wed (asset split — does copper corroborate?)
| series | EV | touch |
|---|---|---|
| KXGOLDD | +9.79 (nd=29) | 3.0% |
| KXSILVERD | +11.06 (nd=29) | 2.0% |
| KXCOPPERD (out-of-sample) | +4.98 (nd=29) | 6.9% |

---

## Verdict by family

**METAL — HOLDS. CONDITIONAL, deployable, era-robust.**
Wings priced 13.7% touch, realize 3.6% → +10.1pp structural overpricing. Fade-NO EV **+8.5/+8.7¢ in BOTH era halves** (nd=14/15) — the cleanest era split in the corpus. Corroborated on **two independent honest splits**: era (both halves +8.5/+8.7) AND asset (gold +9.8, silver +11.1, copper +5.0 — all positive). Copper is a genuine out-of-sample series and confirms the sign, though at ~half magnitude with a higher 6.9% touch → gold+silver are the core, copper the weaker corroborator. Named mechanism intact: thin books quote a ~flat ~13% OTM daily premium blind to how small precious-metal Mon-Wed realized moves actually are. **This is the durable edge.**

**GAS — DOES NOT HOLD at widened scale. Downgrade.**
The calibration gap is real (+8.1pp, realized 5.3% << implied 13.3%), but the **tradeable EV does not survive**: event-weighted Mon-Wed EV is **+0.08¢ (≈ zero)**, and the era split flips hard — **H1 −13.18¢ (nd=7, thin), H2 +6.27¢ (nd=15).** Burst-2's "gas +5.5/+7.6 both eras" was an artifact of (a) per-rung weighting and (b) a gas-specific self-split; under event-weighting + a common split it evaporates. A few low-priced wings that DID settle YES cost enough when you're short them to erase the gap, and gas's late listing (from 02JUN) leaves H1 at nd=7 — too thin to bank in either direction. **Not bankable as a standalone wing-sell.** [thin-n caveat: gas H1 nd=7 is under the n≥60 / one-honest-split bar — treat H1 as indeterminate, not confirmed-negative.]

**OIL — weakly positive, NOT the era-death burst-2 claimed.**
Widened + event-weighted, oil Mon-Wed is **+4.22¢ H1 / +1.44¢ H2** — both positive, small. Burst-2's "oil dies era-2, +7.2→−5.4" does NOT replicate; that figure was per-rung and split-sensitive. But oil's calibration gap is only **+3.6pp** (wings ~fairly priced, realized 10.1% touch is close to implied 13.6%) — consistent with the Mesh prior that oil's larger realized amplitude makes its wings fair, not fat. So oil is a marginal-positive, low-edge sell, not a strong one and not a disaster.

**Placebo note (honest):** the interior-band (0.42–0.58) fade-NO EV was intended as the null but is **not a clean zero** — it is dominated by directional sample tilt (metal interior +10.0 because the sample resolved NO-heavy; gas −14.7, oil −7.7 because those resolved YES-heavy). Fade-NO EV carries a directional component whenever a sample resolves lopsidedly, so I do **not** lean on the interior-EV placebo. The direction-neutral evidence is the **calibration gap** (implied vs realized touch), which cleanly separates metal (+10.1pp, structural) from oil (+3.6pp, near-fair).

---

## Does the metal+gas Mon-Wed gate hold at ~10wk scale? (nd per cell)
- **metal: YES.** +8.61¢ Mon-Wed, era-robust +8.52/+8.69 (nd=14/15), asset-robust (3/3 series positive). n honest: nd=29, ne=87.
- **gas: NO.** +0.08¢ Mon-Wed event-weighted; era-fragile (H1 −13.18 nd=7 / H2 +6.27 nd=15). The gate as written ("metal+gas") is carried entirely by metal.
- Combined metal+gas Mon-Wed +6.89¢ is metal diluted by gas — do not read the pooled number as validation of gas.

## LIVE unified volbook — concrete correction
Keep wing-sell weight ONLY where the edge is era-robust and n-supported:

1. **METAL dailies, Mon-Wed — KEEP / CONCENTRATE.** Gold + silver at full wing-sell weight; **copper at ~half weight** (positive but weaker, higher touch, single ~9wk series). This is the one family that earns top wing-sell ranking.
2. **GAS dailies — CUT to a small monitored sleeve** (was carried as a co-equal of metal). Edge is ≈0 and era-unstable at 10wk scale; do not size it like metal. Re-evaluate as the B4 corpus accrues forward — if H2's +6¢ persists over the next 4–6 weeks it can be restored, but on current data it is not bankable.
3. **OIL dailies — KEEP MINIMAL, do NOT zero.** Weakly positive both eras (+4.2/+1.4) but wings near-fair (+3.6pp gap); rank it below metal, never as a top sell. (This softens burst-2's "drop oil entirely" — oil is low-edge, not negative.)
4. **Weekday gate: Mon-Wed only.** Thursday keeps a residual metal +5.5¢ but touch jumps to 10.1% (the 8:30ET jobless-claims dollar/rates channel) → smaller, noisier; keep Thu off or tiny. Fri excluded.
5. **Ranking principle corrected:** rank the wing-sell by **calibration gap (implied−realized touch)**, not by realized amplitude. Metal +10.1pp >> oil +3.6pp. Amplitude-ranking is backwards for the SELL side, as burst-2 flagged — this widened test confirms it via the gap directly.

## What's unverified / to watch
- ~9.5wk, nd=29 Mon-Wed for metal — solid but not the 2yr confirm. Label **CONDITIONAL**, not TRADE. B4 harvester now accrues forward to reach 2yr without waiting.
- Gas H1 nd=7 is below bar → gas verdict is "not confirmed positive," not "confirmed dead." Watch H2's +6¢ forward.
- Copper is one series, ~9wk — sign-confirming only.
- Rungs are within-day correlated; nd/ne are the real n (reported per cell).

## Files
- `~/kalshi_data/scripts/bezos_b9_widened.py` — the re-test (event-weighted, era-split, calibration gap, per-series)
- `~/kalshi_data/cwing_obs_KXCOPPERD.jsonl` — new metal series (825 rows)
- `~/kalshi_data/scripts/cwing_topup.py` — dedupe-appender used to widen without overwriting history
- `~/kalshi_data/scripts/settle_harvest.py` — B4 harvester (JOB 1; feeds the forward 2yr confirm)
