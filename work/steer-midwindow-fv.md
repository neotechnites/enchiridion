# STEER: Mid-window fair-value dislocation scan (2026-07-26, Ryan's go)

Mission: build a fair-value model P(settle above strike | spot, distance, time-left, vol)
for the 15m crypto windows and scan the captured books for exploitable dislocations at ANY
minute of the window — generalizing the streak edge's "we own a better number" from T0 to
the full window. This is the RENTEC coin-trajectory door (Book column 3).

Priors to test against, not assume (verify-streak-conditioning.md + tonight's cells):
mid/late marks measured calibrated-to-cheap (sell side dead); unconditional cheap-side
buying dead; LOCK (buy confident side late) decayed dead LAST MONTH — your scan must not
re-derive LOCK and call it new; check anything found against `nestor backtest-lock`'s
kill-scan window and the graveyard (latency plays dead — book reprices in 1.9s; buy-the-
jump dead).

Data: ~/kalshi_data — strikes-as-spot series ({S}_mkts_full.json, lookahead-free, proven
by the conditioning lane), obs logs (1-12s asks), kbt books (churn-filter per R13),
ws_divergence tape; mid-window spot gaps fillable from free Coinbase candles (say so if
used, lag properly — 60s candle lookahead trap). Fees in, spreads in (books are 1-2c wide
mid-window), entry realism = cross the spread, no lookahead anywhere.

Deliverable → work/verify-midwindow-fv.md (commit, no push): model spec + calibration
table; dislocation frequency/size/duration distribution net of spread+fees; honest verdict
(TRADE-shaped / capture-gated / DEAD-with-numbers) + what capture is missing if gated.
Note-07 discipline throughout; one lane, no subagents, no nestor changes.
