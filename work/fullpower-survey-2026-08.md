# FULL-POWER KALSHI VENUE SURVEY — 2026-08-06

Re-run of the venue calibration survey at ~100× the first pass's API throughput, built
specifically to test **Ryan's claim: at least one trade, ~once a day, earning ~6.5% on the
capital in the trade (~3¢/contract net on a mid-priced contract).** The first survey's cell
SEs were 3–9¢ and could only rule out ≥5¢ edges — this claim sat **below its detection floor.**
Target here: MDE ~1¢/cell.

Artifacts (all local, `~/kalshi_data/hunt/`): `fp_lib.py` (reader + hygiene primitives),
`fp_plan.py` / `fp_pull.py` (the pull), `fp_taker.py`, `fp_maker.py`, `fp_tape.py`,
`fp_ceiling.py`, `fp_cand.py`, `fp_gates.py`, `fp_verdict.py`; data in `fp_c/`, `br_c/`,
outputs `fp_*.json` / `fp_*.out`.

---

## 0. THE HEADLINE

**The claim does not survive, and it is now bounded by data rather than by power.**

Pooled across every family, **mid-priced (40–60¢), event-clustered, one market per event**:

| quantity | measured | n | events |
|---|---|---|---|
| gross calibration gap (`100·1{YES} − mid`) | **−0.85¢ ± 0.69** | 41,488 | 7,534 |
| taker buy YES at the real ask, after real fees | **−4.53¢ ± 0.69** | 41,488 | 7,534 |
| taker buy NO at the real ask (`100 − yes_bid`), after real fees | **−2.82¢ ± 0.69** | 41,488 | 7,534 |
| mean spread / mean fee / **round toll** | 3.91¢ / 1.72¢ / **3.68¢** | | |

To net **+3.00¢** as a taker at mid you need a gross mispricing of **+6.68¢**.
Measured: **−0.85 ± 0.69 ⇒ the claim is excluded at 10.9 σ in the 40–60¢ band**
(8.0 σ at 45–55¢, 15.2 σ at 30–70¢). This is no longer a power statement.

**The maker version of the claim dies on arithmetic before it dies on statistics.**
Across 70,985 markets / 27.0M quoted minutes / **9.94 billion contracts** of causal
same-minute volume:

- **84.0% of all traded volume is in a 1¢ book. 93.4% is in ≤2¢.**
- **Volume-weighted mean spread 1.51¢** (minute-weighted 4.60¢ — wide books exist,
  but nothing trades in them).
- A 3¢ maker edge requires a ≥6¢ spread to have a 3¢ half-spread to capture at all.
  **Only 2.50% of the venue's traded volume is in books that wide** — and the measured
  adverse selection there is 2–3× the half-spread, so those cells are the *worst*
  on the surface, not the best.

⇒ **In the liquid half of Kalshi the entire maker prize is ≤0.5¢/contract, before adverse
selection.** 3¢ is not available to be earned there at any size.

---

## 1. 🔧 NEW HYGIENE LAW — **AN ABSENT FIELD MUST FAIL LOUD, NOT FAIL SHUT**
### (cost: one +5.79¢ / t=5.15 "survivor" that cleared six gates)

The single strongest cell this survey produced was
**KXMLBGAME, buy YES at the ask, mid 85–95¢, inside 30 min of the anchor:
+5.79¢ ± 1.12, t = +5.15, FWER = 0.000, 165 events, 6.3% of 91¢ of capital** —
numerically *exactly* Ryan's claim.

It cleared **Gate 7** (shallow +6.56 / deep +5.06), **Gate 4 hash halves** (+5.24 / +6.36),
**Gate 4 chronological halves** (+6.93 / +4.58), **Gate 2** (displaced anchor flips to
−8.45, so the time axis carries information), **smoothness** across neighbouring price
bands (70–80 +13.76 → 95–99 +1.17), and **one-obs-per-event** (+5.16, t=3.86).

It was 100% lookahead. The anchor code read:

```python
anc = amax[m['ev']]                                    # max close_ts in the event
if anchor == 'auto' and sc and m['a'] and sc > m['a']:  # <-- m['a'] is open_ts
    anc = sc                                            # scheduled time from ticker
```

`ig_K_MLBGAME.jsonl` rows **carry no `open_ts` field**. So `m['a']` was `None`, the guard
short-circuited, the scheduled-time override never fired, and **every anchor silently became
`close_ts` = the moment the game ended.** "30 minutes before the anchor" was 30 minutes
before the outcome was known. A 90.7¢ favourite realising **97.6% YES** is not a mispricing,
it is the ninth inning.

With the anchor repaired the cell contains **zero rows** — pre-game MLBGAME never sits at
85–95¢ at all.

**Why every gate passed:** the leak was present in *both* halves, at *every* retrieval depth,
and in *every* price band, so no split could see it — the same failure mode as the crypto
early-window pocket ("split-half cannot detect a bias present in both halves"). Gate 2 did
not catch it either; the displaced anchor was *also* leaked, just less.

**LAW: a guard on a field that may be absent must raise, not silently take the other branch.
Print the anchor provenance and cluster count for every panel you build.** `fp_lib.panel`
now emits `asrc` per row (1 = scheduled-time-from-ticker, 2 = close_ts shared by every market
in the event ⇒ a scheduled boundary, outcome-independent, 0 = close_ts that *varies* inside
the event ⇒ outcome-dependent, dropped from every primary scan).

Current mix: **60–67% ticker-scheduled, 33–40% shared-close, 0 rows on a varying close.**

---

## 2. THE TAKER SURFACE — nothing clears the bar

Scan: **202 cells** = 61 families (+ pooled) × 2 sides × 9 price bands × 3 tau bands,
event-clustered, ≥150 event clusters per cell, selection corrected by a **250-draw
event-sign-flip maxT permutation over the entire scanned grid**
(null max|t|: median 2.63, 95th 3.49, 99th 3.86).

**No cell anywhere clears +3¢ net with family-wise significance.** Best FWER on any
positive cell is **0.960**.

### The 3 closest cells, and exactly what each needs

| cell | net after full toll | t | FWER | what it needs |
|---|---|---|---|---|
| **KXMLBSPREAD** no, mid 30–45, T−30m…2h (n=468, **245 events**) | **+5.91¢ ± 2.88** | +2.05 | 0.960 | **523 events** (2.1×) at the same effect size to reach t=3; currently 9.1% of 65¢ capital |
| **KXMLBSPREAD** no, mid 30–45, T−0…30m (n=1,228, **251 events**) | **+5.24¢ ± 2.86** | +1.83 | 0.996 | **674 events** (2.7×) |
| **KXMLBTOTAL** no, mid 30–45, T−2…24h (n=473, **158 events**) | **+5.23¢ ± 3.93** | +1.33 | 1.000 | **801 events** (5.1×) |

All three are the same trade — **buy NO at ~65¢ in an MLB derivative whose YES sits at
30–45¢** — and all three are the mirror of the corpus's existing
"cheap YES is dear by ~1¢" mechanism, here reading ~5¢ rather than ~1¢. Cadence is not the
problem (KXMLBSPREAD runs ~12 events/day); **event count is.** If the +5.91¢ were real it
would be **$91/day at $1,000** (1,538 contracts × 5.91¢, one turn/day) — but ±$44/day, and
family-wise p = 0.96. **Do not trade this. It is the leading candidate for the next pass,
not a result.**

⚠️ Note the shape of the top-12 list: **every one of the twelve highest-net cells is an MLB
prop family**, and the same 30–45¢-NO structure recurs across KXMLBSPREAD / KXMLBTOTAL /
KXMLBF5TOTAL / KXMLBRBI. That is either one real family-wide mechanism or one shared
mis-specification. Held-out families are the test, and the corpus's record on this is
**four for four against** (`heldout_longshot.py`).

### Achieved MDE — the claim is now bounded by data, not by power

| price band | events | gap | SE | **MDE (95%)** |
|---|---|---|---|---|
| 1–5¢ | 3,252 | −1.23 | 0.210 | **0.41¢** |
| 5–15¢ | 5,980 | −1.04 | 0.466 | **0.91¢** |
| 15–30¢ | 6,351 | −0.21 | 0.652 | **1.28¢** |
| 30–45¢ | 5,920 | −2.43 | 0.774 | **1.52¢** |
| **45–55¢** | **4,498** | **−0.45** | **0.933** | **1.83¢** |
| 55–70¢ | 3,632 | +0.35 | 1.031 | **2.02¢** |
| 70–85¢ | 2,627 | +1.96 | 1.083 | **2.12¢** |
| 85–95¢ | 1,985 | +1.39 | 0.887 | **1.74¢** |
| 95–99¢ | 1,364 | +1.76 | 0.399 | **0.78¢** |

**First survey: 3–9¢. This survey: 0.41–2.12¢, and the pull is ~17% complete.**
The 1¢ mandate is met at the extremes already and will be met across the middle at
full pull. Per-family MDE currently spans **3.5¢ (KXMLBRFI, 849 events)** to 17¢ for the
long tail; families below ~150 event clusters remain unresolvable at this scale and always
will be — they do not have enough history to test.

---

## 3. THE MAKER SIDE — surveyed for the first time

Two independent instruments, because each has an opposite bias.

### (a) Candle instrument — conservative, venue-wide
Post passively 1¢ inside the touch (**passivity checked** — the bug `maker_favstrat.py`
omits); fill only when the opposite side of the book crosses *through* our price. One
**first-qualifying** attempt per market (the attempt-weighting law). Zero maker fees.

Pooled, **mid 30–70¢, 14,067 events, 136,120 attempts, 13,948 fills** — both sides
negative, and tightly:

| cell | λ | markout +30m | settlement EV/fill |
|---|---|---|---|
| sell YES passive, mid 40–60 (8,651 ev) | 0.129 | **−1.36 ± 0.23** (t −5.94) | −3.83 ± 1.34 (t −2.85) |
| buy YES passive, mid 40–60 (8,651 ev) | 0.126 | **−2.63 ± 0.29** (t −9.04) | −4.34 ± 1.35 (t −3.22) |
| sell YES passive, mid 30–70 (14,067 ev) | 0.116 | **−1.70 ± 0.18** (t −9.57) | −3.59 ± 1.05 (t −3.42) |
| buy YES passive, mid 30–70 (14,067 ev) | 0.117 | **−2.38 ± 0.21** (t −11.48) | −3.98 ± 1.05 (t −3.80) |

**Markout SEs of 0.18–0.29¢ ⇒ MDE ≈ 0.4–0.6¢ on the maker surface — well inside the
1¢ mandate.** Both sides negative is the signature of adverse selection exceeding the
half-spread, not of a side bias.

🔑 **AND THE MECHANISM: adverse selection grows with the spread FASTER than the
half-spread does.** Mid 30–70, markout +30m by spread bucket:

| spread | half-spread on offer | markout, sell YES | markout, buy YES | λ |
|---|---|---|---|---|
| 1–3¢ | 0.5–1.5¢ | −0.80 ± 0.14 | −1.47 ± 0.20 | 0.21 |
| 3–6¢ | 1.5–3¢ | −2.39 ± 0.50 | −2.47 ± 0.46 | 0.15 |
| **6–10¢** | **3–5¢** | **−3.38 ± 1.43** | **−3.41 ± 1.30** | 0.05 |
| 10–20¢ | 5–10¢ | −2.20 ± 1.35 | −6.75 ± 1.38 | 0.06 |
| 20–61¢ | 10–30¢ | −9.01 ± 1.34 | −6.56 ± 1.09 | 0.02 |

**A wide book is not an invitation, it is a warning**, and λ collapses 10× exactly where
the spread is finally wide enough to pay 3¢. This is the death of the "be the maker in a
wide mid-priced book once a day" version of the claim, measured rather than argued.

⚠️ **Honest bias statement:** this fill rule only counts fills where the book crossed
*through* our price, so it samples the adversely-selected subset — these markouts are an
**upper bound on badness**. Instrument (b) below has the opposite (optimistic) bias. The
truth is between them, and 3¢ is outside both.

*(An earlier −3.58¢, t=−2.51 reading of the narrow-spread cell was itself anchor-contaminated;
it moved to −0.40 ± 1.27 once §1 was fixed. Reported so the record is honest.)*

### (b) Trade-tape instrument — realistic, one family
`ig_trades.jsonl` (real prints with taker side) joined to 1-min books on **KXMLBGAME**,
the venue's largest market (61.3M contracts/day). Fill = a real taker print at our price
with the opposite taker side, on a **fixed offset grid identical for every market**, so a
market cannot earn extra attempts by winning. 5,052 attempts, 4,291 fills.

- **λ is not the constraint: 0.69–0.98 fill probability within 30 minutes.** A resting quote
  in a liquid Kalshi market fills many times a day. "Once a day" is trivially available.
- **The edge is the constraint.** Markouts at +30m are ~zero everywhere:
  −0.83 … +1.21¢, |t| < 2 in 13 of 14 cells. The one tight cell is
  **JOIN sell-YES, mid 45–55: mo30 = +0.43¢, t = +3.17, λ = 0.948, 663 fills** —
  i.e. a maker at the touch in a 1¢ book keeps ~0.43¢ of the 0.5¢ half-spread. Real,
  reproducible, and **7× too small to be Ryan's trade.**
- Improving the touch is usually *impossible*: in a 1¢ book any improvement is no longer
  passive, so the maker must win a queue race for ≤0.5¢.

🔑 **Both instruments agree on the conclusion despite pulling in opposite directions.**
Optimistic instrument (b): best cell **+0.43¢**. Pessimistic instrument (a): **−1.7 to −2.4¢**.
The maker prize on liquid Kalshi is the half-spread; the half-spread is **0.5–0.75¢ where the
volume actually is**; adverse selection takes most or all of it; and the only books wide
enough to pay 3¢ are the ones where adverse selection is 3–9¢ and λ has collapsed by 10×.
This is exactly the existing LIP/seats business — the survey neither adds to it nor
subtracts from it.

---

## 4. ECON — closed permanently, by the API and not by the analysis

The first survey flagged ECON as the one wing where structural mispricing was plausible
a priori, and hoped full history was "20–50 events each, one gentle pull". **It is not
retrievable, authenticated or otherwise.**

`/events?series_ticker=KXCPI&status=settled` lists **61 CPI events back to 2021.** For each,
both `/markets?event_ticker=` and `/events/{t}?with_nested_markets=true` return markets only
for the **two most recent**:

| event | markets returned |
|---|---|
| KXCPI-26JUN | 9 |
| KXCPI-26MAY | 14 |
| KXCPI-26APR … CPI-21JUN (59 events) | **0** |

Same for KXFED (40 listed, 11 retrievable in 1 event) and KXPAYROLLS (40 listed, 15 in 1).
Total usable ECON universe: **154 markets across 11 event clusters** — unchanged from the
first survey. **MDE ≈ 15¢. No amount of compute or throughput reopens this wing.**
Combined with the already-established structural kill (every scheduled-release series stops
trading 1–5 min *before* the print), ECON is closed. Do not reopen it.

---

## 5. PULL STATUS (running, resumable, nothing to wait on)

**Design change that matters:** MDE is bounded by **independent event clusters**, not by
markets. The settled catalogue (`recal_mkts.jsonl`, 1,124,409 markets) contains
**157,195 distinct events**. The already-running breadth pull (`br_pull.py`, another
session's, 120 markets/series × 2,283 series) covers **at most 19,473 events** — a breadth
design, 8× weaker for MDE. So this survey added a complementary **event-coverage** pull:
**one hash-chosen market per event, all 157,195 events**, priority-ordered so partial results
decide early.

- `fp_pull.py` — `nohup`, resumable (skips tickers already in `fp_c/<SER>.jsonl`),
  authenticated signed GETs, no new cron entries, read-only. Log `fp_pull.log`.
- **Tier 0 COMPLETE**: all 44 priority families (MLB game + all props, WNBAGAME, INX /
  NASDAQ100 / INXU / NASDAQ100U / DJI, all daily and hourly crypto ladders, WTI/GOLD/SILVER
  hourly, all six ECON series) — **17,802 events landed.**
- Tier 1 (76,666 events, the non-crypto-15M tail) in progress; tier 2 (weather, 5,671);
  tier 3 (crypto 15M, 57,056 — known-dead, pure power filler) last.
- Rate ~800–1,030 events/min while sharing the limit with the other session's pull;
  **23,666 events landed / 312 MB across 167 series files at time of writing.**
  ETA to full coverage ≈ 2–2.5 h from now.
- Every number in this document is from the events landed so far plus the pre-existing
  MLB corpus (**75,605 markets; 26,475 events in the clean taker panel; 14,067 events in
  the maker panel**).
  **All headline conclusions get tighter, not different, as the rest lands** — the pooled
  mid-band exclusion is already 8–15 σ.

**Re-run to refresh:** `python3 fp_verdict.py`, `python3 fp_maker.py disk`,
`python3 fp_ceiling.py` — all read whatever is on disk.

---

## 6. WHAT THIS SURVEY ADDS TO THE STRATEGY BOOK

1. **Ryan's ~3¢/6.5% claim is excluded for the taker at mid prices at 8–15 σ**, and excluded
   for the maker in the liquid half of the venue by the spread arithmetic (84% of volume in
   1¢ books). It is **not** excluded in the illiquid tail — but there the toll is 8–20¢ and
   there is no volume to trade against, which is the same kill the first survey found.
2. **New hygiene law (§1): a guard on a possibly-absent field must fail loud.** This one bug
   manufactured a cell that passed Gates 2, 4, 5 and 7 and looked exactly like the target.
3. **λ is never the binding constraint on Kalshi** (0.69–0.98 per 30 min in liquid markets).
   Every future maker proposal should be argued on markout, never on fill rate.
4. **MDE improved from 3–9¢ to 0.41–2.12¢ per price band** at ~17% of the pull.
5. **ECON is closed by the API**, not by power — retire the "one gentle pull" note.
6. The **event-coverage pull design** (one market per event across the whole settled
   catalogue) is the reusable asset: it is 8× more powerful per API call than breadth
   pulling, because clusters and not rows are what buy MDE.
