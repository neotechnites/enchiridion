# VERIFY: Volbook execution fit + Monday conformance predictions (2026-07-26)

Goal: is **take-at-first-sight IOC ≤ per-bucket ceiling** the right entry inside the
T-3h±30min window, and what decision stream should Monday produce?
Answer up front: **current policy is right** — the maximum attainable gain from perfect
intra-window timing is **+0.31c/contract (n=462 windows)** on a ~+8c edge, i.e. **≤4%**,
and every realizable waiting/resting variant is **≤ 0.00c** vs current. Do not change it.

## 0. LEAD CAVEAT — the book tape contains ZERO entry windows

`cwing_books_*.jsonl` (captured Fri 2026-07-24 15:00 PDT → Sun 2026-07-26 12:04 PDT) holds
**one close only: 2026-07-27 21:00Z (Monday)**. Snapshot **ttc range = 26.9h..77.2h**; the
entry window is ttc **9000–12600s (2.5–3.5h)**. So:
- **0 snapshots inside any entry window. 0 Mon-Wed entry windows. 0 in-window observations.**
- The whole book tape is **weekend / off-session** on a Monday-close contract.
- **KXCOPPERD is not captured at all** — it is absent from `SERIES` in
  `~/kalshi_data/scripts/capture_cwing_books.py` (`["KXBRENTD","KXGOLDD","KXSILVERD","KXNATGASD","KXWTIH"]`),
  yet it is an enabled metal series in calib (weight 0.5, n=87). No book evidence for copper.

Therefore all **book-derived** numbers below are **1-hour pseudo-windows at the wrong ttc on
the wrong days — indicative only**. The **Mon-Wed T-3h numbers come from the `cwing_obs`
print corpus** (n=336 wing rungs, 29 Mon-Wed dates, 87 events), which *is* measured at exactly
T-3h on exactly the gated weekdays. Filters reproduced bit-exact against
`scripts/volbook_calib.py` (n=336, touch 0.0357, implied 0.1367, nd=29, ne=87 — all match
`volbook_calib.json`), so the corpus read is verified, not assumed.

## 1. Data + filters

**Book tape** (`cwing_books_KXGOLDD/KXSILVERD.jsonl`, 3-min cadence, schema decoded from the
capture script: `yb`/`nb` = full yes-bid / no-bid ladders `[cents,size]`; **NO ask = 100 −
best yes-bid**, backed by that level's yes-bid size; yes ask = 100 − best no-bid).
**R13 stale-field discipline applied**: dropped consecutive byte-duplicate books per rung
(**gold 27%, silver 30% of raw lines**) and dropped snaps with an empty/zero-size side or a
crossed/invalid quote (**gold 9%, silver 13%**). Kept: gold 16,674 / 31 rungs, silver 13,445 /
26 rungs. Wing-band snaps (yes mid ∈ [0.05,0.35)): **gold n=3,710, silver n=3,407**.

**Print corpus** (`cwing_obs_*.jsonl`): T-3h last yes print, `age3 ≤ 4h`, `y3 ∈ [0.05,0.35)`,
weekday ∈ {Mon,Tue,Wed}. **n=336** (gold 100 / silver 149 / copper 87), 29 dates, 87 events.
Ceilings recomputed from calib: `p ≤ 100(1−touch_shrunk) − 2.0c − fee(p)`, fee = 7·p·(1−p) cents.

| bucket (implied yes) | touch_shrunk | **ceiling** | median T-3h ask (print+1c) | P(ask ≤ ceiling) |
|---|---|---|---|---|
| 0.05–0.10 | 0.0083 | **96.96c** | 94c | 100% (n=142) |
| 0.10–0.15 | 0.0250 | **95.18c** | 90c | 100% (n=72) |
| 0.15–0.20 | 0.0694 | **90.46c** | 85c | 100% (n=42) |
| 0.20–0.25 | 0.1563 | **81.31c** | 79c | 100% (n=27) |
| 0.25–0.30 | 0.1601 | **80.91c** | 75c | 100% (n=32) |
| 0.30–0.35 | 0.2783 | **68.66c** | 69c | **43%** (n=21) |

**The ceiling is not the binding constraint** — the market ask sits **3–6c below** it in every
bucket except the top one. 324/336 = **96.4%** of in-band rungs qualify. This matters for the
execution question: there is no "wait for the price to come to the ceiling" problem to solve.

## 2. Ask-path facts (book tape — wrong-ttc, indicative)

Wing-rung books in this tape are **slow, tight and deep**:

| fact | gold | silver |
|---|---|---|
| yes spread, med / p75 / p90 | **1c** / 2c / 3c | **1c** / 1c / 3c |
| taker half-spread paid vs mid (median) | **+0.5c** | **+0.5c** |
| depth at best yes-bid, med / p25 | **381** / 199 | **200** / 100 |
| 1h windows with **zero** ask change | **68%** | **68%** |
| P(any improvement first→best in 1h) | 15% | 18% |
| P(improvement ≥2c) / ≥5c | 3% / 0% | 12% / 3% |
| mean improvement first→best | **0.20c** | **0.46c** |
| P(ask worsens ≥2c after its best) | 9% | 15% |

n = 247 (gold) + 215 (silver) = **462 rung-hour pseudo-windows** with ≥5 clean snaps and the
rung in-band at window open, median 15–16 snaps/window.

Maker-leg fill probabilities within an hour: **P(min ask ≤ first−1c) = 16.5%**, ≤ first−2c =
**6.9%**, ≤ first−3c = **3.5%** (n=462). And **waiting to window-end forfeits the trade
outright in 1.7%** of windows (ask ≤ ceiling at first sight, > ceiling at the end).

Note the calib models **+1c taker slippage**; the measured book half-spread cost is **0.5c
median**, so calib's entry-price assumption is conservative by ~0.5c.

## 3. Policy EV table — E[EV captured] per contract per qualifying rung

Pooled gold+silver, n=462 windows. EV(p, touch) = (1−touch)·100 − p − fee(p); unfilled = 0.
Bucket assigned from the yes mid at window open; ceiling from §1.

| policy | EV/ct | fill | vs current |
|---|---|---|---|
| **A. CURRENT — first-sight IOC ≤ ceiling** | **+6.31c** | 95% | — |
| B. best-in-window (**ORACLE**, unattainable) | +6.62c | 95% | **+0.31c** |
| C. wait to window-end, IOC ≤ ceiling | +6.31c | 93% | −0.01c |
| D. rest NO bid @ first−1c + backstop IOC ≤ ceiling | +6.28c | 95% | −0.03c |
| D. rest NO bid @ first−2c + backstop IOC ≤ ceiling | +6.28c | 94% | −0.04c |
| D. rest NO bid @ first−3c + backstop IOC ≤ ceiling | +6.29c | 93% | −0.03c |

**The oracle ceiling is +0.31c (4.9% of captured EV) and no implementable policy reaches any of
it.** Resting is a *loss* even before adverse selection: the maker leg fills only 16.5% of the
time for 1c, and the 83.5% that backstop pay the same price a window later while carrying the
1.7% forfeit risk and the 9–15% chance the ask has moved ≥2c against them.

This is the structural opposite of the streak result (`work/verify-streak-execution.md`), and
for a legible reason: there the reversal ask **opened above breakeven** (median 53c vs 50.3c)
and dipped 6c within 60s, so waiting *created* the trade. Here the ask **opens 3–6c inside the
ceiling** and the book does not move at all in 68% of hours. Cont–Kukanov regime (ii) — the
underfill penalty is the whole edge (+8c forgone vs ≤1c of price improvement available) →
**take everything, immediately.**

**Independent Mon-Wed corroboration (print corpus, correct days/ttc):** realized P&L of
take-at-T-3h-if-≤-ceiling = **+7.94c/contract, n=324, se 0.95** (calib's event-weighted figure
is +8.61c). Taking *everything* in band ignoring the ceiling = +8.37c (n=336) — i.e. the ceiling
filter costs 0.43c/contract of mean EV in exchange for removing the fat left tail; keep it.

**Earlier entry (T-6h) is not evaluable and looks like a mirage.** Paired T-6h vs T-3h on the
same rungs: +23.23c vs +8.49c (n=303) — but `y6` is only recorded *because* the rung was in-wing
at T-3h, so this is pure look-ahead: mean yes drift T-6h→T-3h is **−15.2pp** and only 188/303
rungs were in-band at T-6h at all. **Do not read this as "enter earlier."** Testing it honestly
needs a T-6h-selected corpus that does not exist.

## 4. PRESCRIBED POLICY — **current is right; ship unchanged**

1. **Keep take-at-first-sight IOC at the per-bucket ceiling.** Maximum theoretical gain from
   any timing change is +0.31c/ct (≤5%); realizable gain is ≤0.
2. **Keep 1+1 retry.** Median depth at the touch is 200–500 contracts vs a small order, spread
   1c — a miss is a quote move, not a size shortfall, and one re-poll covers it.
3. **Do not add a resting-bid leg.** It is −0.03 to −0.04c/ct *before* any maker toxicity, and
   the house probe has not reported. (Maker-fee schedule also unverified — irrelevant here,
   since the leg loses on price alone.)
4. **Keep the ceiling** even though it binds only in the top bucket — it is the tail guard.
5. **Do not mirror the wing.** The symmetric lower tail (y3 ∈ (0.65,0.95], buy YES) has a
   **negative** calibration gap: implied tail 14.35% vs realized **18.11%**, gap **−3.8pp**,
   naive take-all EV **−5.56c/ct (n=370, se 1.97, 12.8 rungs/day)**. The edge is one-sided —
   only strikes *above* spot are systematically rich. Expanding to the mirror would bleed.
6. **Two gaps to close, neither a policy change:** (a) start capturing `KXCOPPERD` books;
   (b) capture must be alive **through 17:30–18:30Z Monday** or the conformance check in §5
   cannot be reconciled against a book at all.

## 5. MONDAY 2026-07-27 — falsifiable predictions

Entry window: **17:30–18:30Z (10:30–11:30 PDT)**, close 21:00Z. Basis: 29 Mon-Wed days of
T-3h prints, `nd=29` per series. Reconcile Monday's decision stream line by line against these.

**Predicted rung counts (per series, at T-3h):**

| series | rungs scanned (med) | **in-band (yes 0.05–0.35)** | **QUALIFYING (ask ≤ ceiling)** | 0-rung days seen |
|---|---|---|---|---|
| KXGOLDD | 34 | mean 3.45, med **3**, p10–p90 **2–6** | mean 3.31, med **3**, range 2–8 | 0/29 |
| KXSILVERD | 37 | mean 5.14, med **5**, p10–p90 **3–9** | mean 4.97, med **5**, range 2–11 | 0/29 |
| KXCOPPERD | 21 | mean 3.00, med **3**, p10–p90 **1–5** | mean 2.90, med **3**, range 1–6 | 0/29 |
| **metal total** | ~92 | **mean 11.6, med 11** | **mean 11.2, med 11, 80% CI ≈ 6–20** | 0/29 |

**Predicted bucket mix of qualifying rungs (per day, all 3 series):**
0.05–0.10 → **4.90**; 0.10–0.15 → **2.48**; 0.15–0.20 → **1.45**; 0.20–0.25 → **0.93**;
0.25–0.30 → **1.10**; 0.30–0.35 → **0.31**.

**Predicted skip-reason histogram (per day, over ~92 metal rungs with a T-3h price):**

| reason | per day | share |
|---|---|---|
| out of wing band (yes <0.05 or ≥0.35) | **58.0** | 63% |
| stale/no recent print — live equivalent: **no two-sided book / no backing size** | **22.2** | 24% |
| **ask above bucket ceiling** | **0.41** | 0.4% (12/336 in-band; **11 of 12 in the 0.30–0.35 bucket**) |
| **QUALIFY → order sent** | **11.2** | 12% |

**Predicted fill / EV:**
- **≥90% of qualifying rungs fill on the first IOC; ≥98% within the 1+1 retry.** (Median depth
  at the touch 200–500 contracts, spread 1c; the only failure mode is a quote move.)
- **Expected EV per filled contract: +7.9c** (print corpus n=324; calib event-weighted +8.6c).
  Per-day mean-per-rung across 29 days: mean **+8.90c**, p10 **−5.05c**, p90 **+14.15c**.
- **Expected day total ≈ +89c/contract summed across ~11 rungs** (median +108c, p10 −56c,
  p90 +153c). **4 of 29 days were net losers** — a red Monday is ~14% likely and is *not*
  evidence of a bug.
- **Expected touches (settling YES against us): 0.4 of ~11 rungs** (realized touch 3.57%,
  n=336). **Two or more touches on Monday would be a 2.5σ event** and is the tripwire to check.
- **Entry price should sit ~0.5c above mid, not 1c** (measured book half-spread) — a fill
  materially worse than mid+1c means we crossed more than the top of book.

**Concrete book state as of the last non-duplicate snap (Sun, ttc 27–47h)** — the wing sits
1–6 strikes above spot, all comfortably inside ceiling:
gold in-band strikes **4085 / 4095 / 4105 / 4115 / 4125 / 4135** (NO ask 67/73/80/85/90/93 vs
ceilings 68.7/80.9/81.3/90.5/95.2/97.0 — **6/6 TAKE**);
silver **60 / 60.25 / 60.5 / 60.75** (NO ask 77/83/88/95 vs 81.3/90.5/95.2/97.0 — **4/4 TAKE**).
Spot will move by Monday; the *count* predictions above, not these strikes, are the test.

## 6. Assumptions & caveats (flagged)

- **No entry-window book data exists** (§0). Every §2/§3 number is a 1h pseudo-window at
  ttc 27–77h on Fri-evening/Sat/Sun. Books at T-3h on a Monday will be **more active** than
  this — the 68% frozen fraction is an **upper bound on inertia**, so the true oracle gap could
  exceed +0.31c. It would have to grow **20×** to justify any waiting policy, so the verdict is
  robust to this, but the fill table itself is indicative only.
- **Touch rates are exogenous** — taken from calib, not re-estimated. Bucket 0.30–0.35 shows
  realized EV **−13.32c on the 9 qualifying obs (se 16.6)** while its gap is still +8pp implied
  vs realized; n=9 is far too thin to act on. **Monitor, do not narrow `wing_hi` on this.**
- **Bucket assignment in §2/§3 uses the book yes-mid**; calib assigns from the T-3h last print.
  A rung near a boundary can bucket differently live.
- **Ceiling arithmetic** solves p = 100(1−touch) − 2.0 − fee(p) by fixed point; if the live
  crate uses fee at a different reference price the ceilings shift ≲0.2c. Not verified against
  the crate (nestor repo not touched, per constraint).
- **Maker leg unshippable regardless** — toxicity unmeasured (house probe pending), maker fee
  schedule unverified. It is presented as analysis only and it loses on price anyway.
- **Sample: 29 Mon-Wed dates, 87 events, 336 wing rungs**, ~2 months (26MAY28–26JUL01 range in
  the corpus). Regime-narrow. Copper is a single ~9-week series at half weight and has **no
  book capture at all**.
- **Money impact by reconstruction only**: +7.9c/contract × ~11 rungs/day ≈ **+87c per contract
  of size per Monday**; at 10 contracts/rung ≈ **+$8.7/day**, p10 −$5.6, p90 +$15.3 (n=29 days).
  Small in absolute terms — the deliverable here is *not losing* the 8c to execution.
- Scripts (analysis only, outside the nestor repo): `~/kalshi_data/scripts/volbook_exec_obs.py`,
  `volbook_exec_book.py`, `volbook_exec_policy.py`.
