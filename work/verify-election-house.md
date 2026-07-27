# VERIFY: ELECTION-LADDER HOUSE (VENUE-MECHANICS V5 → HOUSE-FEE) — 2026-07-27

Charter: Ryan-ordered validation of `work/lane-VENUE-MECHANICS-jul27.md` idea **V5**, promoted
out of VENUE-MECHANICS as CONDITIONAL(gate: election / name-recognition ladders).

**Claim under test:** "In the first hours after a new name-recognition election ladder lists,
rest 1-lot two-sided quotes inside the wide spreads and earn the spread from the opening retail
rush."

**VERDICT: DEAD (structural). The premise is factually false — there is no retail rush, and the
one measurable instance of it was an inventory transfer at 1¢ that a two-sided quoter cannot be
on the right side of.** A 1-lot quoter at ±2/3/4¢ around the evolving mid on the KXAKSEN1 open
loses **−$0.91 / −$1.03 / −$1.85 at +60 s markout** and **−$2.08 / −$2.23 / −$3.52 at +300 s**,
with **100 % of fills on one side (all buys)** — the textbook adverse-selection fingerprint.

Read-only. No orders, no keys touched. All numbers from the public trades endpoint plus the
already-captured local 2 s book tape.

---
## Sources

| What | Path |
|---|---|
| Live 2 s book tape across the KXAKSEN1 / KXWIDGOV2ND opens | `/Users/ryanwhitehead/kalshi_data/vm_newseries_open.jsonl` (1,444 lines, 716 KXAKSEN1 snaps, T0−250 s → T0+1288 s) |
| Full public trade tape, 4 KXAKSEN1 rungs | `https://api.elections.kalshi.com/trade-api/v2/markets/trades` → `/private/tmp/claude-501/-Users-ryanwhitehead/449dc817-6064-457d-a116-2df58b67bcb2/scratchpad/raw_{MPEL,DSSUL,DJSUL,DDAR}_0.json` (**37 trades total, complete — single page each**) |
| Counterfactual quoter | `…/scratchpad/sim.py` |
| Listing cadence | `/Users/ryanwhitehead/kalshi_data/listing_events.jsonl` (8 NEW_SERIES / 2.11 d), `catalog_open_202607.json` (8,437 events, 978 Elections series) |
| Live OI/volume census | `/trade-api/v2/markets?event_ticker=…` — **field is `volume_fp` / `open_interest_fp`, NOT `volume` / `open_interest`** (the plain names exist and are always `0` — a silent-zero trap that cost one pass here; record it) |

T0 = 2026-07-27 16:00:00Z (KXAKSEN1 open).

---
## Q1 — What the "2,019 contracts in 13 min" actually were

The full tape is **37 trades** over T0+380 s → T0+1735 s. Decomposition:

| bucket | contracts | trades | what it is |
|---|---|---|---|
| 1¢ prints on the two dead longshots (DJSUL, DDAR) | **6,006** (2,003 inside 13 min; 4,003 more by T0+29 min) | 16 | two ~1,000-lot and two ~2,000-lot sweeps, taker = **buying NO at 99¢** |
| everything else (MPEL 7 · DSSUL 5 · DJSUL 4) | **16** | 21 | 1-lot MM price-discovery prints |

**Non-1¢ volume in the entire first 29 minutes of this ladder: 16 contracts.** The headline
2,019 is 2,003 longshot contracts + 16 real ones. The Mesh entry (and the V5 rescue that rested
on it) took the volume number at face value; the tape does not support it.

**What spread did the trades cross** (prevailing top-of-book at the last snapshot ≥2 s before
each print):

| spread crossed | contracts | share of volume |
|---|---|---|
| 98¢ (the fresh 0.99/0.01 seed) | 8 | 0.13 % |
| 48¢ | 1 | 0.02 % |
| 44¢ | 0.01 | — |
| 24¢ | 3 | 0.05 % |
| 3¢ | 4 | 0.07 % |
| **2¢** | 1,001 | 16.6 % |
| **1¢** | 5,007 | 83.1 % |
| **size-weighted mean** | — | **1.3¢** |

**99.7 % of the volume crossed a 1–2¢ spread.** The 98¢ seed spread was crossed by **eight
contracts, total**. The lane's entire thesis — "wide spreads getting crossed by arriving flow" —
is refuted on its own showcase event: by the time any size arrives the book is already 1¢ wide,
and the wide-spread window is crossed only by 1-lot MM discovery.

Book path (from the 2 s tape, T0-relative):

```
 +34.6s  all 4 rungs seeded 0.99/0.01, 896×896        (V4's dutchbook seed, reconfirmed)
 +80.9s  MPEL bid walks to 0.75 — the venue MM arrives, one rung only
+380.0s  first trades: 1-lot on all 4 rungs simultaneously, taker=no
+399.5s  MPEL 0.88/0.85 · others 0.45/0.01 — repriced by a single MM, not by flow
+499.0s  MPEL 0.83/0.78 · DSSUL 0.18/0.14 · longshots 0.03/0.01  -> spreads already 4-5c
+701.5s  DJSUL 1,000-lot sweep at 1c ; +708.5s DDAR same
+1288s   final: MPEL 0.79/0.78 · DSSUL 0.19/0.15 · longshots 0.02/0.01
```

The book was repriced from the 98¢ seed to a real prior **by a market maker moving its own
quotes**, not by trades. Price formation here consumed 8 contracts of liquidity.

## Q2 — The counterfactual 1-lot two-sided quoter (house-crate markout method, maker fee $0)

Rules: at every trade, take the mid from the last book snapshot **strictly ≥2 s before** the
print (no lookahead); rest bid = mid−K, ask = mid+K, 1 lot, clamped [1,99]; fill if the print
would have crossed the quote; fill at **my** price; markout = signed (mid at t+h − fill price);
fee = $0 on the maker side.

| K | fills | side split | mk +60 s | mk +300 s | mk terminal (T0+1288 s) | fills with mk300 < 0 |
|---|---|---|---|---|---|---|
| ±2¢ | 31 | **31 buy / 0 sell** | **−185.0¢ = −$1.850** | **−351.5¢ = −$3.515** | −363.0¢ = −$3.630 | 13 / 31 |
| ±3¢ | 28 | **28 buy / 0 sell** | **−103.0¢ = −$1.030** | **−222.5¢ = −$2.225** | −230.0¢ = −$2.300 | 10 / 28 |
| ±4¢ | 27 | **27 buy / 0 sell** | **−90.5¢ = −$0.905** | **−208.0¢ = −$2.080** | −215.5¢ = −$2.155 | 9 / 27 |

**Not one fill on the ask side, at any K.** The flow was 100 % directional (selling YES / buying
NO). A two-sided quoter never round-trips a spread here; it is a one-way inventory sink. That is
the graveyard's "maker = pickoff" in its purest observed form — 100 % one-sided, not 91 %.

The damage is concentrated in the fills that matter (K = ±4¢):

| t | rung | fill | mid at fill | mk+60 | mk+300 | mk term |
|---|---|---|---|---|---|---|
| +380.0 | DDAR | B @46¢ | 50.0 | −23.0 | −44.5 | −44.5 |
| +380.0 | DSSUL | B @46¢ | 50.0 | −23.0 | −25.5 | −29.0 |
| +382.5 | DJSUL | B @46¢ | 50.0 | −21.0 | −44.0 | −44.5 |
| +382.5 | DDAR | B @46¢ | 50.0 | −23.0 | −44.5 | −44.5 |
| +382.5 | DSSUL | B @46¢ | 50.0 | −23.0 | −25.5 | −29.0 |
| +420.9 | DJSUL | B @21¢ | 25.0 | +3.0 | −19.5 | −19.5 |
| +380–382 | MPEL ×3 | B @83¢ | 87.0 | +3.5 ea | −4.5 ea | −4.5 ea |
| +688–1735 | DJSUL/DDAR ×18 | B @1¢ | 1.5 | +0.5 ea | +0.5 ea | +0.5 ea |

Read the mechanism straight off the table: **the seed mid is a lie.** 0.99/0.01 mids at 50¢, so
a "±4¢ around mid" quoter posts a 46¢ bid on a candidate whose true prior is 1–2¢, and the first
MM that crosses the seed lifts exactly that. Five such fills = −$1.87 of the −$2.08. There is no
K that fixes this: widening K only reduces participation in a flow that was never two-sided.

**Two adjustments, both against the strategy:**
- Restricting the quoter to *contested* rungs only (mid 5–95¢, the strongest possible version of
  the idea) removes every positive fill and keeps every negative one: **−217.5¢ = −$2.175 at
  mk300 on 9 fills, 9/9 adverse.**
- The 18 "profitable" +0.5¢ fills are an artifact of mid = 1.5 on a 1/2 book. Those are 1¢
  longshots (Alaska Senate advance also-rans) that will settle at 0. Marked to settlement they
  are −1¢ each: **an additional −27¢**, taking mk-to-settlement to ≈ **−$2.43** at K = ±4¢.

**Adverse selection, explicitly:** mk+60 −$0.905 → mk+300 −$2.080 at K = ±4¢. **The markout gets
2.3× worse between +60 s and +300 s.** Price keeps running away from the fill for at least five
minutes; there is no mean-reversion of the initial impact to harvest. The classic maker defence
("I'll flatten quickly") is worth nothing — even instantaneous flattening at the +60 s mid still
loses $0.91.

**Counterfactual P&L, headline: a 1-lot two-sided quoter on the KXAKSEN1 open earns $0.00 of
spread and loses $0.91–$3.52 depending on K and horizon. Gross spread captured: zero, because
zero round-trips occurred.**

## Q3 — Cadence (what bounds $/week)

Two independent counts:

- **`listing_events.jsonl`**, 2026-07-24 19:09 → 07-26 21:46 (**2.11 days**): 8 `NEW_SERIES`, of
  which **2 are category Elections** (KXRPRESPRIMARY, KXDPRESPRIMARY — same 19:49 batch), plus
  554 `BOOK_LIVE`. Adding the 2 caught live on 07-27 (KXAKSEN1, KXWIDGOV2ND): **4 election
  ladders in ~3.3 days ≈ 8.5/week**, but arriving in same-minute batches of 2, not spread out.
- **`catalog_open_202607.json`** first-market `created_time`, Elections category, ≥3 rungs =
  "ladder": 328 ladders lifetime. Rate: **9.7 ladders/week since 2026-05-01** (116 over 84 d);
  **20.1/week over Jul 1–24** (66); median ISO-week over 2026 ≈ **4**, with fat batch weeks
  (2026-W28 = 44, 2026-W02 = 354 — the annual Elections dump). Undercount, since the catalog is
  open markets only.

**Cadence number: ~10 election/name-recognition ladders per week (median week 4, batch weeks 20–44).**

$/week ceiling, computed generously in the strategy's own favour: suppose every ladder delivered
KXAKSEN1's 16 contracts of real flow and you captured a **full 4¢ on every one of them with zero
adverse selection** — $0.64/ladder × 10 = **$6.40/week gross**. The measured reality is
−$2.08/ladder → **−$21/week**. The cadence does not bound a business either way; the sign does.

## Q4 — What separates the ladders that trade from the OI-0 graveyard

Live `volume_fp`/`open_interest_fp` for every Elections ladder created within 10 days of the
Jul-24 catalog snapshot (queried 2026-07-27, so 3–13 days of age):

| created | ladder | n | vol | OI |
|---|---|---|---|---|
| 07-20 | KXGUAMGOV-26 | 5 | 63,796 | 21,486 |
| 07-17 | KXTRUMPENDORSESC-26AUG11 | 5 | 43,665 | 25,144 |
| 07-20 | KXVOTEPRIMARY-GOVWINOMD26FHONFHON | 8 | 33,477 | 11,181 |
| 07-20 | KXINDIANPM-29APR30 | 9 | 31,520 | 10,831 |
| 07-20 | KXISRAELGOVT-28JAN01 | 15 | 25,674 | 14,652 |
| 07-20 | KXPRIMARYMOV-GOVWINOMD26 | 12 | 23,020 | 15,709 |
| 07-20 | KXLIMAMAYOR-26OCT04 | 10 | 12,531 | 3,173 |
| 07-17 | KXPRIMARYMOV-AZ5R26 | 10 | 10,403 | 6,152 |
| 07-14 | KXMANCHESTER2ND-… | 6 | 9,537 | 4,449 |
| 07-21 | KXPRIMARYPLACE-KXSCRSENS26-2 | 4 | 6,350 | 3,033 |
| 07-14 | KXMANCHESTERMOV-… | 10 | 5,275 | 2,395 |
| 07-21 | KXCEARAGOV-26OCT04 | 3 | 4,834 | 2,362 |
| 07-15 | KXSANTACATARINA-26OCT04 | 5 | 1,496 | 417 |
| 07-24 | KXPRIMARYMOV-GOVSDNOMRLRHO | 9 | 986 | 971 |
| 07-20 | KXNMARIANAGOV-26 | 4 | 328 | 68 |
| 07-20 | KXMISC-26 (MI Supreme Court) | 3 | 9 | 8 |
| 07-20 | KXFIJIPM-27FEB06 | 5 | 2 | 2 |
| 07-24 | KXDPRESPRIMARY-28SC · KXPRIMARYMOV-MO01D26 · 07-21 KXCDUCSULEAD · KXPRIMARYMOV-SCRSENS26DNOR · KXVOTEPRIMARY-SCRSENS26DNORDNOR · 07-20 KXUSVIGOV · KXVICTORIANSTATE | — | **0** | **0** |

**Answer to "is there an ex-ante tell": yes, and it kills the strategy rather than saving it.**
The tell is *US-retail salience* — US jurisdiction and/or a named US political figure
(Guam Governor, a Trump endorsement, a Wisconsin Dem primary, an AZ-05 primary all trade
heavily; Fiji PM = 2 contracts, Michigan Supreme Court = 9, Victorian state = 0). But it is a
tell about **cumulative multi-day volume, not about hour-1 flow**, and hour-1 is the only window
this strategy has. The two ladders we watched live prove the separation:

- **KXAKSEN1** (Alaska Senate — maximally US-salient, a real 2026 race): **12 contracts of real
  volume in its first 24 h** across both contested rungs (MPEL 8, DSSUL 4). The 6,006 remaining
  are 1¢ longshot inventory.
- **KXWIDGOV2ND** (Wisconsin Gov 2nd place): after 24 h, MBAR 502 / DCRO **0** / FHON **0** —
  and the two *priced* rungs (DCRO 0.68/0.72, FHON 0.08/0.12) have **zero** volume. Its one
  trading rung is the one nobody has repriced.

So the Mesh's "16/17 wide-seed books end at OI 0" and the "election ladders convert" observation
are **not** in conflict once you separate the horizons: election ladders *do* eventually accrue
OI (days), and they *do not* trade in hour 1 (12 contracts). V5's premise conflated the two.
The 16/17 OI-0 result was never the thing to rescue — the thing to rescue was hour-1 flow, and
hour-1 flow does not exist on either the econ ladders or the election ladders.

**Corollary for the Mesh:** "new listings are sloppy for ~48 h — wide books, no bots" needs
amending. On these opens a venue MM was quoting **80.9 s after seed** and had the book at 4–5¢
by **T0+8 min**. There are bots. The 98¢ window is 6 minutes long and 8 contracts deep.

## Q5 — Integration sketch (NOT implemented, per charter)

Recording it only so the cost is on the record next to the −$2.08. The house crate quotes a
fixed, pre-configured book list; a listing-triggered vehicle needs, minimally:

1. `listing_monitor` gains an **emit** path: on `NEW_SERIES` it already knows the series ~7–393
   min before the first market's `created_time`; it would publish `{series, event_ticker, rungs,
   open_time}` onto a queue/file the house process tails (it currently only appends to
   `listing_events.jsonl` for humans).
2. House gains a **dynamic universe**: today its market list is static config. It needs (a) an
   ingest loop, (b) per-market state created at runtime, (c) an eviction rule (drop the ladder
   at T0+2 h or on OI-0 timeout) — the crate's book-state map and its config loader both assume
   fixed cardinality at startup.
3. A **seed-aware fair value**. The measured killer is that mid = 50¢ on a 0.99/0.01 book is
   meaningless. House would need to *refuse to quote until a non-seed price exists* — i.e. wait
   for the MM at T0+80 s to +8 min — by which point the spread is 4¢ and the residual is gone.
   This requirement is self-defeating and is the reason not to build it.
4. Mutual-exclusivity awareness across rungs (a ladder is one event; quoting 4 rungs
   independently is 4 correlated inventory positions), which the crate has no concept of.

That is 3 nontrivial subsystems (dynamic universe, seed-aware FV, ladder inventory) for a
measured **−$2.08 per event at ~10 events/week**. **Do not wire it.**

---
## Verdict and taxonomy

**DEAD (structural)** — not "conditional, needs a better gate". Three independent structural
facts, any one of which is sufficient:

1. **The flow is one-sided.** 27–31 fills, 0 sells. A two-sided quoter cannot earn a spread from
   flow that only ever goes one way; it accumulates inventory at the wrong price.
2. **The wide spread is not where the volume is.** Size-weighted spread crossed = 1.3¢; the 98¢
   seed absorbed 8 contracts. You cannot be paid a spread nobody crosses.
3. **The seed mid is not a fair value.** Quoting around it is quoting around 50¢ on 1¢ paper.
   Waiting for a real mid means waiting past the window.

Adverse selection measured and confirmed: **mk+60 s −$0.905 → mk+300 s −$2.080 (K = ±4¢), 2.3×
deterioration, 9/9 adverse on contested rungs.** The graveyard line ("maker/resting orders =
informed-flow magnets, 91–100 % pickoff") now has a fourth proof, and its strongest one — 100 %
one-sided fills, in the exact venue-mechanics setting that was supposed to be the exception.

**Mesh amendments earned by this lane:**
- KXAKSEN1's "2,019 contracts in 13 min" → **16 real contracts + 2,003 at 1¢**. The number that
  rescued V5 was a mis-read; V5's CONDITIONAL should be downgraded to DEAD.
- "New listings are sloppy for ~48 h, no bots" → **a venue MM quotes within ~81 s and has the
  book at 4–5¢ by T0+8 min.** The sloppy window is ~6 min and ~8 contracts deep.
- API trap: market volume/OI live at **`volume_fp` / `open_interest_fp`**; the plain `volume` /
  `open_interest` fields exist and silently return 0 everywhere (76,186/76,186 markets in the
  local catalog). Any past analysis that read `volume` measured nothing.
