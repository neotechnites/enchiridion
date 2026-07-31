# 37 - Sliding Capital Allocation (concept spec — drafted at strategy #2, 2026-07-25)

> ⚠ PARKED (pre-LIP era, 2026-07-25). Concept for nestor's multi-strategy capital shares; never
> ruled on; not part of the LIP lane. Referenced notes 24/15 live in git history.
>
> Promised when the second strategy arrived (note 24). CONCEPT for Ryan's verdict — no code until
> he rules on it. Purpose: replace binary live/dead strategy decisions with earned capital share,
> so the portfolio reallocates continuously the way the kill taxonomy (15) says ideas should.

## The concept (derivation)
Goal: maximize $/day for the whole bankroll. A strategy is never simply "on/off" — it holds a
CAPITAL SHARE that slides with evidence. Money flows toward measured live edge and away from
decay, automatically, without anyone declaring death (binary kills threw away conditional edges;
shares make "mostly dead" = "small share," recoverable by performance alone).

## Mechanism sketch (for review, not prescription)
- Each strategy s gets share w_s of deployable bankroll; risk layer enforces (per-strategy daily
  budget = w_s × daily cap — the existing Flat/Fraction machinery already meters spend per day,
  so shares become per-strategy budget multipliers, a small risk.rs extension).
- w_s slides on EVIDENCE-WEIGHTED live edge: realized $/day per $ allocated over a decaying
  window (live fills only — R104: reconstruction, never inference; paper feeds eligibility, not
  weight), shrunk toward the backtest prior when n is small (new strategies start at a FLOOR
  share, not zero — they must be able to earn their way up).
- FLOOR (e.g. one probe-unit/day) keeps every non-STRUCTURAL-dead strategy measurable — a
  strategy with zero capital produces zero evidence and can never revive; the floor is the cost
  of keeping the hypothesis alive. CEILING caps any one strategy's share (concentration risk +
  capacity limits are real: streak ~$4/trade×5-6/day, volbook rung depth).
- Rebalance cadence: daily at the day boundary (matches existing day accounting); no intraday
  share changes (avoids feedback churn on tiny n).
- Kill taxonomy mapping: STRUCTURAL dead → share 0 (out). CONDITIONAL → floor share while gate
  unmet, normal sliding when met. DECAY → share decays with the evidence window, no declaration.

## Open questions for Ryan (the actual decisions)
1. Window + shrinkage aggressiveness (fast reallocation vs stability — concept: slow while total
   bankroll < $1k, faster later).
2. Floor/ceiling numbers.
3. Does streak's current 100% share simply become "100% minus volbook's starting floor" on
   volbook's live day?
