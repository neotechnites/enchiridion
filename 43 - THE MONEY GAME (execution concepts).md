# 43 - THE MONEY GAME (execution concepts) — 2026-07-28

> The mesh for EXECUTION: the concepts a mind must hold to derive trading behavior from scratch.
> Concepts only — no decisions, no parameters, no policies. Where a number appears it is a
> pointer to evidence, and evidence decays. Written after a week in which every one of these,
> unheld, cost real dollars. *Add to this file or you didn't learn it.*

## 1. What a contract is
A binary claim that settles at $1 or $0 from a NAMED SOURCE at a KNOWN TIME. YES + NO = $1
always — buying NO is selling YES; every fill is an acquisition; positions close by netting.
Collateral equals the price of what you bought: the cheap side of any book is cheap to hold and
cheap to be wrong about. Settlement is the only truth; marks between are opinions that converge
to it as time runs out — panic-liquidation realizes the opinion of the thinnest book instead.

## 2. Capital has states, and only one of them earns
**Resting** (an order in the book): earns — spread on fills at your price, rewards for presence,
queue priority. **Filled** (a position): earns nothing, carries settlement risk and locks its
collateral until liquidity arrives (settlement, or someone taking your exit). **Settled**: cash.
Every execution strategy is a policy over these transitions. Fills are conversions OUT of the
earning state — a strategy that wants presence treats fills as its cost of goods. The exit's
price of impatience is spread + taker fee; its price of patience is carry. Both are computable,
and entry price belongs in neither (sunk — a rule that anchors on entry cuts winners and rides
losers by construction).

## 3. Correlation: the settle source is the position
Every rung of a ladder settles on ONE number. Fifteen rungs of "yield ≥ X" are one bet wearing
fifteen tickers; per-ticker limits cannot see it. Series sharing an underlying (all tenors of
rates; all windows of one commodity) co-move. Exposure is meaningful only per settle-source
cluster — and both sides of one rung partially cancel (a box = riskless $1 at settlement, minus
whatever you overpaid crossing legs at different times).

## 4. The maker's ledger (every term measurable, per venue, from your own tape)
earn = spread captured + rewards(presence)
     − adverse selection (fills that arrive BECAUSE information moved against your quote)
     − carry (capital × time-to-liquidity × what the capital would earn elsewhere)
     − fees (taker only; maker is free — evidence: prod-proven)
A venue is the SIGN of this sum, re-measured continuously; yesterday's sign is a hypothesis today.
Adverse selection concentrates where books are fast and informed (indexes, live-event mentions)
and where a market trends (a resting quote is a standing offer to whoever knows more — on a
trending day the book eats your quotes in one direction all day). Quiet books invert it: the
absent market-maker is the fish, and presence itself is the product.

## 5. Toxicity is observable without a model
Who fills you, and how fast? A quote that rests is earning; a quote that fills instantly met
information. Presence-seconds-per-dollar-hour — how much of your capital's time survives in the
earning state — separates venues empirically, from your own fills, within hours. Its mirror:
zero fills forever means either the perfect rewards venue or a market nobody wants — the
difference is whether anyone ever trades there at all.

## 6. Time structure
Everything here is windowed: markets open/close, reward windows start AND end, programs are
mortal (and their pools re-price without notice). Empty-book moments (opens, new listings) are
when presence is cheapest to establish; crowded moments are when it is diluted. Horizon is a
cost: a position in a market that settles tonight rents capital for hours; one that settles in
months (or on an event that may not occur) rents it indefinitely — the same fill at the same
price is a different trade at different horizons. Marks near settlement converge to truth, so
the value of paying to exit decays toward zero as settlement approaches.

## 7. Rewards are payment for BEING the book, not for trading
Pools attach per market per period; scoring samples the book (size × nearness-to-best, per
second, both sides independently); floors forfeit crumbs. Therefore: reward earning =
capital × time × proximity, in the resting state — and it saturates per pool (owning 100% of a
rung's book cannot earn more than the rung pays). Breadth beats depth once share saturates.
Fills reduce reward earning twice: the capital leaves the book, and the exit consumes room.
The subsidy is a business with a landlord: terms change, programs die, and behavior that reads
as farming rather than market-making invites eviction.

## 8. Shared account, many writers
One cash pool; every bot and every human action moves it. Any system watching the balance must
be told what the others did (feeds, ledgers), or its alarms fire on truth it wasn't given. One
writer per state file, ever. An account-level number (positions, balance) can never be
attributed to one system by inspection alone — attribution requires each system's own ledger.

## 9. Where the current numbers live (evidence, dated, decaying)
Measured reward rate, spread/fee round-trip costs, venue P&L decompositions, crossover horizons:
`work/audit-2026-07-28.md`, `work/backtest-instant-flatten.md`, and successors. Treat every
number read there as "was true then" — the concept is permanent, the constant never is.
