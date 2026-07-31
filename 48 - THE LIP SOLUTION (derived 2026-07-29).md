# 48 - THE LIP SOLUTION — derived from first principles, 2026-07-29 ~7:30am MT

> ⚠ **SUPERSEDED IN TURN (2026-07-29→31): do not implement from this note.** Its numbers are
> snapshot artifacts ([[50]] §4 — three independent allocators mined the same compmap tail) and
> its decisions (D3–D9, FREE_RIDE_ONLY, the $376 model) were replaced by [[52]] → [[54]] → [[55]].
> WHAT STANDS: §1 — the count-vs-dollars trap dissolves at floor-clearing SIZE; the escape is
> size, not price — and the derivation shape.
>
> Supersedes the design half of [[47 - THE LIP PROBLEM]]; 47's MEASUREMENTS all stand.
> Every decision below is derived from the goal and closed against a measurement on our own
> tape or on the live board. Where a number is a model and not a receipt it says so.

---

## 0. THE DESCENT

**Goal.** Power for Ryan → money → nestor earning maximally on Kalshi.

**State.** We hold a receipt-verified income line (**$71.34/day** of LIP credits) bolted to a
capital-destruction machine (**−$195.20** position P&L on $448.37 deployed; gas 26JUL29 settled
at **−$80.60 / −58%**, ~$42 worse than the mark Appendix I used). Every previous fix tried to
*trade off* the subsidy against the destruction — ban cheap rungs, band the price, add an exit
leg. All measured worse or neutral.

**Maximize.** What property of the answer maximizes profit? Not the price band — Appendix I
measured price dispersion at **5×** against venue dispersion at **250×**. The answer must
maximize **credit collected per dollar exposed to settlement**. That ratio has a numerator that
is *floored and saturating* and a denominator that is *linear*. So the maximizing move is on the
denominator, and it is not a filter — it is a size.

**Therefore.** The binding decision was never WHERE to stand. It was HOW MUCH to stand with.

---

## 1. THE RESULT THAT DISSOLVES THE CENTRAL TRAP

[[43]] §7 / [[47]] §1 name the hazard: *scoring is denominated in CONTRACT COUNT, count is
cheapest where contracts are worthless, so maximising the subsidy walks the capital into the
fire.* It has been treated as a law of the venue. **It is not. It is an artifact of buying count
we never needed.**

Credit per market-period = `pool × q/(q+S)`, floored at **$1.00** (below it, forfeit — the floor
is visible in the receipt itself, smallest line item exactly $1.00) and measured to **saturate
as ~√(size × time)** (Appendix I3, assumption-free: our biggest gas rung held 59.8% of the
event's contract-seconds; the biggest gas credit was 22.4% — no assignment reconciles those).

Risk = `q × p` dollars, strictly linear.

**Concave numerator, linear denominator ⇒ the optimum is the SMALLEST q that clears the floor.**

Measured on the live board (`~/compmap.json`, 2,983 books / 5,695 sides): median rival score
**123**, so floor-clearing size is **1–3 contracts**, median collateral **$0.40 per rung**.

At 1–3 contracts, **price barely moves cost** (5¢ vs 40¢ on 2 contracts is ~70¢ of collateral —
noise) while it moves *fate* from −65% to ~0%. The two denominators stop competing. **You are
free to stand where the contract is not garbage, because you are no longer buying count.**

> The −100% cohort was **1,123 contracts at ~5.6¢**. Floor-clearing size on those rungs was
> ~2 contracts. **We bought roughly 40× the size the reward required, and the excess is the
> entire loss.** Not the price. The size.

---

## 2. THE FATE SENTENCE (note 23 Part V, git history — the blocking item, now fillable)

> *A position acquired by this system ends by **settling at $0 or $1**, worth on average
> **~84% of the ~$0.40 paid** (calibration-weighted over the chosen band), against a subsidy of
> **≥$1.00 per rung per period**. With **every** rung filling — the true worst case — the whole
> book loses **$18.14** against **$376** of credit: a reward-to-fate ratio of **20.8 : 1**.*

v5 as run: **~1 : 10**. That inversion is the entire fix.

---

## 3. EVERY DECISION, DERIVED

**D1 — What is the product?** We are not trading; we are selling *presence* to Kalshi for a
posted subsidy. An acquired contract is **cost of goods** ([[43]] §2), never a position we want.
*Ethos: earning is the only aesthetic — a boring rebate that pays beats a clever trade that doesn't.*

**D2 — Do we acquire at all?** Unavoidably: a resting bid is a standing offer. So the question
is only how much, which is D3.

**D3 — SIZING (the master decision).** `q* = ceil(1.5 × S/(POOL−1))`, minimum 1 contract.
NOT `n_cap = floor($10/p)`, which buys 25 contracts at 40¢ and **500 at 2¢** — the trap written
as code. *MIRROR (too small ↔ too large):* too small forfeits the $1 — **32 of 56 rungs (57%)
did exactly this, 167 dollar-hours for zero**; too large is yesterday. The 1.5× margin is the
guard on the first end and is **the only job size has**. (Margin factor UNDERIVED — recalibrate
to `$1.00/q05(actual/projected)` after one period.)

**D4 — QUALIFICATION: never fund it.** The 1,000-contract target is a discrete precondition;
below it nobody is paid. `alloc.qualification_pass` funds the gap **at 1¢** — that one code path
*is* the −100% cohort's geometry. Replacement derives itself: qualification is worth the same to
us whether we or a rival created it, and a rival's costs nothing. The standing objection was
"nothing will qualify without us." **Refuted: 5,681 of 5,695 live sides already qualify.**
⇒ `FREE_RIDE_ONLY = True`.

**D5 — PRICE: chosen on fate, never on cost.** Since q is set by rival score, cost = q·p and the
cost difference across the whole price range is cents. Rank by `(credit − expected_loss)/cost`.
The water-fill lands at 6–20¢ and 21–50¢, with a few ≤5¢ rungs surviving only because they are
nearly free. **Do NOT ship the uncommitted `ACQUIRE_FLOOR_C = 6`** — it is the wrong instrument
(bans a price when the defect was a size), and Appendix I measured ≤5¢ as one of only two
net-positive buckets. The objective is the instrument; a ban is not.

**D6 — INDEPENDENCE: one rung per settle source.** Gas gave nine rungs on one number and we held
the complement of the mode on eight of them. Treasury gave 8–9 rungs on one yield curve: −$106.
[[43]] §3 — a ladder is one bet wearing many tickers. **359 independent settle sources are live
right now; 251 are fundable for $100.** This is also the answer to *"we don't have capital to
wait for hits"*: survivability is governed by independence, not bankroll ([[47]] §5 — the price
cancels).

**D7 — THE WINDOW FILTER MUST GO, AND IT IS COUPLED TO D3.** `MAX_WINDOW_MULT = 2.0` refuses any
program with a window > 48 h. Measured against today's board it deletes **299 of 310**
opportunities. Stacked with the deny-list and the acquire floor, **v5 would today quote 7 settle
sources: DXY, TRUEV and the five treasury rungs — precisely the venues that produced the losses.**
It was derived from ρ = pool/window (a daily packs its pool into 20 h; a weekly spreads it over
166 h — 11× per capital-dollar-day). That is a **capital-efficiency** argument, correct only when
capital binds. At floor-clearing size the entire 359-source board costs ~$300. **Capital does not
bind; opportunity does — and when capital doesn't bind you maximise total dollars, not rate per
dollar.** *Nobody caught this because sizing-by-count MADE capital scarce, which made the rate
filter look correct, which concentrated the book into one settle source.* Fix sizing and remove
the filter in the same commit, or the fix does nothing.

**D8 — HOLD, DON'T SELL.** Refuted twice on our own tape: instant-flatten **−$123** vs riding;
the +2¢ resting sell leg **−$40.30**, netting **zero** contracts. Below ~15¢ no exit exists at
any price. Derived: **the exit decision is downstream of entry.** At EV≈0 entry, holding costs
only carry, and carry on $0.40 for a week is fractions of a cent. At EV −65% no exit policy
repairs it. So build the entry filter, not an exit system. *MIRROR (holding ↔ being stuck):* D3
is the guard — being stuck costs $0.40. Stuck is only expensive when the position is big.

**D9 — DENY THE MECHANISM, NOT THE WORD.** `DENY_SUBSTRINGS = ("MENTION", …)` is wrong: the $16
loss was `KXEARNINGSMENTIONPYPL` at **20¢**, while `KXMLBMENTION` earned **$6.76 with zero
fills** — the highest-yielding venue we touched, 80× gas per dollar-hour. Deny `EARNINGSMENTION`.
The current ban costs **33 of 310** sources.

---

## 4. THE NUMBERS (model, with the discount attached)

| budget | settle sources | collateral | credit if all clear | worst-case loss (100% fill) | ratio |
|---|---|---|---|---|---|
| **$100** | **251** | $99.92 | $376.50 | **$18.14** | **20.8 : 1** |
| $200 | 311 | $199.35 | $466.50 | $32.73 | 14.3 : 1 |
| $300 | 328 | $298.22 | $492.00 | $36.74 | 13.4 : 1 |

**The honest discount.** $376 is a MODEL and [[47]] §4 says treat modelled accrual as 4–8× hot.
But this model is anchored differently from every prior one — to the **$1.00 floor visible in the
receipt**, not to a pool-share projection. Cross-check: our receipt paid **$71.34 across 56
rested rungs = $1.27/rung**; this model assumes **$1.50/rung**. It agrees with the receipt to
within 20% — the first accrual model on this program that does.

**The real discount is FORFEITURE.** 57% of our rungs earned under $1.00. If that persists at
floor sizing: 251 rungs → ~108 paying → **~$160/period**. *That is the number to plan against*,
and against −$19/day it is still the whole business.

---

## 5. WHAT CHANGES IN CODE (minimal set)

1. `alloc.n_cap` — replace `floor(inv_cap/p)` with floor-clearing size from rival score. **The inversion.**
2. `alloc.qualification_pass` → zero; gate `scan.build_slots` on **rival** qualification (own size deducted, SF-5). Removes the 1¢ land grab — and with it **both** 1¢ doors: the empty-book branch (`scan.py:509`, sets `p = 1¢` without re-testing the floor) and `lg_px_c` (`scan.py:568`).
3. `MAX_WINDOW_MULT` — off. See D7; it is not a separate decision from #1.
4. `DENY_SUBSTRINGS` — `"MENTION"` → `"EARNINGSMENTION"`.
5. **Revert** the uncommitted `ACQUIRE_FLOOR_C` diff in `config.py`/`scan.py`.
6. `clusters.py` — enforce **one rung per settle source** as a hard cap, not a worst-case dollar bound.

---

## 6. WHAT COULD KILL IT — named before it finds us

1. **Forfeiture at the floor.** Sizing to just-clear is sizing to just-miss. The 1.5× margin is the only defense and it is UNDERIVED.
2. **Pool value UNVERIFIED** ($10 vs $100 per period). The *structure* is robust to it (both give ~$125 for 147–310 sources); credit-per-rung is not.
3. **compmap is a snapshot.** Rival score moves; a rival piling in collapses our share under the floor. Needs continuous re-sizing — which the requoter already does.
4. **Adverse selection at 1–3 contracts is unmeasured** ([[47]] §8 question 3, still open). The loss column assumes **100% fill**, the true worst case, so this can only improve the number.
5. **Farming optics.** 251 minimum-size rungs across 160 series reads as farming; [[43]] §7 — the subsidy has a landlord.
6. **`depth ≥ target` is a proxy** for qualification; the rule may key on size at specific levels, not total depth.

---

## 7. THE TEST (receipt-shaped — the only kind that counts here)

One period. **$100.** Floor-clearing size, one rung per settle source, no window filter,
free-ride only, no acquire floor. Next morning, read the **Credits page** and compare the
**line-item COUNT** against 251 and the total against $376 / $160.

- count ≈ 251, total ≥ $150 ⇒ **the business is breadth at minimum size. Add capital.**
- count ≈ 251, total ≈ $251 (all exactly $1.00) ⇒ clearing the floor and no more; raise the margin.
- count ≪ 100 ⇒ forfeiture dominates; the margin factor is wrong, not the thesis.
- position P&L worse than −$20 ⇒ fill rate is the defect, and open question 3 is finally forced.

**Cost of being wrong is bounded by construction at $18.14 on $100.**

---

## 8. WHICH CONCEPT FILE CHANGES

- **[[43]] §7** — the two-denominator hazard is **conditional, not structural**: it binds only when you must buy COUNT, i.e. when you fund qualification yourself or rivals' score is high. At floor-clearing size on an already-qualifying book it does not bind. Amend §7 to say so, and to say the escape is **size**, not price.
- **[[47]] §1** — "do not fix this by banning cheap rungs" stands, and now has its positive form: fix it by **buying only as many contracts as the floor requires**.
- **Note 23 Part V (git history)** — the fate sentence is filled (§2 above); the program may ship an acquiring system again.
- **Note 23 Part IV capital corollary (git history)** — needs its mirror: *"every non-earning dollar displaces an earning dollar"* presumes the dollar has somewhere to go. Under a NON-binding capital constraint, rate-maximisation is the wrong objective and actively concentrates the book. That is what D7 measured.
