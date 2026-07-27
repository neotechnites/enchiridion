# LANE BEZOS-TAPE (2026-07-27) — depth-mine our own participation record

Charter: `work/steer-ideation-jul27.md` lane 6. Mandate: entry-price-bucket win rates per
coin (Ryan's floor question), maker_rest vs taker_backstop fill quality, whatever else the
tape volunteers. **SMALL-N HONESTY is the grading criterion — every n labelled, no verdict
beyond the n.**

## Data used (all Mac-on-disk, current to the 2026-07-26 VPS cutover)

| source | what | n |
|---|---|---|
| `nestor/data/streak_week1.jsonl` | our signal/fill records | 12 signals, 130 skips, 19 derives |
| `nestor/logs/settlements.jsonl` | our settled positions | **6** |
| `nestor/data/ws_divergence.jsonl` | WS-vs-REST quote pairs | 1,167 (4.3h, Jul 26 17:30-21:46Z) |
| `nestor/data/house_probe.jsonl` | house sleeve decisions | 3,972 (1.8h, 2 tickers) |
| `nestor/data/obs/2026-07-2{3,4,5,6}.jsonl` | our own REST quote tape | 83,424 quotes / 576 ticker-windows |
| `~/kalshi_data/KX*15M_mkts_full.json` | settled 15m corpus, 5 coins | 6,339-6,346 windows/coin |
| `~/kalshi_data/KX*15M_virgin.jsonl.gz` | our per-second price paths, 5 coins | 2,541-2,571 windows/coin |
| `~/kalshi_data/kbt_books_*.jsonl(.gz)` | 100ms true top-of-book, 5 coins | 342-363 windows/coin (39-45% frozen, filtered) |

Scripts (scratchpad, not repo):
`/private/tmp/claude-501/-Users-ryanwhitehead/449dc817-6064-457d-a116-2df58b67bcb2/scratchpad/{floor,paths,policy,asktruth,offset,inverted}.py`

**Signal population** = nestor's own rule replayed on the settled corpus: 4 contiguous
same-direction 15m windows → buy the reversal side of window 5. Priced by our virgin tape.
**n = 1,238 signals across 5 coins** (BTC 229, ETH 206, SOL 264, XRP 299, DOGE 240).
Span **2026-06-25 → 2026-07-22, 28 days** — the virgin tape's reach, not the 67-day
settlement corpus. ONE REGIME. Note-32 window-validity rule applies.

**Fill-model honesty.** Fills are modelled with nestor's own `paper_maker_fills` convention
(a bid at L fills iff the reversal side trades at ≤L). Cross-checked three ways at L=40:
virgin-trade proxy **34.1%** (n=1,238) · kbt TRUE asks **33.3%** (n=27 streak signals) ·
our own obs REST asks, cheap side **33.0% BTC / 35.9% ETH** (n=576 windows). On 84
windows present in both kbt and virgin, `min(ask) − min(trade)` over the first 45s is
**mean −0.05¢, median 0¢**. The proxy is unbiased at the resolution that matters.

---

## THE FLOOR TABLE (Ryan's question, answered per coin)

**Fill-conditional win rate for a resting bid at L, first 45s. Maker fee = $0 → breakeven
win% = L.**

| coin | L=36 | L=38 | L=40 | L=42 | L=44 | L=46 | L=48 | signals |
|---|---|---|---|---|---|---|---|---|
| BTC | 41.0 (n39) | 47.2 (n53) | **45.5 (n66)** | 49.5 (n99) | 51.7 (n120) | 49.7 (n147) | 51.5 (n171) | 229 |
| ETH | 50.0 (n42) | 48.2 (n56) | **52.1 (n71)** | 49.4 (n85) | 50.5 (n107) | 51.7 (n120) | 51.1 (n131) | 206 |
| SOL | 39.2 (n51) | 45.9 (n74) | **46.2 (n93)** | 49.6 (n113) | 48.9 (n133) | 50.7 (n152) | 52.7 (n169) | 264 |
| XRP | 45.9 (n74) | 43.2 (n95) | **40.2 (n112)** | 42.0 (n131) | 43.8 (n146) | 46.5 (n172) | 48.8 (n203) | 299 |
| DOGE | 42.9 (n56) | 45.8 (n72) | **47.5 (n80)** | 51.1 (n94) | 51.8 (n110) | 51.7 (n120) | 53.3 (n137) | 240 |
| **POOLED** | 43.9 (n262) | 45.7 (n350) | **45.7 (n422)** | 47.9 (n522) | 49.0 (n616) | 49.8 (n711) | 51.3 (n811) | 1,238 |
| **pooled EV¢/fill** | +7.89 | +7.71 | **+5.73** | +5.89 | +5.03 | +3.79 | +3.29 | — |

Pooled CIs at the three live-relevant rungs: **L=40 45.7% [41.0, 50.5]** · L=42 47.9%
[43.6, 52.2] · L=44 49.0% [45.1, 53.0]. Every rung clears its own breakeven; the cushion
is **5-7pp, not the 12pp the shipped derivation assumed.**

Chronological half-split (28-day span cut in two) — both halves positive at all three rungs:

| L | H1 | H2 | ALL |
|---|---|---|---|
| 40 | 43.5% (n184, +3.48¢) | 47.5% (n238, +7.48¢) | 45.7% (n422, +5.73¢) |
| 42 | 47.1% (n242, +5.11¢) | 48.6% (n280, +6.57¢) | 47.9% (n522, +5.89¢) |
| 44 | 48.6% (n294, +4.64¢) | 49.4% (n322, +5.38¢) | 49.0% (n616, +5.03¢) |

**Literal entry-price buckets** (taker at the first print after T0, one obs/window, taker
fee) — pooled ALL-bucket rows: BTC 55.0% @51.2¢ (+2.06¢/tr, n=229) · ETH 54.4% @53.3¢
(−0.71¢, n=206) · SOL 54.9% @51.7¢ (+1.45¢, n=264) · XRP 51.5% @50.7¢ (−0.97¢, n=299) ·
DOGE 57.1% @50.7¢ (+4.66¢, n=240). Per-bucket cells are n=3-94 and mostly non-monotone —
**do not read a single bucket cell as a floor**; the L-sweep above is the load-bearing table.

---

## 8 FINDINGS

### 1. Adverse selection on the streak fade is now MEASURED: −8.7pp. **TRADE-shaped (it is the price of the edge, and the edge survives it).**
Same 1,238 signals, split by what the shipped policy does with them:

| path | n | share | avg fill | win% | 95% CI | breakeven | EV¢/fill |
|---|---|---|---|---|---|---|---|
| maker_rest @40 | 422 | 34.1% | 40.0 | **45.7** | [41.0, 50.5] | 40.0 (maker $0) | **+5.73** |
| taker_backstop @≤46 | 103 | 8.3% | 43.3 | **43.7** | [34.5, 53.3] | 45.1 | **−1.37** |
| no fill | 713 | 57.6% | — | **61.2** | [57.5, 64.7] | — | — |
| ALL SIGNALS | 1,238 | 100% | — | 54.4 | [51.7, 57.2] | — | — |

Mechanism, stated plainly: **the fade side gets cheap precisely in the windows where the
streak is continuing.** We fill 34% of signals and they are the losing 46%; the 61% winners
are the ones we never get into. This is the graveyard's "maker = informed-flow magnet"
appearing inside our own strategy — but here it is survivable, because at a $0 maker fee a
40¢ bid only needs 40%.

### 2. The taker backstop is EV-negative at every ceiling we would ship. **CONDITIONAL KILL (gate: n=103).**
Backstop ceiling sweep, pooled, on signals the 40¢ bid missed:

| ceiling | fills | win% | 95% CI | avg px | breakeven | EV¢/fill |
|---|---|---|---|---|---|---|
| 42 | 26 | 30.8 | [16.5, 50.0] | 39.7 | 41.4 | −10.60 |
| 43 | 43 | 37.2 | [24.4, 52.1] | 41.0 | 42.7 | −5.48 |
| 44 | 62 | 41.9 | [30.5, 54.3] | 41.9 | 43.6 | −1.68 |
| 45 | 82 | 45.1 | [34.8, 55.9] | 42.7 | 44.4 | +0.74 |
| **46 (SHIPPED)** | 103 | 43.7 | [34.5, 53.3] | 43.3 | 45.1 | **−1.37** |
| 47 | 124 | 42.7 | [34.4, 51.5] | 44.0 | 45.7 | −2.95 |
| 48 | 146 | 44.5 | [36.7, 52.6] | 44.6 | 46.3 | −1.78 |

Whole-policy EV: maker40+backstop46 **+1.840¢/signal** vs maker40 alone **+1.955¢/signal**.
The backstop is a −0.115¢/signal drag. Fable's note-39 ruling flagged exactly this risk
("the 45-48 population is NEW; the conditional win rate in swept windows may be below the
assumed 52%"). Measured: **43.7%**, below its own 45.1% breakeven. The CI still contains
breakeven at n=103, so this is CONDITIONAL, not DEAD: **gate = re-check at n≥250 backstop
fills; until then ship the backstop OFF or at ceiling 44**, which costs almost nothing
(−1.68¢/fill on 62 fills) and removes the fee-paying leg entirely.

### 3. The maker bid is derived one to two cents too low. **CONDITIONAL (CIs overlap).**
Policy EV per signal, pooled n=1,238, maker fee $0:

| policy | fills | fill% | win%\|fill | EV¢/fill | **EV¢/signal** |
|---|---|---|---|---|---|
| maker40 + backstop46 (SHIPPED) | 525 | 42.4% | 45.3 | +4.34 | +1.840 |
| maker40 only | 422 | 34.1% | 45.7 | +5.73 | +1.955 |
| maker42 only | 522 | 42.2% | 47.9 | +5.89 | **+2.485** |
| **maker44 only** | 616 | 49.8% | 49.0 | +5.03 | **+2.501** |
| maker46 only | 711 | 57.4% | 49.8 | +3.79 | +2.176 |
| maker38 only | 350 | 28.3% | 45.7 | +7.71 | +2.181 |
| taker at first print, all signals | 1,238 | 100% | 54.4 | +1.29 | +1.294 |

The shipped 40 was chosen on `P(fill) × EV` using an **assumed** 52% win rate. With the
**measured** fill-conditional rate the product flattens and tilts up: the win rate rises
with L faster than the price cost, because a higher bid buys the less-adversely-selected
windows. +0.55¢/signal from 40→44 with overlapping CIs — **not a ship order; a re-derivation
order.** The DIRECTION contradicts the ledger and that alone is worth knowing.

### 4. Our actual live participation record: **1 fill in 6 live attempts.** DATA, not verdict.
| | n |
|---|---|
| streak signals ever recorded | 12 |
| paper/simulated fills | 6/6 (100%) |
| **live attempts** | **6** |
| **live fills** | **1 (16.7%)** — BTC-26JUL251000-00, NO @38, fee 16.5¢, **won +$6.03** |
| live misses (`missed_fill`) | 5 — limits 42, 31, 44, 44, 44 |
| settlements logged | 6 → 3W/3L, **net +$6.50** |
| skips | prev_not_settled 52, not_entry_window 43, price_above_gate 35 |

**`maker_rest` / `taker_backstop` records: n = 0.** The new `EntryPath` execution is in
`crates/streak/src/exec.rs` but has never produced a record — `grep -c` over `logs/run.log`
returns 0. **Ryan's maker-vs-backstop fill-quality question cannot be answered from the
tape; finding 1/2 above is the pre-registered simulated answer against which the first
real records should be scored.**

Retry ladder reality check: the last 3 live signals all ran `attempts: 4` at limit 44 and
**filled 0/3**. `verify-streak-retry` predicted 88.5% with limit-at-gate + 3 retries;
P(0/3 | 88.5%) = 0.0015. n=3 — flagged, not concluded, but it points straight at finding 5.

Signal timing: offset from T0 ranged 4-58s (live subset 4-33s, median 26s); signal→submit
0.35-1.11s on single-shot, **7.35-7.86s on the 4-attempt ladder**; submit→ack 0.09-0.30s.

### 5. **A quarter of our gate passes are phantom: REST lags WS by a full price level.** TRADE-shaped fix.
`ws_divergence.jsonl`, n=1,167 paired samples, 4.3h on 2026-07-26, BTC+ETH.

- WS/REST agree exactly on only **13.7% (yes) / 15.8% (no)** of samples; mean |Δ| **3.73¢ / 3.82¢**.
- The divergence is **antisymmetric by construction** — hour 18: yes **+2.20¢**, no **−2.20¢**;
  hour 21: +2.65 / −2.77. The SPREAD matches; the **MID** does not. REST is not wider, it is
  **stale in level**.
- WS freshness: `ws_age_ms` p50 **11ms**, p90 94ms, p99 13.9s (a few stall episodes).
- **The money number: when REST says the ask is ≤44 (gate pass), the true WS ask is >44 in
  123 of 496 quote-moments = 24.8%. At ≤46: 171/683 = 25.0%.** Gate-decision flips overall:
  11.9% at ≤44, 15.5% at ≤46 (n=2,200 side-moments).

This is the mechanism behind 1-live-fill-in-6 and 0-of-3 on the retry ladder: we are firing
IOCs at a price that a quarter of the time does not exist. **Door: the gate must read the WS
book, not the REST poll.** Cheapest decisive test: log `ws_ask` alongside `rest_ask` at every
signal for one week and compare fill rate on WS-gated vs REST-gated signals — zero new
capture needed, the divergence logger already exists. Caveat: one 4.3h afternoon.

### 6. Window-discovery latency: p50 T0+25.5s, and only the >30s tail actually costs us. **CONDITIONAL, narrower than expected.**
Our own obs tape, 576 ticker-windows over 4 days: first quote lands at **p10 10.7s, p50
25.9s, p90 42.6s** after T0 (max 797s). `streak_pass` reason is `no_current_market` **349/349**
times, with `into_window` p50 13s — i.e. the market is not in our list yet, which is
precisely the charter's pre-T0 `initialized` fact from lane 2.

Cheap-ask availability by arrival bucket (n=552 windows with ≥5 quotes):

| first quote at | windows | P(cheap ask ≤40 seen) | 95% CI | P(≤44) | mean min ask |
|---|---|---|---|---|---|
| ≤10s | 45 | 35.6% | [23.2, 50.2] | 60.0% | 40.8 |
| 11-20s | 161 | 34.8% | [27.9, 42.4] | 62.7% | 41.5 |
| 21-30s | 170 | 40.6% | [33.5, 48.1] | 62.9% | 41.1 |
| **>30s** | 176 | **27.8%** | [21.7, 34.9] | **50.0%** | **43.1** |

**Honest correction to my own first read:** 58.5% of windows have their minimum ask at our
very first observation, which LOOKS like "the dip is over before we arrive" — but it is a
censoring artifact, and the table above falsifies the implication. Availability is **flat
for every arrival inside 30s** (CIs fully overlap) and only degrades in the >30s tail, which
is **32% of our windows**. So: shaving 26s→5s buys nothing measurable; **eliminating the
>30s tail buys ~7-13pp of fill availability on a third of windows.** That is a reliability
fix (ticker construction / list-index bypass), not a latency race — consistent with the
standing "we are not and cannot be the fast money" doctrine.

### 7. The 61.2% no-fill windows are NOT free money — the book prices them exactly. **DEAD-with-numbers.**
The obvious inversion of finding 1: buy the fade only in windows where it never dips to F
in the first 45s (the "firm" windows that win 61%), entering at the last print in [45,60]s.

| floor F | qualify | win% | 95% CI | avg entry | breakeven | EV¢/fill | EV¢/signal |
|---|---|---|---|---|---|---|---|
| 40 | 809 (65.3%) | 59.0 | [55.5, 62.3] | 57.4 | 59.0 | −0.08 | −0.055 |
| 44 | 615 (49.7%) | 59.8 | [55.9, 63.6] | 59.2 | 60.9 | −1.02 | −0.508 |
| 46 | 520 (42.0%) | 60.8 | [56.5, 64.9] | 60.5 | 62.1 | −1.31 | −0.551 |
| 50 | 327 (26.4%) | 63.6 | [58.3, 68.6] | 62.7 | 64.3 | −0.67 | −0.178 |

Negative at every floor; both chronological halves negative at F=46 (H1 −1.03¢ n=260,
H2 −1.59¢ n=260); only BTC is positive per-coin (+2.58¢, n=82, CI [53.8, 74.1] vs
breakeven 62.1 — inside noise). **This is the calibration map confirming itself inside our
own signal population**: the market's own price already contains everything the "firmness"
tell contains. Do not re-litigate.

### 8. Overnight maker fills are the one clock cell that loses. **CONDITIONAL (named gate).**
Pooled, by UTC hour block, maker-40 policy:

| UTC block | signals | fade win% | maker40 fill% | **maker40 win%** | EV¢/signal |
|---|---|---|---|---|---|
| 00-05 | 300 | 54.7 | 32.7% | **38.8** | **−0.400** |
| 06-11 | 302 | 55.6 | 34.4% | 50.0 | +3.444 |
| 12-16 | 294 | 51.4 | 31.3% | 44.6 | +1.429 |
| 17-21 | 211 | 58.3 | 38.4% | 46.9 | +2.654 |
| 22-23 | 131 | 51.9 | 35.9% | 51.1 | +3.969 |

00-05 UTC = 20:00-01:00 EDT: 98 fills at 38.8%, below the 40.0% maker breakeven. Every other
block is +1.4 to +4.0¢/signal. Independent corroboration of R134's "streak is in practice a
daytime strategy" and of `verify-streak-clock`'s "overnight ≥ daytime in signal count, equal
EV" — refined here to **overnight signals are equally frequent but their FILLS are the bad
ones.** n=98 fills in the losing cell; one 28-day regime. Gate to test before shipping: does
00-05 stay negative on the next 100 fills?

**Also volunteered, no verdict claimed:**
- Streak length is flat (pooled): len4 54.3% (n=674), len5 53.4% (n=305), len6 59.0% (n=144),
  len7+ 52.2% (n=115). Confirms `verify-streak-conditioning` cell 3 on a second construction.
- Direction asymmetry is absent pooled (up-streak fade 54.7% n=653, down 54.2% n=585). The
  only ugly cell is **XRP down-streak maker fills: 36.5% (n=52)** — XRP is also the worst coin
  at every rung (L=40: 40.2% at breakeven 40.0 = exactly zero). **If a coin gets cut, cut XRP.**
- **House sleeve participation record = zero.** 3,972 `house_pass` records over 1h48m,
  `quoting: false` on 100% of them; gates `spread_lt_2c` 1,986 (KXAPRPOTUS, p50 spread 1¢) and
  `no_two_sided_book` 1,986 (KXCPIYOY). Both probe books are structurally unquotable — one too
  tight to quote inside, one one-sided. Hard evidence for lane HOUSE-FEE's "hunt beyond the two
  probe books".

---

## TOP DOOR

**The entire remaining streak edge is a maker-fee-exemption edge — so the build order is
"make everything a maker fill", not "tune the fade".**

Every fee-paying expression of this signal in our own record is ≈0 or negative: taker at open
+1.29¢/signal (BTC/DOGE positive, ETH/XRP negative), taker backstop −1.37¢/fill, the inverted
firm-window taker −0.55¢/signal at every floor and in both halves. The one clearly positive
expression is the resting bid, and it is positive *only because the maker fee is $0*: at 40¢
it wins 45.7% [41.0, 50.5] against a 40.0% breakeven — a 5.7pp cushion that a 1.68¢ taker fee
would cut to 4.0pp and that the fee quadratic would erase entirely near 50¢.

Two concrete moves, cheapest first:
1. **Move the gate to the WS book** (finding 5). 25% of REST gate-passes are phantom; this is
   free — the logger exists, the fix is which field the gate reads. It converts missed fills
   into either real fills or honest no-trades, and it is the single highest-leverage change in
   this ledger.
2. **Drop the taker backstop, re-derive the maker rung at 42-44** (findings 2+3). Together
   ≈ +0.66¢/signal on the measured tape and they remove the only fee-paying leg.

This collides directly with lane HOUSE-FEE: if our own record says the streak edge IS the
maker exemption, then the exemption is not a discount on an existing strategy — it is the
strategy, and the question becomes how many other books have a 5pp cushion that only exists
on the free side.

## WHAT THIS LANE COULD NOT ANSWER
- **maker_rest vs taker_backstop from real fills** — n=0. Simulated only.
- Anything cross-era: the priced signal population is **28 days, one regime** (2026-06-25 →
  2026-07-22). The 2yr settlement corpus exists but our price paths do not reach it.
- The WS-vs-REST result is **one 4.3-hour afternoon**.
- SOL/XRP/DOGE are **not traded by nestor** — their rows are counterfactual, not participation.
