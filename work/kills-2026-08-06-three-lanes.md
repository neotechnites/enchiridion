# Three lanes killed on data — 2026-08-06 (MT afternoon)

All three were run with the six guards the vault has paid for: executable prices only,
two-sided-book requirement, `yes_bid+no_bid<=100` staleness filter, event-clustered
t-stats, fixed-close anchoring, and no conditioning on the trade being possible.

## 1. DERIBIT CROSS-VENUE GATE — DEAD (and it was negative, not merely zero)
Source: `~/kalshi_data/deribit_gate_hourly.jsonl`, **forward-logged since 07-22** by
`scripts/deribit_gate_hourly.py` — pre-registered, so no backtest lookahead is possible.
28,881 resolved rows, **662 distinct events**, BTC+ETH hourly ladders. Rule as logged:
flag rungs where |Kalshi mid − Deribit-implied| − fee > 2c, fade the gap.

| cut | clustered EV/contract | t |
|---|---|---|
| ALL | **−0.54c** | **−4.21** |
| btc | −0.88c | −4.93 |
| eth | −0.20c | −1.09 |
| first half / second half (1c haircut) | −1.67c / −1.37c | −8.67 / −8.71 |

**Fading the professional options market's disagreement LOSES money, significantly, in
both halves.** 80% of rows sit at kmid<5c, where it is −1.03c with t=−47: the gate keeps
telling us to BUY the cheap wing because Deribit says it is underpriced, and reality says
Kalshi's 3c is still too dear. That is LIP STATEMENTS 9a replicating on an independent,
forward-collected universe. **Do not rebuild a cross-venue fair-value gate on this venue.**

## 2. SELLING THE CHEAP WINGS AT EXECUTABLE PRICES — DEAD (9a is inside the spread)
Source: `cwing_books_KX{BRENT,GOLD,SILVER,COPPER,NATGAS}D + KXWTIH.jsonl`, 3-min full-ladder
capture since 07-26. **703,544 two-sided snapshots, 5,413 rungs, 5,007 settled (92%).**
Trade tested: **sell YES by hitting the best YES bid** (immediately executable, no fill
model, no phantom quotes), hold to settlement, net of the real Kalshi taker fee.

At lead>=0.5h (178 events) and lead>=1h (69 events) — the well-populated samples — **every
band is noise**: clustered t between −2.37 and +0.94, signs flipping between leads.
The 1-3c band prices 1.25% and realizes 1.01%: the mispricing is REAL and it is 0.24c wide,
against a 1-4c spread. **9a's "harvestable mirror" is confirmed to live entirely inside the
spread. It is not takeable, at any lead, in any commodity family.**

**TRAP RECORDED:** the lead>=2h and lead>=4h rows show +1.1c to +8.5c at **t=+34 to +44**
with realized rates of **exactly 0.00%** across every band including 12-20c. That is the
note-43 §5b leak signature — a cell that never loses. It is sample collapse: only ~25 events
survive the lead filter, all deep wings of long-dated dailies on quiet days. **Anyone who
reads those rows as an edge will fund a −100% residue book.**

## 3. QUEUE PROTECTION — REAL BUT WEAK (1.6x, not the free lunch it looks like)
Hypothesis worth testing: rest at the touch of a CROWDED tick-floor book — full score
(scoring is per contract at the best price, queue-position-blind) but no fills, because
20-28k contracts sit ahead of us. Measured over **698,506 ticker-hours** of the same capture:

| depth at touch | P(best bid drops below our level within 1h) |
|---|---|
| 0-50 ct | 28.6% |
| 200-1,000 ct | 26.8% |
| 1,000-5,000 ct | 20.2% |
| 5,000+ ct | **18.0%** (n=1,298) |

Depth cuts level-loss from 29% to 18% per hour. **Directionally right, magnitude far too
small to build on** — and measured on fast-underlying commodity books, so it does not
transfer to the slow families without re-measurement.

**Method note (my own error, recorded so it is not repeated):** the first version of this
test asked "P(level fully consumed while the price is unchanged)" and returned 0.0% in every
bucket on n=295,491. It conditioned away the event it was measuring — a level that clears
IS a price move. Censored sample, same defect class as the settled-cohort problem.

## WHAT THE THREE KILLS ADD UP TO
Every edge measured today is 0.2-3c gross against a 1-4c spread. **The venue is efficient to
within the spread**, so the only way to be paid here is to BE the spread — and the only
mechanism that pays for that without requiring a prediction is LIP.

Which puts everything on one unresolved fact: **does resting collateral actually get PAID,
and in which families?** Every estimate we hold is unreliable — K_eff varies 0.20-0.56 per
market, and LIP STATEMENTS §40 is ratified that $34.01 of accrual paid $0.00. Right now
$27.49 of $113.51 of live accrual (146 of 184 programs) sits under the $1 floor.
**No one has ever reconciled a Kalshi credits statement against `est_history.jsonl`.**
There is no credits endpoint in the API (seven paths probed, all 404) — it needs the
statement export from the web UI. Until that exists, the income side of this business is
unmeasured, and every allocation decision including "run at all" is being made blind.

## LIVE BOARD, measured today (context for that reconciliation)
$43,611/day of total pool active; $35,385/day of it in slow-underlying (non-continuously-
observable) families. Account captures **$25.43/day gross** — 0.06%. The self-consistent
reading of the books: seats that pay are already stuffed with 20-28k contracts at the 1c
tick floor; thin mid-priced seats are thin because the target-size gate means they pay
nobody. That reading is a HYPOTHESIS and the receipts are what test it.
