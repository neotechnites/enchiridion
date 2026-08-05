# LIP STATEMENTS
**The canonical statement of what is TRUE about the LIP strategy.**
Started 2026-07-31 (MDT) by Ryan's order, after three days in which code was built on
unratified assumptions and lost money proving they were wrong.

---

## THE DOCTRINE OF THIS FILE — read before touching it

1. **Nothing enters this file without Ryan's explicit thumbs-up.** Not a line, not a
   number, not a clause. Claude may PROPOSE; only Ryan RATIFIES.
2. Every statement carries a status: `[RATIFIED]` (Ryan approved it, with date) or
   `[PROPOSED]` (written by Claude, NOT yet approved — it may be wrong and may not be
   relied on).
3. **A `[PROPOSED]` line may not be used to justify code, an allocation, or a deploy.**
   If code depends on it, the code is unbuilt until the line is ratified.
4. **Theory vs prescription.** A statement belongs here only if it is a FACT about the
   world (how the exchange pays), a MEASUREMENT (a number we derived from data, with its
   source), or a DERIVATION (math that follows from those). A chosen operating point —
   a cap, a threshold, a bound, a policy — is a PRESCRIPTION: it does not belong in the
   theory sections, and where a choice is unavoidable the line must say out loud that it
   is a choice and who made it.
5. **State conclusions, don't gesture at them.** Premises first, then "Therefore: X" in
   full. Never write "X follows from that" — the reader does not already know X.
6. **This file outranks everything else** — code, code comments, other vault notes, and
   Claude's memory. Where they disagree with a `[RATIFIED]` line, they are wrong and get
   fixed.
7. **Corrections are logged, never silently overwritten.** A superseded line moves to the
   CORRECTIONS log at the bottom with the date and what changed.
8. Every number must be traceable to its source (an API response, a settled-market
   dataset, a ledger row). A number with no provenance is a `[PROPOSED]` guess and must
   be labelled as one.

---

## A. WHAT THE EXCHANGE PAYS

1. `[PROPOSED]` Each market runs a reward pool for a program window, and each side of the
   market is scored and paid separately out of half that pool.
2. `[PROPOSED]` Our credit = (our score ÷ total score on our side) × (pool ÷ 2) ×
   (fraction of the window we were resting).
3. `[PROPOSED]` A side's score = Σ qty × 0.5^(cents from the reference price): every cent
   of distance from the reference halves what a contract is worth.
4. `[PROPOSED]` A side pays nothing at all until resting depth on it reaches the program's
   target size (typically 1,000 contracts), and our own contracts count toward reaching
   that target.
5. `[PROPOSED — now sourced, awaiting Ryan's ratification]` Credit below $1.00 is
   forfeited instead of paid, and the threshold applies **PER MARKET PER TIME PERIOD**.
   Source: CFTC filing "Liquidity Incentive Program — February 11, 2026 Update"
   (rules02112639183.pdf): *"Each Time Period Liquidity Provider Score is multiplied by
   the Time Period Reward, and if the result is greater than or equal to $1.00, the
   result is paid out to the corresponding user, rounded down to the nearest cent."*
   A program specifies ONE market (its own Target Size, Discount Factor and Time Period
   Reward of $10-$1,000 per calendar day); our own programs feed confirms 1:1 — every
   program row carries exactly one market_ticker. Kalshi's help centre agrees: "a final
   reward below $1 for an individual program is not paid." **Therefore the payoff is a
   step, not a slope: earning $0.99 and earning nothing are the same outcome — and it
   is settled per market, so scattering escrow across sibling strikes of one event
   multiplies the number of cliffs we must clear.** (Statement 42's "Kalshi credits by
   event" describes the STATEMENT granularity, not this threshold — they do not
   conflict. Verified 2026-08-05.)
6. `[PROPOSED]` Only resting orders score. A fill ends the earning and leaves a position
   behind.
7. `[RATIFIED 2026-07-31 — replaces the earlier 1/price claim, which was WRONG]`
   Our share ≈ our contracts ÷ rival contracts, and our contracts = dollars ÷ price.
   Therefore share ≈ dollars ÷ (price × rival contracts), and
   **reward per dollar ≈ half-pool ÷ (the DOLLARS of collateral resting against us).
   Price cancels.** A dollar earns the same whether it is 100 contracts at 50¢ or 1,000 at
   5¢. **Therefore the ranking metric is pool ÷ competing dollars — not 1/price.**
   *(Ryan, 2026-07-31: "100 contracts at 50c is 50 dollars. If that is 10% of the pool,
   that's cheaper than 1000 contracts at 10 cents for 100 dollars if that's only 1%.")*
7a. `[RATIFIED 2026-07-31]` Price does not affect the earn rate, but it affects three other
   things: **(i)** the qualifying threshold is denominated in CONTRACTS (~1,000), so a
   cheap market costs far less to qualify in — at 3¢ that door costs $30, at 62¢ it costs
   $620, and this is the real advantage of cheap markets; **(ii)** variance per dollar if
   the order converts is (1−p)/p, so cheap positions are the volatile ones; **(iii)** the
   1–4¢ tick floor, where contracts are genuinely overpriced (see line 9a).
   **Therefore price determines what it costs to participate and what it costs to be
   wrong; competition determines what we earn.**

## B. WHAT A SEAT IS WORTH

8. `[PROPOSED]` Resting earns credit; being filled costs money, because the person who
   chooses to trade against a resting order usually knows something we don't.
9. `[REFUTED 2026-07-31 — the table is an ARTIFACT, do not use it]` A derived table (g)
   claimed that filled collateral loses a large fraction of itself, side-split, with cheap
   NO (6–50¢) losing 0.48–0.58. **It is a measurement artifact and the claim is false.**
   Proof: restrict the same dataset to quotes taken 1–2 hours before close and the toxic
   band's g becomes **+0.018 (n=431) — statistically zero**; the "bleed" comes entirely
   from quotes sampled hours before close on markets whose outcome was already decided,
   where the book is dead and the recorded "mid" is half an ask against no bid (4,157 of
   8,240 rows have bid = 0; mean spread 6.3¢, p90 16¢). The side split was the artifact's
   fingerprint, not a finding: a determined-YES market leaves a stale NO quote at 5–25¢,
   while a determined-NO market has no bid at all so its residue lands under 2¢ — which
   poisons the NO bands and leaves YES clean. Ryan's objection was the correct test: an
   edge that size would be takeable, and it is not there — buying the allegedly-good side
   at the real ask on live books earns **−0.3% ± 2.8%**.
   **Therefore fills in the 5–50¢ range are approximately EV-neutral, and every refusal
   made on the strength of g was made on a fabricated cost.**
9a. `[RATIFIED 2026-07-31]` One real effect survives every correction: **at the 1–4¢ tick
   floor, contracts are systematically overpriced** — buying them at the ask loses ~93%
   (z ≈ −30), and it persists at 1–2 hour leads. Its harvestable mirror pays 1–2% on
   95–99¢ collateral, which is fee-sized, **which is why it survives rather than being
   arbitraged away.** **Therefore the cheapest wings are the one place where a fill
   carries a real, measured, negative expectation.**
10. `[PROPOSED]` φ is fills per contract-hour of exposure; over a horizon h the expected
    number of turnovers is T = φ × h.
11. `[PROPOSED — the g term is REFUTED, see 9]` The SHAPE is: expected loss = q × p × T ×
    (loss per fill), while capital tied up = q × p × max(1, T). The shape stands; the g
    values do not, and no number may be substituted until one is measured honestly.
12. `[PROPOSED]` A seat's worth is net(q) = credit(q) − loss(q). Our share of a side is
    q/(q+S) against rival depth S, which is concave in q, while capital is linear in q.
13. `[RATIFIED 2026-07-31]` Marginal return within a rung is **near-linear while our size
    is small next to rival depth S**; the exact rate is
    `r(q) = [ S/(q+S)² × (ρ/2) × h ÷ p − T·g ] ÷ max(1,T)`, which declines monotonically,
    but the decline only bites as q approaches S. **Therefore diminishing returns inside a
    rung is real math and a weak force at the sizes we trade — what decides where a dollar
    goes is pool, competition, price, and time left.**
14. `[PROPOSED]` A budget is best spent by always buying the highest available marginal
    rate. **Therefore the optimal book equalizes r across every seat, and the rate at
    which the money runs out defines how deep each seat goes — that depth is an output of
    the board, not a number we pick.**
15. `[PROPOSED]` Because the payoff is a step (5) and our rate estimate is uncertain, a
    seat's real value is the probability it clears the threshold × the credit it would
    pay. **Therefore capital belongs wherever it is most likely to clear, not wherever it
    exactly reaches the threshold on paper.**
16. `[RATIFIED 2026-07-31]` Credit already banked cannot be lost or increased by anything
    we do next. **Therefore banked credit argues for nothing.** But a rung that has ALREADY
    CLEARED the threshold carries no threshold risk on anything further it earns, while an
    uncleared rung's credit is worth only face × P(it clears). **Therefore at equal gross
    rate a cleared rung's next cent is worth more than an uncleared rung's** — and credit
    accrued but still under the threshold vanishes if unfinished, so completing it can be
    the most valuable dollar on the board. *(Ryan, 2026-07-31)*

## C. RISK

17. `[PROPOSED]` We earn by providing liquidity and cannot predict outcomes. A filled
    position's value depends entirely on the outcome, and trading it means wagering on
    that outcome while paying the spread to someone better informed. **Therefore after a
    fill the correct action is to do nothing and let it settle.**
18. `[PROPOSED]` Nothing is ever exited, so every loss is realized at settlement.
    **Therefore a day's entire profit-and-loss distribution is fixed the moment the book
    is placed, and nothing done afterward can change it.**
19. `[PROPOSED]` A dollar on a binary at price p returns either 1/p − 1 or −1, giving
    variance (1−p)/p per dollar, which grows without bound as p falls. **Therefore a cheap
    book usually returns intact and occasionally loses everything at once.**
20. `[PROPOSED]` For N holdings at weights wᵢ, portfolio variance V = Σ wᵢ²(1−pᵢ)/pᵢ; at
    equal weights V = (1−p)/(N·p). **Therefore variance falls with genuine breadth and
    rises as prices fall, and those two levers trade against each other.**
21. `[PROPOSED]` Two markets are the same bet when their settlements are strongly
    dependent, regardless of naming: rungs on one price print resolve together, while
    distinct questions inside one series can resolve independently. **Therefore N is the
    number of independent settlement facts, not the number of tickers.**
22. `[PROPOSED]` Concentration and cheapness both raise return per dollar and both raise
    the daily tail. **Therefore where we sit between them is a DECISION — it depends on
    capital, on what the board offers, and on how large a daily swing we accept — and it
    is not derivable from the math alone.**
23. `[PROPOSED]` Cheap seats survive only where φ is small enough that collateral rests
    and returns instead of converting. **Therefore calling a family quiet is an empirical
    claim about the world, and a wrong one turns a wing book into a ruin book.**

## D. KNOWLEDGE

24. `[PROPOSED]` Everything in section B is a model of what a seat will earn. The exchange
    separately publishes what we have actually accrued, per market, continuously.
25. `[PROPOSED]` A measurement of the thing beats an estimate of the thing. **Therefore
    where measurement exists it governs, and the model's only remaining job is pricing
    seats nothing has observed yet.**
26. `[PROPOSED]` If the model and the measurement disagree, one of the model's inputs is
    wrong. **Therefore that disagreement is a discovery, and it must be impossible to
    overlook.**
27. `[PROPOSED]` A seat wrongly funded costs its collateral and is visible; a seat wrongly
    refused costs its earnings and is invisible. **Therefore refusals must be inspected as
    deliberately as fills.**

## E. SCALE

28. `[PROPOSED]` The board holds a finite number of good seats, and each additional dollar
    must buy a worse one than the last. **Therefore earnings are concave in capital.**
29. `[PROPOSED]` **Therefore what we can earn is capped by whichever is smaller: our
    capital, or the amount of positive-value depth the board offers that day.**
30. `[PROPOSED]` A small stake reveals whether the best seats pay what the model says. A
    large stake buys the breadth that makes owning many such seats survivable.
    **Therefore they answer different questions and must not be confused for one another.**

## F. THE OBJECTIVE, AND WHAT THE MECHANISM ACTUALLY COSTS
*(ratified 2026-07-31 — Ryan's corrections and additions)*

31. `[RATIFIED 2026-07-31]` **Profit = rewards − position profit and loss.** Rewards alone
    is a vanity metric; a day can earn well and lose money.
31a. `[RATIFIED 2026-07-31]` **The goal is $200 of profit per day on $1,000–2,000 of
    capital.** That is roughly 10–20% of the bank per day, and it is the number every
    design decision is ultimately judged against. *(Ryan)*
32. `[RATIFIED 2026-07-31]` A fill costs twice: the position's expected loss, **and the
    presence we stop earning while we are out of the book**. On a rung that fills often,
    the second cost can dominate — each fill removes us from the pool and we must re-enter
    to keep scoring. *(Ryan, 2026-07-31)*
33. `[RATIFIED 2026-07-31]` Each market's pool is finite, and our share of a side saturates
    as we come to dominate it. **Therefore breadth buys access to more pools — it is an
    earnings mechanism, not only a risk one** — though whether a second dollar in the best
    rung beats a first dollar in the next-best is always an empirical comparison, never a
    doctrine.
34. `[RATIFIED 2026-07-31]` Variance scales with the fraction of capital that **converts to
    positions**, not with the capital deployed. **Therefore fill rate and the average price
    we need are one question, not two.**
35. `[RATIFIED 2026-07-31]` Three constraints share one budget: N ≥ N_min (enough
    independent settle sources), size ≥ size_min (enough per rung to clear the threshold
    given competition, time left, and re-entry after fills), and N × size ≤ capital.
    **Therefore if N_min × size_min exceeds capital, the strategy does not fit at that
    capital and no cleverness repairs it — only more capital, more accepted variance, or
    rungs that are cheaper to clear.**
36. `[RATIFIED 2026-07-31]` Kalshi's hierarchy is **Series → Event → Market**; one rung is a
    *market*, a ladder is an *event*. **But the risk unit is neither: it is correlation of
    settlement, which can span events** (a daily and a weekly settling off the same print
    are one bet).
37. `[RATIFIED 2026-07-31]` Score-per-dollar ∝ 1/price systematically tilts us toward
    longshots. **Therefore our book is not a random draw — it is a draw biased toward high
    variance, and the ruin question follows from that bias, not from bad luck.**

## G. WHAT THE RECEIPTS SAY — the 2026-07-28 day, verified
*(ratified 2026-07-31; source: Kalshi Credits statement, cross-checked against
`~/nestor/data/lip/external_cash.jsonl` and the v4/v5 ledgers)*

38. `[RATIFIED 2026-07-31]` On 2026-07-28 Kalshi **paid $71.34**, in 24 line items across
    6 events. This is a receipt, not an estimate.
39. `[RATIFIED 2026-07-31]` **Gas daily paid $38.80 and treasuries paid $23.58 — 87% of the
    day** (UST 7yr $12.38, 10yr $8.29, 5yr $2.91; MLB mention $6.76; EV commodity $2.20).
40. `[RATIFIED 2026-07-31]` The two hourly index markets our own maker's model credited with
    $34.01 that day (`KXINXHUD`, `KXNDQHUD`) **were paid $0.00**. That accrual was phantom.
    **Therefore our model has produced large fictitious earnings, and any earnings figure
    not traced to a receipt is suspect.**
41. `[RATIFIED 2026-07-31]` UST 2yr and 30yr **forfeited everything** that day — no rung
    reached $1.00. The threshold is real and it bites at our sizes.
42. `[RATIFIED 2026-07-31]` Kalshi credits **by event, not by market**. **Therefore a
    per-rung record of what was actually paid does not exist anywhere, and event level is
    the finest truth obtainable.**
43. `[RATIFIED 2026-07-31]` The book that earned it was **not a resting book**: the v4 loop
    placed and cancelled orders every one to two seconds all day.
44. `[RATIFIED 2026-07-31]` July's paid total of $80.96 = $7.48 (Jul 27, verified receipt) +
    $71.34 (Jul 28) + $2.14 on unidentified days.
45. `[RATIFIED 2026-07-31 — UNVERIFIED CLAIM]` The ~−$195 of position losses associated with
    that day is **not present in any ledger on our machines**: those positions were still
    open when the run stopped and settled off-record. The number is remembered, not
    sourced.

## F. WHAT TESTING HAS TAUGHT US — *(data being reconstructed 2026-07-31; nothing here yet)*

The central lesson to be documented: the single day that earned ~$70 of rewards also took
roughly −$195 in fill losses. Two forensic reconstructions are in flight — (a) every rung
that earned that day, with its orders by time, size and price, and how long each rested;
(b) what Kalshi actually PAID per market that day, from the exchange's own record. This
section stays empty until those land AND Ryan ratifies what they say.

---

## CORRECTIONS LOG
*(superseded lines move here with the date and what changed — nothing is silently edited)*

- 2026-07-31 — Earlier drafts of this page mixed chosen operating points (a 20% daily
  loss bound, a 30-cluster diversification floor, a per-cluster cap of capital÷30, a
  12–15¢ price floor, "never sells" as a rule) into the theory. Ryan rejected all of them
  as prescriptions, not theory. The underlying relationships were kept (19–23); the chosen
  numbers were removed and belong in an operating-decisions note, not here.
- 2026-07-31 — Line 17 originally read "Holding to settlement follows from that," which
  presumes the reader already knows the conclusion. Rewritten to state the conclusion.
