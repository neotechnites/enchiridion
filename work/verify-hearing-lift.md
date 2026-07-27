# VERIFY-HEARING-LIFT — deep-verify of lane-MUSK M3 "sell the KXHEARINGMENTION lift"

Burst-2 verification lane, 2026-07-27. Target: [[work/lane-MUSK-jul27]] §1 M3 / §2 —
*"n=226 yes-taker lifts: mean px 0.545 → realized YES 0.323 = **+22.2¢/contract** for the seller.
Monotone in price. 22/26 events positive, median +14.2¢, worst −9.2¢. TRADE-shaped — top door."*

Spin-up read: [[33 The Mesh]] COUNT-family sections (lines 6, 18, 27, 42-49), [[07 Overfitting & Validation Discipline]].
Data: the MUSK lane's own pull, `hm_all.json` — **35 finalized events · 561 rungs · 73,586 trades**
(KXHEARINGMENTION-26JUN02 … -26JUL24). Read-only public API. No capital, no orders.

## VERDICT: **DEAD — look-ahead bias.** The +22.2¢ does not exist for any tradeable actor.

---

## 1. THE BIAS, NAMED AND REPRODUCED

M3's population is *"yes-taker lifts, condition on **the last trade** ≥2h before gavel-down."*
That is **one observation per rung: the last trade in the window `[listing, gavel−2h]`.**

Reproduced exactly (`vh_d.py`, candidate A):

| filter | n | mean px | realized YES | edge | events + |
|---|---|---|---|---|---|
| **MUSK M3 as published** | 226 | 0.545 | 0.323 | **+22.2¢** | 22/26 |
| **my reproduction** (last trade before gavel−2h, yes-taker, 0.02<p<0.95) | **141** | **0.511** | **0.312** | **+19.9¢** | **21/25** |

Same shape, same realized rate, same event hit-rate. (n differs on the exact gavel/threshold
convention; the mechanism is identical.) **This is the number.**

**Why it is not tradeable.** "The last trade in `[listing, gavel−2h]`" is identifiable only
*after* you know the rung never traded again in that window. Conditioning on a trade being the
last one selects rungs whose price **stopped moving** — dead words nobody bid up — which is
overwhelmingly the NO-settling population. It is a filter on the future.

The tell is in the realized rate itself: **realized YES = 0.312 against a base rate of
300/561 = 0.535.** The filter, not the trade, produced the 22 cents.

## 2. THE SAME POPULATION WITHOUT THE LOOK-AHEAD

Drop only the "last trade" condition. Keep everything else (yes-taker lifts, ≥2h before gavel,
0.02 < p < 0.95). This is exactly the population a resting offer is exposed to:

| | n | contracts | px / vwap | realized YES | edge | events + |
|---|---|---|---|---|---|---|
| M3 filter | 141 | 1,462 | 0.511 | 0.312 | +19.9¢ | 21/25 |
| **same, look-ahead removed** | **35,402** | **493,796** | 0.534 | 0.502 | **+3.2¢** equal-wt | 22/35 |
| **…size-weighted (the money number)** | 35,402 | 493,796 | 0.5508 | 0.5502 | **+0.06¢** | **15/35** |

**$288 of net seller edge on 493,796 contracts over 8 weeks. That is zero.**
Worst event −26.9¢/contract (−$2,798 on JUL14C); best +26.2¢ (JUN11). Per-event sd 8.25¢.

**MUSK's monotone price table inverts on the honest population** (all 48,804 lifts, size-weighted, ≥2h pre-gavel):

| price bucket | MUSK claim | **truth** | contracts | events + |
|---|---|---|---|---|
| 0.02–0.20 | — | **+6.71¢** | 80,207 | 30/35 |
| 0.20–0.40 | +18.7¢ | **+2.44¢** | 85,202 | 21/35 |
| 0.40–0.60 | +24.3¢ | **−4.46¢** | 106,697 | 16/35 |
| 0.60–0.80 | **+34.0¢** | **−2.17¢** | 102,543 | 14/35 |
| 0.80–0.95 | +25.4¢ | **+0.04¢** | 132,276 | 14/35 |

The "monotone in price" claim — the single strongest anti-overfit argument in M3 — is an artifact
of the same filter. Note 07's non-monotone-spike fingerprint applies in reverse: the *real* curve is
monotone-decreasing and crosses zero at 40¢.

## 3. (a) TAPE-vs-BOOK — WHAT A RESTING SELLER ACTUALLY CAPTURES

MUSK gate #2 framed this as unknown depth. It is worse than unknown: **the fill distribution of a
resting ask is not the tape's distribution.** A resting offer at price *p* is filled precisely by the
lifts that walk **through** *p* — i.e. by the rungs whose price is *rising*, i.e. the rungs where the
word is being said. The tape average is over prints; the maker's average is over pickoffs.

**Sim 1 — always-on offer at p on every rung** (`vh_h.py`; unlimited size, 100% queue priority = strict upper bound):

| p | rungs filled | contracts | edge | ROC | events + |
|---|---|---|---|---|---|
| 0.05 | 559/561 | 807,241 | **−62.14¢** | −65.4% | 0/35 |
| 0.20 | 534 | 724,919 | **−54.24¢** | −67.8% | 0/35 |
| 0.50 | 496 | 568,486 | **−35.82¢** | −71.6% | 3/35 |
| 0.70 | 441 | 452,071 | **−21.53¢** | −71.8% | 3/35 |

**Sim 2 — single-shot maker** (`vh_i.py`; offer ≤100 ct, filled by the FIRST lift at/above p, then done —
the fair model of a maker who is run over once):

| p | rungs filled | fills | **realized YES on fills** | edge | ROC | events + |
|---|---|---|---|---|---|---|
| 0.05 | 559 | 7,557 | **0.637** | −58.74¢ | −61.8% | 1/35 |
| 0.20 | 534 | 7,380 | **0.674** | −47.40¢ | −59.3% | 3/35 |
| 0.50 | 496 | 6,068 | **0.705** | −20.54¢ | −41.1% | 7/35 |
| 0.70 | 441 | 5,985 | **0.800** | −9.98¢ | −33.3% | 8/35 |
| 0.85 | 365 | 10,375 | 0.819 | +3.09¢ | +20.6% | 15/35 |

**The load-bearing number: a resting YES offer that gets lifted settles YES 64–80% of the time
against a 53.5% base rate.** That is the adverse selection, measured. Cancelling the offer 10h
before the gavel (pre-session only) does not rescue it (−59.5¢ / −48.5¢ / −21.5¢ at p=.05/.20/.50).

Only p=0.85 is positive (+3.1¢, +6.3¢ pre-session) and it is 9–15/35 events — selling an 85¢
favourite against a 0.79–0.82 realized rate, i.e. inside the noise, at 20% ROC on 6× the collateral.
Not a door.

**The graveyard defence in M3 §2 is backwards.** M3 argued "the pickoff kill is *measured into* this
number, and the seller still nets +22.2¢." The pickoff was measured *out* of it by the look-ahead
filter — the filter deletes exactly the rungs that picked the seller off.

## 4. (b) EVENT CONCENTRATION AND THE LEFT TAIL

On the honest all-lift population the per-event distribution is **18/35 positive**, mean −0.68¢,
median +0.33¢, sd 8.25¢ — a coin flip, not 22/26.

| worst 5 events | edge | $net | yes-rate |
|---|---|---|---|
| 26JUL14C | −26.73¢ | −$6,513 | 11/17 = 0.65 |
| 26JUL15 | −13.78¢ | −$2,853 | 11/17 = 0.65 |
| 26JUN30 | −11.08¢ | −$2,364 | 12/17 = 0.71 |
| 26JUN04B | −10.89¢ | −$4,489 | 10/16 = 0.62 |
| 26JUN04 | −9.28¢ | −$4,933 | 10/14 = 0.71 |
| *(worst $ loss: 26JUN17C, −$7,596)* | | | |

**The left tail has one driver and it is exactly the "hearing goes long/viral" scenario.**
Yes-rate of the 5 worst events = **0.67**; of the 5 best = **0.26**. A talkative witness says more
of the listed words, every YES rung ratchets, and the seller is short the whole slate at once —
the 20 rungs of one event are **not** 20 independent bets, they share one witness and one clock.
The best events (JUL01 1/15 yes, JUL01B 1/12, JUN17B 1/17) are hearings that were postponed or
never qualified, where nearly everything settled NO. **The strategy is short a single latent factor
"how much does the witness talk", and the payoff is a short-vol profile: many small wins, occasional
−27¢/contract across an entire 17-rung slate.**

Fauci is on the talkative/viral end of that distribution. His slate (§6) is already 5 rungs at 0.84–0.87.

## 5. (c) SETTLEMENT MECHANICS — FINE PRINT, PULLED LIVE

From `/series/KXHEARINGMENTION` and `/events/KXHEARINGMENTION-26JUL29` (2026-07-27 16:3xZ):

- **`fee_type = "quadratic"`, `fee_multiplier = 1`.** NOT `quadratic_with_maker_fees` — so the series
  is outside the 130-series maker-fee list and **maker fee is $0.00**. MUSK gate #1 survives; it is the
  only gate that does. Taker pays 0.07·C·P·(1−P).
- **Settle source is the LIVE VIDEO, not a transcript.** `rules_secondary`: *"Video of the … Testimony
  will be primarily used to resolve the market; if a consensus by Kalshi employees cannot be reached
  using video, internal & external (official) transcripts … will be used."* **There is no transcript
  lag to trade** — this closes the last door M2 left ajar and confirms the Mesh line 18 gate
  ("transcript-lag, not count-lag") does *not* apply to KXHEARINGMENTION.
- **Who settles: Kalshi employees, by consensus, watching the stream.** Discretionary human settlement
  is itself a risk line — payout criterion is literal (exact phrase, plural/possessive count,
  other inflections do not).
- **`settlement_timer_seconds = 1800`** (30 min).
- **Listed `close_time` is a placeholder.** All 20 JUL29 rungs list `2026-08-13T14:00:00Z`. Measured
  across the 35 finalized events, realised close landed **0.0–38.6 min after the event's last trade**
  (median ≈ 4.5 min): **the market closes at gavel-down, whole-event, and the listed date is overwritten.**
  Correction to lane-MUSK M2's phrasing: rungs do **not** individually early-close at the spoken word —
  the per-event spread between first and last rung close is 0.00–1.13h for 34/35 events (one outlier
  3.93h). The "median lock-lead 10,102s" M2 measured is **the market repricing to ≥95¢ 2.8h before the
  gavel**, i.e. efficient in-hearing repricing, not an early close. Same conclusion (no post-lock
  window), different mechanism — worth fixing in the Mesh.

## 6. (d) FAMILY AGE — CLASS B LIFETIME

First finalized event **26JUN02**, most recent **26JUL24**: **7.6 weeks, 35 events, ~4.4/week.**
Everything above lives inside one series, one product, one 8-week regime, one settlement team.
Even had the number survived, note 07's bar (different period *or* different platform) is unmet —
JUN-vs-JUL is a within-regime split, the exact failure mode of the 2026-06-25 favourite-underpricing
case study. It did not survive, so this is moot; recorded so the family is not re-proposed.

## 7. (e) CAPACITY AT REAL OI — measured on the live JUL29 book

Live dry-run of the capture script, 2026-07-27 16:32Z, all 20 rungs:

- **Total event OI = 1,539 contracts. 8 of 20 rungs have OI = 0.**
- **Resting ask size at the touch: 1 to 61 contracts, median 5.5, total ≈ 248 across all 20 rungs**
  — roughly **$150 of notional** for the entire slate. Ladders are 5/10-lot quotes.
- Spreads 5–22¢ (BIDE .47/.69, PARD .21/.43, CONS .53/.70).
- Historical per-event yes-taker volume ranges 17.8k–151.3k contracts (median ≈ 38k), so ~99% of the
  flow arrives on hearing day — but it arrives *into* a 5-lot book, in 5-lot bites.

Even under the counterfactual that the +22.2¢ were real, a maker at 100% queue priority on the whole
slate captures a few hundred contracts per event → **~$50–100/event gross before adverse selection**,
against a measured left tail of −$2,800 to −$7,600 per event on the same flow. The risk/capacity
ratio alone kills it independently.

## 8. WHAT IS LEFT (small, honest, and NOT the M3 door)

One residual survives every cut above and is *not* the sell-the-lift trade:

**Cheap-wing YES lifts, pre-session** (px 0.02–0.20, ≥10h before gavel): vwap 0.092, realized YES
0.016, **+7.56¢/contract, 63,776 contracts, 33/35 events positive, worst event −$258.**
Split-half holds: JUN +5.13¢ (26,861 ct, 18/21 ev) / JUL +4.35¢ (118,973 ct, 10/14 ev).

This is long-shot bias on unlisted-word lottery tickets, sold *days before* the hearing, cancelled
before the session — i.e. it deliberately never rests through the pickoff window. ROC ≈ 7.56/91 = **8.3%
per event**, ~4.4 events/week, capacity bounded by the same 5-lot book (~$150/event of premium at 100%
capture, realistically $15–40). **CONDITIONAL / parked**: it is the same shape as verify-seed-prior's
blind-NO, it is measured on the same 8-week single family that just failed, and the single-shot
maker sim at p=0.05–0.20 pre-session is **−48¢ to −60¢** — meaning the +7.56¢ is available to the
*tape* but not to a *resting offer* that stays live. It would need a cancel-before-session discipline
proven on the forward capture before it is anything but a note.

## 9. LEDGER

| # | attack | result |
|---|---|---|
| V1 | reproduce +22.2¢ | **reproduced at +19.9¢ / realized 0.312 / 21/25 events** — filter identified as "last trade in `[listing, gavel−2h]`" |
| V2 | remove look-ahead, same population | **+0.06¢/contract size-weighted, $288 on 493,796 ct, 15/35 events** — edge is zero |
| V3 | (a) resting-offer reconstruction | **−62¢ (always-on) / −59¢ to −10¢ (single-shot); fills settle YES 0.64–0.80 vs 0.535 base** |
| V4 | (b) event concentration + left tail | 18/35 positive, sd 8.25¢, worst −26.7¢/−$7,596; **loss driver = event yes-rate (0.67 worst-5 vs 0.26 best-5)** — one witness, one clock, 20 correlated shorts |
| V5 | (c) settlement fine print | maker fee $0 ✅; **settles on LIVE VIDEO by Kalshi consensus, no transcript lag**; close = gavel-down whole-event, listed date is a placeholder; timer 1800s |
| V6 | (d) family age | 7.6 weeks, 35 events, one series — note-07 OOS bar unmet |
| V7 | (e) capacity | **1,539 OI; ≈248 ct of resting ask across 20 rungs (~$150); median 5.5 ct at touch** |
| V8 | residual hunt | cheap-wing pre-session +7.56¢ (33/35 ev) — **CONDITIONAL, parked**, not the M3 door |

**Disposition: M3 → DEAD-with-numbers.** Move it out of "top door". The MUSK lane's other verdicts
are untouched by this (M2's no-post-lock-window conclusion stands, on a corrected mechanism).

## 10. MESH DELTAS

- **New graveyard entry — the "last trade before T" filter is a look-ahead trap.** Conditioning on a
  print being the *last* one in a window selects rungs whose price stopped moving = the NO population.
  It produced +22.2¢ out of a +0.06¢ tape. **Any per-rung "last trade before X" statistic is
  disqualified unless X is a wall-clock cutoff and the observation is the last trade *known at X*.**
- **Resting-fill ≠ tape-average, quantified for the first time.** On KXHEARINGMENTION a lifted resting
  YES ask settles YES **0.64–0.80** vs a 0.535 base. Any future "sell the lift" claim measured on the
  trade tape must show a single-shot maker sim before it is called TRADE-shaped.
- **Size-weight before believing.** Equal-weight-per-print gave +3.2¢; size-weighted gave +0.06¢ on the
  identical rows. Print-count means overweight the 1-lot noise.
- **KXHEARINGMENTION settles on LIVE VIDEO, not a transcript** — correct the Mesh's COUNT-family line 18:
  the spoken-count/transcript-lag gate does *not* apply to the hearing family. Kalshi employees settle
  by consensus off the stream.
- **Hearing rungs do not individually early-close at the word.** Whole event closes at gavel-down
  (0.0–38.6 min after last trade); per-event rung-close spread 0.00–1.13h for 34/35. Fix M2's phrasing.
- **The COUNT/MENTION family is now fully worked.** Lock: dead (M2). Path: dead (this note).
  Seed-prior: +2.7¢, doesn't generalize. Only the cheap-wing pre-session long-shot remains, parked.

## 11. FORWARD CAPTURE — SPEC AND READINESS

`~/kalshi_data/scripts/hearingmention_26jul29_capture.py` — **written, dry-run green, NOT scheduled.**
Fable arms it. Read-only public API, no credentials, no orders, zero capital.

Its purpose changed with the verdict. It is no longer confirming a trade; it captures three facts
only a live book+tape can produce:
- **F1 queue reality** — pair every book snapshot with the trades that follow it → per-rung resting ask
  size at each level, how much is consumed before the level moves, therefore the share of a lift a new
  resting offer could ever win. (Answers MUSK gate #2 with numbers.)
- **F2 the pickoff clock** — seconds between the first print that reprices a rung and the disappearance
  of the stale ask. That interval *is* the resting seller's loss; never measured.
- **F3 close mechanics observed** — logs `status` + `close_time` every sweep, so the placeholder→gavel
  rewrite is caught in the act, per rung.

Cadence: start **2026-07-29 12:30Z** (SLOW 120s baseline) → **13:45Z** switch to **FAST 20s** full sweeps
(hearing scheduled 10:00 ET = 14:00Z) → auto-stop once every rung leaves `active` plus 30 tail sweeps
(~10 min) to catch the settlement rewrite → hard stop **2026-07-30 02:00Z**. Self-exits(0) outside the
window so a supervisor respawn is harmless. ≤8 req/s, `*_dollars`/`*_fp` only, key-presence validation
on every page, trade dedupe on `trade_id` persisted to `_seen.json` (restart-safe).

Outputs: `~/kalshi_data/hearingmention_26jul29_books.jsonl` (rung × sweep: full yes/no bid ladders, OI,
volume, status, close_time) and `~/kalshi_data/hearingmention_26jul29_trades.jsonl` (new trades only).
Resting **ask** size at cents *c* = the **no**-bid size at level (100−c).

Live event: **KXHEARINGMENTION-26JUL29** — *"What will Fauci say during the Senate Homeland Security
Hearing?"* — 20 rungs, all `active`, opened 2026-07-27 15:49Z. OI 1,539 total; VACC .86/.95 (246),
CHIN .87/.96 (256), CDC .84/.94 (241), LEAK .85/.94 (237), GAIN .85/.94 (221), WUHA .77/.88 (102),
MASK .72/.80 (89), MAND .70/.83 (91), LOCK .65/.75 (42), TRUM 3+ .54/.66 (31), BIDE .47/.69 (17),
MRNA .46/.60 (1), NQE .04/.17 (20); zero-OI: CONS .53/.70, PEER .38/.49, HIV .31/.42, MORT .29/.42,
HYDR .22/.33, PARD .21/.43, TERR .15/.29.

---

*Analysis scripts in the session scratchpad: `vh_b.py` (repro + size-weight), `vh_c.py`/`vh_d.py`
(filter identification), `vh_f.py`/`vh_g.py` (clock + price buckets), `vh_h.py` (left tail +
always-on sim), `vh_i.py` (single-shot maker sim). Source tape `hm_all.json` (35 events / 561 rungs /
73,586 trades) is the MUSK lane's own pull — the kill uses MUSK's data, not different data.
Nothing written to `~/kalshi_data` except the armed-but-idle capture script. No nestor changes.*
