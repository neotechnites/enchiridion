# ACCOUNT TRUTH — 2026-08-06 night (primary tape, Kalshi API, pulled fresh)

> Ryan called it: "lip does not pay, it loses, a lot" — and the vault's numbers were wrong.
> This note is the exchange's own records, not the engine's log. **It supersedes every P&L
> claim in HANDOFF §4, the stack note §0, and the subpool-join result.**

## THE NUMBERS (indisputable — /portfolio/deposits, /portfolio/balance)

| | |
|---|---|
| Total deposited (13 deposits, Jun 17 → Aug 5) | **$2,600.00** |
| Total withdrawn | $0.00 |
| Cash now | $778.13 |
| Open positions, venue-marked | $138.84 |
| **Account value now** | **$916.97** |
| **LIFETIME P&L (incl. ALL LIP rewards ever credited)** | **−$1,683.03 = −64.7%** |

Tape: 1,527 fills (Jun 17 → tonight), 272 settlements, 1,289 orders, 53 markets with live
state. Trade fees $93.36 + settlement fees $63.27 ≈ **$157 of pure toll**.

## RETRACTION — "the book is winning ex-LYFT" IS FALSE

The 2026-08-06 subpool-join verdict ("14 of 17 families positive, +$125.93/day per $1k
ex-LYFT, the bleed is one uncapped tail") was computed from `seats_log.jsonl`'s own
`realized`/`markout` rows — **the engine grading its own homework with fill-at-mid marks the
vault already bans quoting as money** (HANDOFF §7: `day_realized` is NOT P&L). The exchange
tape contradicts it:
- Venue `realized_pnl_dollars` on the 53 live-state markets alone: **−$101.96** spread across
  MANY families (KXGROK −27.60, KXBILLSCOUNT −15.00, HIMS-PEPT −12.28, TRUMPACT −21.79, …) —
  not one LYFT tail. (Positions endpoint drops settled markets, so this is a floor-view, not
  the whole; the whole is the −$1,683 above.)
- **LAW (add to the field-hygiene list): engine self-marks are never P&L. Only
  deposits-vs-account-value, or the venue's own realized fields, count as money.**
- The stack note's "−$41.39 over Aug 3–6" and HANDOFF's "close-up-shop ≈ −$31" are
  understatements of a different regime: the account has been losing since June across
  political books, mention books, and the seats era alike.

## LIVE ALARM — THE BOT IS CROSSING TODAY (env intends crossing OFF)

Aug 6 (MT): **69 fills, 58 TAKER, $10.93 taker fees in one day.** Example: taker BUY
KXEARNINGSMENTIONCAVA-26AUG11-PITA yes @ 67¢, 16:06Z. Seats-era overall: 258 fills, 156
taker (60%), $30.25 fees. The handoff's env block (`SEATS_EXIT_CROSS_H=999`,
`SEATS_EXIT_EVENT_LEAD_H=-100000`) is either not what's running or not covering these
routes. **Verify the boolean, never the intent — again.** Whatever route is crossing, the
tape says the account pays the spread dozens of times a day.

STATUS: reported to Ryan; stopping the bot cancels live orders = money action, awaiting his
explicit go per standing rule.

## OPEN BOOK RIGHT NOW
10 open positions, cost basis $228.79, venue mark $138.84 (mostly short-yes/NO in
WEN/DKNG Aug 7 settlements + CAVA Aug 11). Books currently empty (no bids) overnight.

## ARTIFACTS
VPS `/tmp/truth.json` (full pull: fills/settlements/orders/balance/positions),
`/tmp/truth_an*.py`, `/tmp/dep.py`. Deposits list verbatim in the session transcript.
Note: naive fill-cash reconstruction over-counts short-side escrow (two failed attempts in
session — +$2,320 and +$1,996 "lifetime P&L" both impossible against deposits); do not
rebuild P&L from the fills tape without modeling escrow AND settlement-revenue semantics.
Deposits-vs-balance needs no model.
