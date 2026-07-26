# STEER: Two-year regime map of streak behavior (2026-07-26, Ryan's fear, made testable)

Ryan verbatim intent: "bitcoin is very possibly at the bottom of its price right now, and
will only go up, that comes with far more streaks no? is it just that the regime is about
to flip and were about to eat all those loses becasue long streaks keep coming?"

Mission: from ~2 years of BTC + ETH 15-minute candles (free public source — Coinbase/
Binance; document which), reconstruct synthetic window win/lose series (close vs open per
15m bar ≈ the settle rule; state the approximation honestly — real settle is a 60s avg vs
the open strike) and measure, BY MACRO REGIME:
1. Streak frequency and length distribution (does a bull leg lengthen streaks?).
2. Post-4-streak reversal rate (the strategy's engine) per regime.
Regime definitions PRE-NAMED (no slicing until it glows): trend = sign+slope of 30d MA;
drawdown-from-ATH bands; realized-vol terciles. Report the reversal rate in each cell with
n and CI, plus the 2024-2026 era series (the 2024 bull run and 2025 chop are the natural
experiments). Key deliverables:
- Does ANY regime cell push post-4-streak reversal materially below ~50% (the fade's
  price-adjusted floor)? That is the "eat the losses" scenario quantified.
- Where does July 2026 sit in regime space, and does the regime explain the measured
  June 57.6% → July 53.3% decay better than competition does?
- If a regime gate is warranted, its entry-time-measurable definition + cost in signals.
Cross-check the synthetic series against the real 67d Kalshi corpus overlap (May-Jul 2026)
— the synthetic reversal rate there must reproduce ~56% or the approximation is broken
(calibrate before trusting the 2yr numbers).

Traps: candle lookahead (never use a bar's close before its end), note-03 (BTC/ETH share
regime — report per-asset), note-07 (placebo: shuffled-regime labels must kill any gate).
→ work/verify-regime-map.md (commit, no push). One lane, no subagents, no nestor changes.
