# PROBE: LIP-yield farm on KXAAAGASD — go/no-go package
Prepared 2026-07-27 for Ryan. Evidence: `work/verify-lip-gas.md`. Parent lane:
`work/lane-HOUSE-FEE-jul27.md` (H15/H16/H19). **Nothing here has been executed. No orders placed.**

**The ask: $30 of collateral for one 16-hour window, worst case −$30, to settle G1 and H23 and to
observe a real LIP payout for the first time. Everything else is already settled on paper.**

---

## 0. WHAT CHANGED SINCE THE LANE NOTE (read this first)

The lane proposed a probe to settle four gates. **Three are now settled without trading**, because
the exact LIP algorithm is published in Kalshi's CFTC rule filing of 2026-02-11 (effective
2026-02-28) — see `verify-lip-gas.md` §0.

- **G2 (distance metric) — CLOSED.** `Score(bid) = DiscountFactor^(RefPrice − Price) × Size`,
  distance in ticks **from the same-side best**. H19's cheap-side law stands.
- **G3 (target_size=1000) — CLOSED.** It is an **aggregate two-sided gate**, not a per-user minimum.
  100 lots score fine; but if *either* side's total resting size fails to reach 1,000, the snapshot
  is dropped **for everyone**.
- **G4 (crowding) — quantified.** Denominator is the qualifying set only, normalized per side.
  One rival matching our size halves us; ten rivals leave us ~19% (§5).
- **G1 (unit) — near-closed on four independent legs, still wants one payout observation.**

Two facts also invalidate parts of the lane's plan:
- **The 12-13Z gate is dead** (argmax of a 16-way sweep; fails out of sample; its stated mechanism —
  "post-AAA-print hours" — is false, since AAA prints ~04:00-05:00Z, *after* the 03:59Z close, and
  12-13Z is simply the market's opening hour). **Stake −0.729¢/ct [−1.016, −0.438], not −0.875¢.**
- **10 of the 17 gas rungs cannot generate an included snapshot at any price** (best bid pinned at
  the 99¢ cap, or best ask pinned at the 1¢ floor ⇒ no legal resting price exists on the missing
  side). The vehicle is **7 rungs, not 17**.

**And the trade is a LIP trade, not a markout trade.** At probe scale the maker edge is worth
~$1-3/day (0.729¢ × a few hundred contracts of fills); the LIP is worth $100-300/day. Size the probe
for the rebate; treat the markout as the reason the fills don't hurt.

---

## 1. VEHICLE — the rungs, and why

Series `KXAAAGASD` (AAA national average regular gas, daily). Each daily event opens **12:00:00Z**
and closes **03:59:00Z** (11:59pm ET), a **16.0-hour** life; expected expiration 14:00Z the next
day. Every rung carries its own `series_lip` program with **`period_reward` 1,000,000 = $100**,
`target_size_fp` 1000, `discount_factor_bps` 5000 (DF = 0.50), running exactly the market's life.
**17 rungs × $100 = $1,700 per window.** (Not $150/rung, not $2,553 — see verify §1.)

### 1a. Rung triage (state as of 2026-07-27 16:35Z; re-run the triage at probe time)
| class | rungs (26JUL28 strikes) | state | probe use |
|---|---|---|---|
| **QUALIFYING** | 4.090, 4.095, 4.100, 4.105, 4.110, 4.115, 4.120 | two-sided, both sides clear Target 1000, 100% of sampled snapshots included | **the main sleeve** |
| **REVIVABLE** | 4.075 (no NO bids at all), 4.085 (NO side only 25 lots) | one side short of Target ⇒ every snapshot excluded ⇒ **nobody is paid** | **the two lottery tickets** |
| **PINNED** | 4.070, 4.080 (yes bid at 99¢) and 4.125–4.150 (yes ask at 1¢) | no legal resting price exists on the missing side; permanently excluded | **do not touch** |

### 1b. Which qualifying rungs, and where in the ladder
Score per side on the qualifying rungs is a **thin ladder, not a wall** — DF=0.5 kills anything
beyond ~6 ticks, so the whole side's score totals 5-160 even though 3,000-6,000 contracts rest.
Measured 16:35Z: our 100 lots at the best price would be **62.3%** of 4.100's yes side and **95.0%**
of its no side. Prefer rungs with (i) small top-of-book size and (ii) a wide gap to the next level.
Deprioritise 4.120 (3,110/3,320-lot walls at 1¢/98¢ ⇒ our share 3%) and 4.090 (1,041 lots at 2¢).

---

## 2. THE PROBE — three arms, run in one window

Run on **one** daily event (open 12:00Z → close 03:59Z). All orders **GTC with `expiration_ts`
set to 03:55Z**, coid-scoped, per `build-house-probe.md`'s existing machinery.

### ARM A — the unit/scoring probe (settles G1, and calibrates the model)
Rest **10 lots per side** on **3 qualifying rungs**: `4.100`, `4.105`, `4.110`.
- Join the best price on both sides. Do **not** improve — arm B tests improvement.
- Collateral: 10 × (yes_bid + no_bid) ≈ 10 × $0.99 = **$9.90/rung → ~$30 for three rungs.**
- Predicted payout under the exact algorithm at size 10 (measured book, static): **$45-55 total**
  across the three rungs. If the unit is $1e-4 this shows up as tens of dollars; if it is $1e-5 it
  shows up as **$4.50-5.50**; if $1e-3 it is $450+ (and would breach the filing's $1,000/day cap).
  **One observation separates all three by a factor of 10.**

### ARM B — the placement probe (calibrates DF and the improvement premium)
On one *additional* qualifying rung with a ≥3¢ spread (today: `4.110`, spread 6¢), rest **10 lots
one tick inside** the best on both sides while arm A joins at best elsewhere.
- Predicted improvement premium from the model: **+9%** at equal size (join $311 → improve $340 at
  size 100 across 7 rungs). If the observed ratio between the join rungs and the improve rung is
  materially different from `DF^0 / DF^1`-implied, our qualifying-set model is wrong.
- Collateral ≈ **$10**.

### ARM C — the revival probe (settles H23 — the highest return per dollar on the board)
Rest **1,000 lots NO at 1¢** on `4.075` (currently has *zero* NO bids) — or on `4.085` if 4.075 has
been fixed by someone else by probe time.
- This single order takes the NO side from 0 to 1,000 contracts, clearing Target Size, and makes
  **every snapshot in the window includable for the first time**. As sole qualifying bidder on that
  side our normalized no-score is 1.0 ⇒ **50% of every included snapshot ⇒ $50 of the $100 pool.**
- Collateral: 1,000 × $0.01 = **$10.00. Max loss $10.00** (NO settles worthless if the AAA average
  exceeds $4.075, which the 98¢ YES bid prices at ~98%). It is also an ≈EV-neutral outright.
- **This is the single most informative order in the package.** If it pays ~$50, the pinned/revival
  law (H23) is confirmed and there is a repeatable, near-free structural farm. If it pays $0, either
  the unit is wrong or our reading of the two-sided exclusion is wrong — both are worth $10.

**Total probe: ~$50 of collateral, worst case −$50, realistically −$10 to −$20.**
(Arms A and B rest on *both* sides of each rung, so a two-sided fill is a locked box: pay ~99¢ per
YES+NO pair, receive exactly $1.00 at settlement. The true loss exposure is one-sided fills only.)

---

## 3. SIZE LADDER 1 → 10 → 100 (and why 100 is the wrong answer)

Modeled with the exact algorithm on the measured live books, joining at best on **both** sides of
all 7 qualifying rungs, $100/rung/window:

| size/side/rung | collateral | modeled reward /16h | return | note |
|---|---|---|---|---|
| **1** | $7.87 | $20.14 | **256%** | several rungs pay <$1.00 and are **forfeited** (filing's minimum payout) |
| **10** | $78.70 | $105.16 | **134%** | every rung clears $1.00; **best risk-adjusted rung of the ladder** |
| **30** | ~$236 | ~$200 | ~85% | interpolated |
| **100** | $787 | $311.42 | **39.6%** | the lane's proposed size; already deep in diminishing returns |
| 300 | $2,361 | $432.43 | 18.3% | |
| 1,000 | $7,870 | $652.03 | 8.3% | saturated — we already own 90-98% of every side |

**Ladder discipline:** run **size 10** in window 1 (arms A/B/C above). Only escalate to 30 in
window 2 if the observed payout is within **±40%** of the model at size 10. Only escalate to 100 in
window 3 if size 30 also holds and total collateral stays under the sleeve cap. **Never go past 100**
— the marginal $ buys under 15% of what the first $ bought, and the inventory risk scales linearly.

R153's ledger line *"SIZE BAND 100-20,000 CONTRACTS (1-lot probe scores ~nothing)"* **should be
corrected**: 100-20,000 is the range of the *Target Size parameter Kalshi sets per market*, not a
per-user requirement. A 1-lot order scores; it is capped only by the $1.00 minimum payout.

---

## 4. TTLs, STOP, AND KILL CONDITIONS

| control | value | rationale |
|---|---|---|
| order type | GTC + `expiration_ts` = **03:55:00Z** | 4 minutes before the 03:59Z close; existing `crates/house` machinery |
| replace cadence | re-evaluate every **60s**; only reprice if our price is no longer the same-side best or the spread has moved ≥2 ticks | snapshots are 1/second at nonpublic random times; churn buys nothing and risks crossing |
| **uptime target** | **≥95% of 12:00Z→03:55Z** | LIP is an uptime business; a gap is score forfeited pro-rata. Run on the VPS, not the laptop (R153) |
| **hard stop** | **−$20 realized+unrealized** across the probe | below the lane's −$20 convention because the probe deploys only ~$50 |
| per-rung inventory cap | **±size** (i.e. ±10 net contracts at size 10) | on a one-sided fill, stop quoting that side until flat or the window ends |
| orphan sweep | every 120s, coid-scoped cancel of anything not in the intended set | build-house-probe |
| **abort** | any fill at a price we did not intend, any self-trade, or `yes_bid ≥ yes_ask` in our own book | self-crossing is the one way this probe can lose real money |
| **do not** | quote the 10 PINNED rungs; take liquidity; run inside nestor's account | §1a; R153 |

**The 02:00-04:00Z problem.** ~45% of the measured maker edge sits in the last two hours before the
close (markout −1.18¢ at +14h, −2.01¢ at +15h). That is also 22% of all contracts. **The probe must
be live through 03:55Z** or it will systematically under-observe both the LIP score and the maker
edge. This argues for the VPS from window 1, not "after mechanics prove out".

---

## 5. WHAT WE ARE MEASURING (and the pass/fail bar)

| metric | how | model prediction (size 10, 3 rungs + revival) | pass |
|---|---|---|---|
| **M1 LIP $ received** | UI **Rewards → reward details** and **Account → Activity → Credits**, per program | arms A+B ≈ **$50-65**; arm C ≈ **$50** | within **±40%** ⇒ model validated |
| **M2 unit (G1)** | M1 magnitude | $1e-4 ⇒ tens of $; $1e-5 ⇒ single $; $1e-3 ⇒ hundreds | any of the three, decisively |
| **M3 revival law (H23)** | arm C pays vs $0 | **$50** | >$10 ⇒ H23 confirmed, a repeatable structural farm exists |
| **M4 improvement premium** | arm B rung $ ÷ arm A rung $ | ≈ **1.09×** at equal size | 0.9-1.4× ⇒ DF model right |
| **M5 realised maker P&L** | own fills, fee_cost, markout at +300s | **−0.729¢/ct in our favour**, ~$1-3 total | sign, not magnitude |
| **M6 fills** | count + one-sidedness | mid rungs trade 100-300 contracts/hr; expect **10-60 fills** | inventory stays inside cap |
| **M7 crowding** | book snapshots at 45s through the window | top-of-book size on our rungs | if anyone matches our size, note it — G4 is live |
| **M8 scoring latency** | poll `paid_out` on each program id | flipped **within ~12.5h** of close on all 330 completed gas programs | read the result the same evening |

**Readout method, concretely.** There is **no public rewards endpoint** (`/portfolio/rewards`,
`/portfolio/credits`, `/portfolio/incentives`, `/incentives` all 404; `incentive_programs` accepts
no ticker filter). So:
1. Before 12:00Z, snapshot the program objects for the 17 rungs (`id`, `period_reward`, `paid_out`).
2. From 04:00Z, poll `GET /trade-api/v2/incentive_programs` and watch each `id` flip
   `paid_out: false → true`. That is the scoring-complete signal.
3. Read the dollar amounts in the UI (**Rewards → Current month → reward details**, cross-check
   **Activity → Credits**) and record per-program.
4. Reconcile against `probe_sim2.py` re-run on the window's own captured book snapshots — **not** on
   the pre-probe snapshot. Sample books every 45s throughout so the reconciliation is honest.

---

## 6. WORST-CASE MATH (be pessimistic on purpose)

| loss channel | bound | reasoning |
|---|---|---|
| Arm C outright | **−$10.00** | 1,000 NO @ 1¢; total loss if AAA > $4.075. Market prices this at ~98%. |
| Arms A+B collateral | **−$40 absolute worst** | 10 lots × 2 sides × 4 rungs ≈ $40 tied up. Total loss requires *every* fill to be the losing side of *every* rung — the ladder is one underlying, so this is not achievable in practice. |
| Realistic one-sided-fill P&L | **−$2 to +$3** | ~10-60 fills of 10 lots at a measured **+0.729¢/ct maker edge**; even at the CI's adverse edge the sign is favourable. |
| LIP received | **$0** | if the unit is wrong, or the two-sided exclusion reading is wrong, or a whale copies us |
| **Total worst case** | **−$50** | the entire deployed collateral |
| **Expected case** | **+$70 to +$115 net on ~$50 of collateral, for one 16-hour window** | model $100-115 LIP, minus $0-10 arm-C loss, plus ~$1-3 markout |

**Downside that is NOT dollars:** a fill we did not intend (self-cross) or an inventory position we
cannot exit in a 1-6¢-spread book. Both are controlled by §4's abort conditions.

**Program risk:** the LIP terminates **2026-09-01** (verbatim in the filing), 36 days out, and Kalshi
"may end the Program at any time". R152's crowding warning stands and argues for probing **now**.

---

## 7. THE SUBACCOUNT / R153 SEPARATION QUESTION — my read, Ryan's call

R153's architecture call was: the maker system is a **separate binary, separate Kalshi subaccount,
restricted trade-only key** — to kill self-trade collisions with nestor's takers, keep P&L clean,
give it its own capital sleeve, and because **LIP consistency scoring is an uptime business that is
incompatible with nestor's drawdown halts**. That call is right and this probe strengthens it: §4's
uptime target (≥95% of a 16h window) is exactly the thing a drawdown halt would destroy.

**But the separation is not required for *this* probe, and I recommend not blocking on it:**
1. **Self-trade risk is nil here.** Nestor does not trade `KXAAAGASD` — it is not in any nestor
   vehicle list. The collision R153 was protecting against cannot occur on this series.
2. **The probe is $50 and one window.** Standing up a subaccount, a restricted key, and a separate
   binary is days of work to protect $50.
3. **The clean-P&L argument is satisfied by coid scoping**, which `crates/house` already does.

**Recommended sequence (differs from R153's "probe proves fills → standalone system → subaccount"):**
- **Window 1 (now):** run the probe from the existing account with coid-scoped orders, on the VPS,
  on `KXAAAGASD` only. Settles G1/H23/M4 for $50.
- **Only if M1 and M3 pass:** *then* build the separate binary + subaccount, before any size
  escalation past 30 lots and before extending to any series nestor might touch.
- **Do not** extend to `KXRAIN` (the other `series_lip` family, $1,669/day) until the subaccount
  exists — KXRAIN was measured **toxic** in the lane and is 40 markets wide.

**The open question for Ryan:** does the restricted trade-only key on a subaccount support the
`expiration_ts` GTC orders and coid-scoped cancels that `crates/house` relies on? If it does not,
the separation has a cost we have not priced, and that should be discovered before window 2, not
during it.

---

## 8. GO / NO-GO CHECKLIST (run at 11:45Z on probe day, ~15 min)

- [ ] Re-pull `incentive_programs`; confirm the day's 17 `KXAAAGASD` programs exist with
      `period_reward` 1,000,000, `target_size_fp` 1000, `discount_factor_bps` 5000, window
      12:00Z→03:59Z. **If `period_reward` or `target_size` has changed, re-run the triage.**
- [ ] Re-run the rung triage (`lipscore.py` on fresh books): confirm ≥3 QUALIFYING rungs and ≥1
      REVIVABLE rung. **If <3 qualifying rungs, NO-GO for this window.**
- [ ] Confirm no rival has appeared at ≥300 lots at the top of book on the chosen rungs.
      **If one has, drop that rung.**
- [ ] Confirm VPS clock, connectivity, and a scheduled 03:55Z cancel-all.
- [ ] Confirm the −$20 stop and the per-rung inventory cap are wired, and that the abort on
      self-cross is armed.
- [ ] Confirm `KXAAAGASD` is absent from every nestor vehicle list.
- [ ] Snapshot the 17 program `id`s and `paid_out` flags for the M8 poll.

**GO condition: all boxes ticked and total intended collateral ≤ $60.**

---

## 9. WHAT THIS PROBE DOES *NOT* SETTLE
- Whether the pinned rungs' $1,000/window is unpaid or paid to someone we cannot see. (Observable
  only indirectly, via arm C's revival result.)
- Whether Kalshi's CRO regards single-sided Target-Size revival as "abusive or inconsistent with the
  purpose of the Program" — the filing reserves that right explicitly. **Arm C is the one arm with
  non-financial risk.** It is defensible (it genuinely creates two-sided liquidity where there was
  none, which is precisely the Program's stated purpose), but it should be run at $10, once, and the
  outcome read before it is ever repeated at scale.
- Whether the $23.8k/day exchange-wide pool is reachable. That question needs the subaccount, the
  separate binary, and a per-family toxicity screen — not this probe.
