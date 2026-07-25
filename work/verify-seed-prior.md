# Lane 8 — SEED-PRIOR feasibility ledger (2026-07-25)

Extends wave-1 Lane-4 finding: new mention-grab series open at a uniform **0.54 YES / 0.46 NO** seed
that ignores per-word base rates. This lane asks: from SETTLED mention history, how far is the seed
from realized YES rates, and what per-market edge would a seed-hitter with an external prior capture?

## Data & method
- Source: public Kalshi API `/markets?series_ticker=…` (no auth). Iterated all **397** series in
  category `Mentions`. Filtered to markets with `result ∈ {yes,no}` (API status field = "finalized";
  note: `status=settled`/`finalized` query filters return 0 — must pull unfiltered and filter locally).
  urllib fails SSL here → pulled via `curl` subprocess; 6 workers + retries → 0 fetch failures.
- Resolved markets found: **504** across **11** series (rest of the 397 are upcoming/one-off, not yet
  settled). Dropped 12 "Event does not qualify" admin rows → **N=492** word-markets.
- Seed benchmark = 0.54 (wave-1). Live `*_fp`/`*_dollars` fields read **None** on the markets endpoint
  for all sampled open markets (earnings, Maddow, TrumpSay) → cannot reconstruct the seed for
  already-finalized markets; 0.54 is imported from wave-1's direct listing observation.

## Realized vs seed — full mention universe (N=492, 11 series)
- Overall realized YES = **0.417** (205/492). The 0.54 seed is systematically **too high**.
- Blind buy-YES @ seed: **−12.3¢/contract** gross. Blind buy-NO @ (1−seed): **+12.3¢** gross (**+10.3¢** after ~2¢ fee).
- Per-word: 202 distinct words, 62 repeat ≥3×. **|realized_wordrate − seed|: mean 0.332, median 0.373, p90 0.540.**
  The flat seed is nowhere near per-word truth.
  - Low base-rate words (seed way too high): iran 0.00 (n6), shakira 0.00 (n5), trump 0.08 (n12),
    israel 0.11 (n9), tariff 0.17 (n6), crypto 0.20 (n5), oil/gas 0.25 (n12), AI 0.31 (n16).
  - High base-rate words (seed too low): var 1.00 (n5), record 0.80 (n5), election 0.75 (n8),
    corrupt 0.58 (n12), penalty-kick 0.60, zlatan 0.60.

## Leave-one-out external-prior backtest (N=492)
Prior for each market = YES rate of the SAME word across all OTHER settled markets (needs ≥2 other obs;
n=327 markets qualify). Decision: prior≥0.5 → buy YES@seed, else buy NO@(1−seed). Gross ¢/contract:
| policy | EV/contract | n | note |
|---|---|---|---|
| blind YES @ seed | **−12.3¢** | 492 | seed too high |
| blind NO @ seed | **+12.3¢** | 492 | fade the seed |
| LOO word-prior | **+11.3¢** | 327 | dir-correct 0.61 |
| prior, |prior−seed|≥0.15 (confident) | **+20.9¢** | 237 | +18.9¢ after 2¢ fee |

The prior barely beats blind-fade on the full universe (both ~+11–12¢) because the dominant signal is
just "average base rate (0.42) < seed (0.54)". Selectivity (confident subset) is where the prior adds:
+20.9¢ gross on the half of markets where a repeated-word prior diverges ≥15¢ from the flat seed.

## CRITICAL caveat — seed only CONFIRMED for the earnings family
The 0.54 seed was directly observed (wave-1) only on **KXEARNINGSMENTION*** (earnings-call grab). The
big fade edge above is dominated by non-earnings families (MADDOW 0.32, WARSH 0.31, POWELL 0.29) whose
opening seeds are UNVERIFIED — if they seed at their own base rates the fade edge is illusory for them.

Earnings-mention subset (CONFIRMED 0.54 seed; n=76, 5 events: CRWD .65, DELL .35, INTC .27, C .71, ALK .54):
- realized YES = **0.500** exactly → blind fade edge only **+4.0¢** gross, **~break-even to −** after 2¢ fee.
- i.e. on the one family where the seed is real, the seed is only mildly biased and the aggregate edge
  is fee-marginal. Per-word prior is the only lever but earnings per-word sample is too thin to trust.

## Verdict: CONDITIONAL (not dead) — two gates
1. **Seed-reality gate:** confirm 0.54 seed applies to the target family via a sub-20-min capture at
   listing (wave-1's open prereq). Only earnings-grab is confirmed today; its edge is thin (+4¢).
2. **Prior gate:** the flat seed misses per-word truth by median 0.37 → an external word base-rate prior
   is mandatory. A leave-one-out word prior captures +11¢ (all) to +21¢ gross (confident, |dev|≥0.15, n=237).
Feasibility is REAL where both gates hold, but the fat numbers rest on the unverified assumption that
non-earnings mention families also seed at 0.54. Fill-at-seed and mid→bid slippage are unmodeled (R104:
all P&L is hypothetical, no fills reconstructed). Sample is 11 settled series — a design green-light,
not a proven edge.
