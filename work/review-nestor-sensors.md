# REVIEW: nestor deep review — lane SENSORS (class D: unasked data) — 2026-07-26

> Charter: `work/steer-nestor-deep-review.md` §Lane charters #4. Question: can every plausible
> anomaly be RECONSTRUCTED from what is logged today, and does a written EXPECTATION exist to
> reconcile the decision stream against? Repo read-only; all numbers below are measured off
> `nestor/data/*.jsonl` + `logs/run.log` on disk today.

**Headline:** the single most expensive sensor gap is not a missing field — it is that
**the streak entry-window tape does not begin until a median T0+25s** (n=518 windows, 3 days).
The policy shipped this afternoon rests a 40¢ bid on a dip that the ledger says bottoms at
**T0+4.8s**. We have never observed that interval, and nothing in the logs says so.

---

## 1. FINDINGS (most severe first)

### F1 — CRITICAL(money) / HIGH(sensor). The first ~25s of every entry window is unobserved, and the blind spot is itself invisible
`crates/streak/src/strategy.rs:576-579` — `current_market()` returns `None` → `return Ok(())`
with **no log line, no jsonl record, nothing**. `data/obs/*.jsonl` is only written *after* that
point (`:589`), and `ws_divergence.jsonl` after that (`:454`).

Measured, `data/obs/2026-07-{24,25,26}`, **n=518 windows** (first observation of each new
market, seconds after its T0):

| P(first obs after…) | T0+5s | T0+10s | T0+20s | T0+30s | T0+40s | T0+45s | T0+60s |
|---|---|---|---|---|---|---|---|
| share of windows | 96.9% | 87.6% | 61.4% | 30.3% | **13.3%** | 6.4% | **4.2%** |

median T0+25s, mean T0+35.5s, minimum ever seen T0+4s. Stable across all three days and both
series (BTC median 21-27s, ETH 25-26s).

Failure scenario (concrete): 4-streak completes at T0. `scan_series` runs at T0+1, T0+2, … but
`markets?status=open` does not yet return the new market, so every pass silently returns. At
T0+25 the market appears; `place_maker` posts the 40¢ GTD bid. Per
`work/verify-streak-execution.md` §2 the reversal-side dip bottoms at a **median 4.8s** after a
real 4-streak and then sweeps up 55% of the time — so the bid arrives *after* the population it
was fitted to. The 24% maker-fill expectation is unattainable, and the shortfall will be read as
"the maker leg doesn't work" rather than "we posted 25s late."
Two further consequences already visible in the data:
- **13.3% of windows** first observe after T0+40 → `exec::maker_eligible()` is false → the
  episode is forced to `taker_late`, **no maker leg at all**.
- **4.2% of windows** first observe after T0+60 → the entry window is missed outright.
  Corroborated in `logs/run.log`: 8× `skip — not_entry_window(ttc=839s)` plus 8× REPEAT-SKIP
  ALARM for `missed_entry_window` over three days.

Sensor verdict: **the cause is not reconstructible.** Nothing distinguishes "market not listed
yet" / "listed but unpriced" / "open-markets fetch timed out" / "process wedged" — all four
produce identical silence.

*Cheapest decisive check (on disk, no probe):* already run — the table above. To separate cause,
one 10-minute demo/prod read loop hitting `GET /markets?series_ticker=KXBTC15M&status=open`
every 500ms across a 15-min boundary, recording the wall-clock at which the new ticker first
appears and whether `open_time == T0`. That distinguishes listing latency from our poll logic in
one boundary.

### F2 — HIGH. House metric 1 (the PROMOTE gate) is wrong by ~10× — `quote_secs` is cumulative age, summed
`crates/house/src/strategy.rs:337-341` writes `"quote_secs": age` where `age = now - q.quoted_ts`
— the **cumulative** age of the quote — on *every* hold pass. `crates/house/src/report.rs:47`
then **sums** those into `quote_secs`.

Failure scenario: quote posted at t=0, mid steady, 3s loop (`nestor_bin/src/main.rs:449`),
requote staleness 60s. Passes at t=3,6,…,57 write 3,6,…,57 → Σ = 570s of "quote time" for 60
real seconds of quoting = **9.5× overstatement**. Fill rate = fills / quote-hours is therefore
understated ~10×, and the promote gate is "fill rate ≥ a handful/hr" (`work/probe-house.md`
metric 1). A probe that genuinely fills 5/hr reports 0.5/hr → **spurious KILL of a working
sleeve.** Worse, the bias is not constant: when the mid moves ≥1¢ the quote is re-posted and
`quoted_ts` resets (`should_requote`, `crates/house/src/signal.rs:94-101`), so the distortion is
an uncontrolled function of mid volatility — in choppy markets it *understates* quote time.

*Cheapest check:* replay — sum `quote_secs` from a day of `house_probe.jsonl` and compare against
(count of `house_quote_live` records × 3s). If the sum exceeds 3× the record count, it is
double-counting. Unit-testable offline; no live data needed.

### F3 — HIGH. House metric 2 (realized half-spread) is conditioned on favorable markouts only — the gate cannot fail
`crates/house/src/strategy.rs:629`: `let half_spread = if *mk > 0.0 { Some(*mk) } else { None };`
and `report.rs:57` averages only the `Some`. So metric 2 = **E[markout | markout > 0]**, which is
structurally > 0 and typically ≫ 0.6¢. The promote condition "realized half-spread ≥ +0.6¢ net of
fees" is therefore satisfiable by a sleeve that loses money on every other fill.

Failure scenario: 10 fills, markouts +1,+1,+1,−4,−4,−4,−4,−4,−4,−4. True mean = −2.5¢ (kill).
Reported metric 2 = **+1.0¢ → PROMOTE**. Real capital gets allocated to a bleeding maker sleeve.

*Cheapest check:* the same replay — recompute mean over ALL `markout_cents` and compare to the
reported `avg_half_spread_cents`. Also: fees are never subtracted from metric 2 even though the
gate says "net of fees" (`fee_cents` lives on `house_fill`, not on `house_markout`, and the two
records share no id).

### F4 — HIGH. House logs nothing on the two gates that actually fire — the probe has no denominator
`crates/house/src/strategy.rs:269` (`no_two_sided_book`) and `:290` (`spread_lt_2c`) call
`pull_quotes` and return **without writing any record**. Only `catalyst_window` gets a
`house_gate` record (`:281-287`). `data/house_probe.jsonl` **does not exist on disk** — zero
bytes of house telemetry after a full weekend live with `HOUSE_PROBE=1`.

Failure scenario: note 39 states "Zero quotes yet (weekend spreads 1¢ = gate working)". That is
an inference from an **absent file**, not from data. The identical file-absence is produced by:
`pick_market` never finding an in-band rung; the markets fetch timing out every pass
(`:242`, logs to run.log only); `HOUSE_PROBE` not actually reaching the process; a panic in the
3s task. On Monday, "no quotes" will again be unfalsifiable, and the 2-3 day probe budget burns
with no evidence either way.

*Cheapest check:* on disk right now — `ls data/house_probe.jsonl` (absent) vs
`grep -c "house" logs/run.log`. If run.log also has no per-pass house line, the sleeve is
unobserved, not idle.

### F5 — HIGH. A streak maker episode's participation record is written only at episode END — a restart loses the whole signal
`base_record()` (`crates/streak/src/strategy.rs:925-945`) holds `ts_signal`, `streak_dir`,
`ask_at_signal`, `entry_path`, `derived_fourth/avg/margin_bp` and the **T0 order-book snapshot**
(fetched at `:969`). It is written **only** on a terminal path (fill `:1542`, reject `:1134`,
flip `:1446`, off-book `:1369`, taker outcomes `:1681-1691`). The record written at placement is
`streak_maker_rest` (`:1093-1109`) and it carries **none** of them — only
`{series, ticker, side, price, count, order_id, expiration_ts, backstop_at, ceiling, paper, order}`.

Failure scenario: bid rests at T0+25. Process restarts at T0+40 (VPS migration is tonight;
restarts are routine and note 39 documents the kill being flaky). The startup sweep
(`:492-527`) cancels the orphan. The tape now shows a `streak_maker_rest` with no signal context,
no book, no `ask_at_signal`, and **no terminal record at all** — the episode cannot be classified
as maker/backstop/no-trade, and it silently disappears from every entry_path denominator.
Same hole on the `mark_cancel_failed` give-up path (`:1384-1403`): alert + run.log line, **no
week1 record**.

*Cheapest check:* on disk — `streak_week1.jsonl` today has 12 `streak_signal`, 130
`streak_skip`, 19 `streak_derive`, **0 `streak_maker_rest`** (no maker episode has occurred yet).
Confirm by inspection that no code path writes `base_record` before the terminal branch.

### F6 — CRITICAL (cross-lane: MONEYPATH/kill-switch, surfaced here because it is a blind sensor)
The house −5¢ gap-through stop can only fire when the fill's age is **exactly 60s**.
`crates/house/src/strategy.rs:602-604` skips any pending fill with `age_secs < 60`; the survivor
is then passed to `gap_through_stop(mk, age_secs)` = `age_secs <= 60 && markout <= -5.0`
(`crates/house/src/signal.rs:202-204`). On the 3s loop the first eligible evaluation lands at age
60, 61 or 62 with ~equal probability → **the protocol's own hard stop misses ~2/3 of the events
it exists to catch.** The metric-3 classification (`is_gap_through`) is unaffected, so the log
will *record* the gap-through while the stop stays silent.

*Cheapest check:* pure unit test — `gap_through_stop(-6.0, 61)` returns `false` today.

### F7 — MED-HIGH. House markouts silently fabricate 0¢ when the mid is unknown
`crates/house/src/strategy.rs:607`: `let mid = mids.get(&p.ticker).copied().unwrap_or(p.entry_cents);`
→ `markout_cents` = exactly 0 for a YES leg. `last_mid` is only refreshed on a pass that reaches
that ticker with a two-sided book (`:272-276`); a pull for `spread_lt_2c` or a market rotation
leaves it stale or absent. The `house_markout` record carries **no `mid_used` and no
`mid_age_secs`**, so a fabricated 0 is indistinguishable from a genuine flat markout. Metric 3
(the stated *kill number*) is diluted toward "no gap-through" by exactly the cases where the book
went one-sided — i.e. the gap-through cases.

*Cheapest check:* add `mid_used`/`mid_age_secs` to the record; until then, on any real tape,
count `house_markout` rows with `markout_cents == 0.0` exactly — a spike at exactly 0 is the
fingerprint.

### F8 — MED. Streak's backstop does not record the ask it fired into (live), nor a book at cancel/backstop time
`taker_leg` sets `rec["observed_ask"]` **only** in the paper no-cross branch
(`crates/streak/src/strategy.rs:1602`). In live, `exec::taker_limit` returns the ceiling
unconditionally, so the branch never runs and `leg.last_ask` (passed in at `:1340`) is dropped on
the floor. `meta.book` in the record is the **T0** book (fetched at `:969`), reused verbatim for
a `taker_backstop` record generated at T0+45 — it is 45s stale and unlabelled as such.
`retry_books` (`:1648`) snapshot attempts **2,3,4 only**; attempt 1 has no book.

Failure scenario: backstop returns `filled_count: 0`. Was the ask 60¢ (correct no-trade, the
policy working) or 45¢ that we missed on a 300ms RTT (execution defect)? The record cannot say.
Join to `obs`/`ws_divergence` by ts is the workaround — but F1 means those tapes are ~1Hz at
best and start late.

*Cheapest check:* free — add `observed_ask_at_backstop` + a book snapshot before attempt 1.

### F9 — MED. `obs` carries asks only: no bids, no sizes → the flagged maker over-estimate is unmeasurable
`crates/streak/src/strategy.rs:589-597` writes `{ts_ms, ticker, yes_ask, no_ask}`.
`work/verify-streak-execution.md` §5 flags precisely this risk: "*the fill model ignores queue
position … fills at exactly L with size ahead may not occur → maker fill-rate (24%) is a mild
over-estimate*". With no size on the ask and no bid ladder, an ask that touches 40 with size 1
is indistinguishable in the tape from a sweep that clears 500 — so the one caveat the researcher
flagged as material cannot be tested against our own data. (The full book *is* captured, but only
at 3 instants: signal, and retries 2-4.)

### F10 — MED. ws seq gaps, resyncs, reconnects and HTTP 429s exist only as free text in run.log
`crates/engine/src/ws.rs` contains **zero** `logging::record` calls; gaps/resyncs go through
`logging::info` (`:356, :365, :395`). Rate-limit hits likewise: `logs/run.log` today holds
2× `streak task error: markets HTTP 429 code=too_many_requests`. There is no counter, no jsonl
row, and no per-pass request accounting anywhere — so the REALITY lane's question ("worst-case
req/s of three strategies + ws + 1Hz sampling vs the known limit") has **no sensor at all**, and
a 429 storm during an entry window would appear in the tape only as F1-style silence.

### F11 — MED. `ws_divergence.jsonl` cannot settle the question it was built for
Today's file: 510 rows, 1h46m, 16 tickers. **463/510 rows have a non-null ws ask, 444 synced,
482 with an age** — so ws health *is* measurable. But: no ws **bid** (the reversal quote is
`100 − streak-side bid`, so half the input is absent), no **seq**, no gap/resync marker, and the
gate `(MIN_TTC_SECS..=WINDOW_SECS)` (`:453`) inherits F1 — first row per window lands at
T0+5..42s (median T0+29), never earlier. Per-window row counts run **19-54, not 60**.
And **no written numeric criterion exists anywhere in the vault for flipping `STREAK_WS=1`** —
note 39 says only "flips ONLY after data/ws_divergence.jsonl proves the win". An unfalsifiable
gate is a wasted sensor. (Proposed threshold in §3 below.)

### F12 — MED. Nothing computes the 45-46 conditional win rate — the exact evidence the 46 ceiling is waiting on
`logs/settlements.jsonl` rows are `{event, strategy, ticker, won, pnl, result, ts}`
(`crates/engine/src/reconcile.rs:153-163`) — **no `entry_path`, no `fill_price`**. The whole
reason `exec::TAKER_CEILING_CENTS` ships at 46 instead of 48 is "walk to 48 only when live fills
at 45-46 prove the conditional win rate" (note 39). That requires a join
`streak_week1.jsonl.ticker → settlements.jsonl.ticker`, grouped by `fill_price ∈ {45,46}` and
`entry_path`. There is a `house-report` subcommand; **there is no `streak-report`**. Nobody will
run this join by hand every week — which is exactly the class-D failure mode (paper's settled
filter sat in the tape for hours).

### F13 — MED. Volbook skip records carry no numbers — Monday's bucket-mix prediction is not directly checkable
`crates/volbook/src/strategy.rs:247-255` writes `{event, series, ticker, reject_reason, ts}`.
The implied YES is embedded **inside the reason string** (`out_of_band(impl=23.4)`), and there is
no `ttc`, no `close_unix`, no `no_ask`, no `ceiling`. `work/verify-volbook-execution.md` §5
predicts a **bucket mix** of qualifying rungs (4.90 / 2.48 / 1.45 / 0.93 / 1.10 / 0.31 across the
six implied buckets) and a skip histogram — the counts are reconstructible (one record per
ticker×reason = one per rung), the **buckets are only reachable by regex on a formatted string**,
and there is no way to confirm a skip was evaluated inside the 17:30-18:30Z window rather than
outside it. Filled rungs are fine (`volbook_signal` carries `implied_pct`, `touch`, `gap_pp`,
`ceiling_cents`, `no_ask_cents`, `attempts`, `book`).

---

## 2. MISSING-INSTRUMENTATION LIST (what to add, cheapest first)

| # | Add | Where | Buys you | Prio |
|---|---|---|---|---|
| I1 | **Per-pass heartbeat record** `{ts_ms, series, into_window, cur_ticker\|null, reason: listed\|no_market\|fetch_timeout, ttc}` — one line per `scan_series` pass, including the silent-return passes | `streak/strategy.rs:576` (and the `:565` timeout branch) | makes F1 self-describing; turns "silence" into a measured listing latency | **1** |
| I2 | **`house_pass` record every pass**: `{book, ticker\|null, best_bid, best_ask, spread, mid, gate: ok\|spread_lt_2c\|no_two_sided\|catalyst\|no_pick, quoting: bool}` | `house/strategy.rs:247, 268, 289` | the probe's denominator: quotable-spread fraction, quote uptime, and positive proof the sleeve is alive | **1** |
| I3 | **Fix + re-define quote time**: emit `quote_delta_secs` (time since the *previous* record) instead of cumulative age, or emit `quote_start_ts`/`quote_end_ts` pairs | `house/strategy.rs:337`, `house/report.rs:47` | metric 1 becomes true fills/quote-hour | **1** |
| I4 | **Write `base_record` at placement**, not only at episode end (`event: streak_signal`, `outcome: pending`), and re-emit on terminal | `streak/strategy.rs:1093` | survives restarts; F5 | **1** |
| I5 | **Metric-2 over all markouts** + carry `fee_cents` and the `house_fill` id on `house_markout` | `house/strategy.rs:625-637`, `report.rs:57` | metric 2 becomes falsifiable and net-of-fees | **1** |
| I6 | `mid_used`, `mid_age_secs`, `mid_source` on `house_markout` | `house/strategy.rs:630` | F7 — separates real 0¢ from fabricated 0¢ | 2 |
| I7 | `observed_ask_at_backstop`, `book_at_backstop`, `attempt1_book`, `book_ts_ms` on the backstop record; label `meta.book` as `book_at_signal` | `streak/strategy.rs:1332-1347, 1590-1654` | F8 — "correct no-trade" vs "missed a 45" | 2 |
| I8 | Bid + top-3 sizes both sides in `obs` (`yes_bid, no_bid, yes_ask_sz, no_ask_sz, yes_bid_sz, no_bid_sz`) | `streak/strategy.rs:589` | F9 — queue-position test for the 24% | 2 |
| I9 | `data/ws_events.jsonl`: `{ts_ms, event: connect\|close\|seq_gap\|resync, sid, expected_seq, got_seq}` | `engine/ws.rs:356, 365, 395, 444` | seq-gap-during-entry-window reconstruction | 2 |
| I10 | `ws_bid`/`rest_bid` + `seq` + `ws_gap_since_ms` on `ws_divergence` rows | `streak/strategy.rs:454-466` | F11 — the reversal quote's actual inputs | 2 |
| I11 | `entry_path`, `fill_price`, `filled_count`, `actual_fee_cents` copied onto the settlement record | `engine/reconcile.rs:153` | F12 — one-file conditional-win-rate by fill price | 2 |
| I12 | `data/net.jsonl` counters: per-minute request count by endpoint + every non-2xx status (429s especially) | `engine/net.rs` / `kalshi.rs` call sites | F10 — the only sensor for the rate-limit budget | 2 |
| I13 | Numeric fields on `volbook_skip`: `implied_pct`, `no_ask_cents`, `ceiling_cents`, `ttc`, `close_unix` | `volbook/strategy.rs:247` | F13 — direct bucket-mix conformance | 3 |
| I14 | `streak-report` subcommand mirroring `house-report`: joins week1 × settlements, prints the §3 table | new, alongside `house/report.rs` | makes the conformance table a command, not a chore | 3 |
| I15 | Ledger/inventory snapshot record on every house pass (`realized_cents`, `unrealized_cents`, `fees_cents`, open legs) | `house/strategy.rs:302-309` | reconstructs the −$20 stop's own mark; today only the *breach* is logged | 3 |

---

## 3. CONFORMANCE-CHECK TABLE — ready to run against tomorrow's tape

### 3a. STREAK — new execution policy. **These expectations did not exist before this document.**

Derived from `work/verify-streak-execution.md` §2 fill-probability curve
(prev-1 set, n=143, the conservative one; the 4-streak set n=20 is deeper/cheaper) with the
**shipped** dials `MAKER_PRICE_CENTS=40`, `TAKER_CEILING_CENTS=46`, `BACKSTOP_AT_SECS=45`
(`crates/streak/src/exec.rs`). Base curve: P(min ask ≤ L in 60s) = 24% @40, 40% @44, **45% @46**,
61% @48.

Population = one **signal** (a detected 4-streak inside the entry window).

| # | metric | expected | source of the expectation | where to read it |
|---|---|---|---|---|
| S1 | **maker fill rate** (filled from the 40¢ rest) | **24% of signals** (95% CI ≈ 17-31%, n=143); 25% on the 4-streak set | P(min ask ≤40 in 60s), ledger §2 | `streak_week1.jsonl`: `count(entry_path=="maker_rest" && filled) / count(signals)` |
| S2 | **backstop fill rate** | **15-21% of signals**, central **18%** (= 28% of unfilled makers). Upper 21% = P(min≤46)−P(min≤40) credits the whole 60s; lower 15% = the ledger's point-in-time taker rate at t≈45-55s (21% at ceiling 48) scaled by P(≤46)/P(≤48)=0.74 | ledger §2 curve + §3 "taker fill rate rises 15%→21% at t≈45-55s" | `entry_path=="taker_backstop" && filled` |
| S3 | **no-trade rate** | **55-61%**, central **58%** | 1 − S1 − S2 | `filled==false` over all signals |
| S4 | **entry-path mix among FILLED entries** | maker_rest **57%**, taker_backstop **43%**, taker_late **≈0%** *(see S5)* | S1/S2 ratio | group by `entry_path` where `filled` |
| S5 | **taker_late share of signals** | **13.3%** — NOT ≈0. New prediction from this review (F1): P(first observation > T0+40) over n=518 windows | `data/obs/*` 3-day measurement | `entry_path=="taker_late"` / all signals. **If this is ≈0, F1 has been fixed or the sample is thin; if it is ≥10%, F1 is confirmed live** |
| S6 | **missed entry window** | **4.2% of signal-eligible windows** (P(first obs > T0+60)); run.log shows 8 such skips over 3 days | same | `streak_skip` rows with `reject_reason` starting `not_entry_window` |
| S7 | **maker fill price** | exactly **40¢** on every maker fill (a resting limit fills at its limit) | order semantics | `fill_price` where `entry_path=="maker_rest"`. Anything ≠40 (except `crossed_at_post==true`) means the fill model is wrong |
| S8 | **crossed_at_post rate** | **<5%** of maker placements (P(reversal ask ≤40 at T0); open median 53¢, p25 49¢) | ledger §2 open-ask distribution | `crossed_at_post==true` / `streak_maker_rest` count |
| S9 | **backstop fill price** | **44-46¢**, central 45¢ (IOC price-improves below the 46 ceiling) | ledger §2 (population is 40 < min ask ≤ 46) | `fill_price` where `entry_path=="taker_backstop"` |
| S10 | **maker fee** | **0.0** (`actual_fee_cents==0`, `is_maker_fill==true`, `fee_basis=="exchange_fee_cost"`) | demo-verified 2026-07-26, note 39 | `streak_week1.jsonl` maker fill record. A non-zero maker fee is a **new API truth** and invalidates the 40¢ EV of +10.3¢ |
| S11 | **taker fee** | **1.74¢/contract at 46¢** (`fee = 7·p·(1−p)/100`), `is_maker_fill==false` | `exec.rs` header / risk::taker_fee | compare `fee_cents` (estimate) vs `actual_fee_cents` (exchange) on backstop fills — **a divergence >0.2¢ is a finding** |
| S12 | **EV captured / signal** | **+3.2 to +3.6¢/contract @52% win rate** (0.24×10.3 + 0.18×4.9). vs current-policy baseline **+2.50¢** | ledger §3, re-derived at the 46 ceiling (the ledger's 3.55¢ is at 48) | week1 × settlements join |
| S13 | **conditional win rate at fills of 45-46¢** | the **unproven** number; ≥52% required to walk the ceiling to 48. Needs ~8 fills minimum | note 39 Fable ruling; ledger §5 "single biggest risk" | `settlements.jsonl` ⋈ `streak_week1.jsonl` on ticker, filter `fill_price ∈ {45,46}` |
| S14 | **retry ladder efficacy** | one-shot 70.5% → **88.5%** with 4 attempts, conditional on a crossable ask | `work/verify-streak-retry.md` (2026-07-25) | `attempts` distribution on filled `taker_*` rows |
| S15 | **derive-fourth accuracy** | **6/6 agree** to date; any `agree:false` with `used:true` disables derivation | `data/derive_verify.jsonl` | same file; today 19 `streak_derive`, 0 disagreements |
| S16 | **tape completeness (the F1 tripwire)** | ws_divergence rows per window **≈60**; today **19-54, median ~31** | 1 Hz × 60s gate | `ws_divergence.jsonl` grouped by ticker. **<50 rows/window = the entry window is still partly blind** |
| S17 | **ws-vs-REST divergence** (the STREAK_WS flip criterion — **no written threshold existed; proposed here**) | flip `STREAK_WS=1` only when, over ≥1000 in-window paired rows: (a) `ws_synced` ≥ 99%, (b) median `ws_age_ms` ≤ 200 and p99 ≤ 1000, (c) `\|ws_ask − rest_ask\| ≥ 1¢` on ≥20% of rows *with ws leading rest* (ws value later confirmed by rest) on ≥90% of those, (d) **zero** rows where ws shows a crossable ask that rest never confirms within 3s (a phantom). Today: 463/510 non-null, 444 synced (87%) — **fails (a)**, sample far too small | proposed, this review | `ws_divergence.jsonl` |

### 3b. VOLBOOK — Monday 2026-07-27, entry window 17:30-18:30Z

Source for every row: `work/verify-volbook-execution.md` §5. Read from `data/volbook.jsonl`
(file does not exist yet — its creation is itself the first check).

| # | metric | expected | where to read it |
|---|---|---|---|
| V1 | qualifying rungs → orders sent | **mean 11.2, median 11, 80% CI 6-20**; 0-rung days 0/29 historically | `count(event=="volbook_signal")` |
| V2 | per-series qualifying | GOLD med 3 (2-8), SILVER med 5 (2-11), COPPER med 3 (1-6) | group by `series` |
| V3 | in-band rungs | metal total mean 11.6, med 11 | `volbook_signal` + `volbook_skip` with `no_edge` |
| V4 | skip histogram (of ~92 priced rungs) | out_of_band **58.0 (63%)**, unpriced **22.2 (24%)**, no_edge **0.41 (0.4%)**, qualify **11.2 (12%)** | `volbook_skip.reject_reason` prefix counts |
| V5 | no_edge concentration | **11 of 12** historical no_edge rungs sit in the 0.30-0.35 implied bucket | regex `impl=` out of the reason string (**F13** — add the numeric field) |
| V6 | bucket mix of qualifying rungs | 0.05-0.10 → **4.90**; 0.10-0.15 → **2.48**; 0.15-0.20 → **1.45**; 0.20-0.25 → **0.93**; 0.25-0.30 → **1.10**; 0.30-0.35 → **0.31** | `implied_pct` on `volbook_signal` |
| V7 | first-IOC fill rate | **≥90%** on attempt 1; **≥98%** within 1+1 | `attempts` + `filled` |
| V8 | entry price vs mid | **~+0.5¢ above mid**, not +1¢ (measured book half-spread). A fill worse than mid+1¢ = we crossed past the top of book | `fill_price` vs mid parsed from the `book` blob on the same record |
| V9 | EV per filled contract | **+7.9¢** (print corpus n=324, se 0.95); calib event-weighted +8.6¢ | settlements ⋈ volbook.jsonl |
| V10 | day total | **≈ +89¢ per contract of size** summed over ~11 rungs (median +108¢, p10 −56¢, p90 +153¢). **A red Monday is ~14% likely and is NOT a bug** | same |
| V11 | touches (settling YES against us) | **0.4 of ~11 rungs**. **≥2 touches = a 2.5σ event → stop and investigate** | `settlements.jsonl` where `strategy=="volbook"` and `won==false` |
| V12 | cluster cap | all metal rungs on one ET day pool into ONE cluster ≤15% of bankroll; expect `risk:ClusterCap` rejects once ~4 rungs × $4 = $16 vs 15%×$106 = $15.90 | `reject_reason` starting `risk:` on `volbook_signal` — **expect this to bind on a median 11-rung day** |
| V13 | no-trade Monday | if V1 = 0, check V4 first: 0/29 historical zero-rung days means a 0-rung Monday is itself a finding, not "gates working" | `volbook.jsonl` existence + skip counts |

### 3c. HOUSE — probe metrics (`work/probe-house.md`), read via `house-report`

**Every one of the four is currently mis-computed or un-computable — see F2, F3, F4, F7.**
The table below states what they SHOULD read, so the fix has a target.

| # | metric | expected / gate | where to read it | status today |
|---|---|---|---|---|
| H1 | fill rate | "≥ a handful/hr" — call it **≥3 fills/quote-hour** to promote | `house_fill.count` ÷ true quote-hours | **BROKEN** — quote-hours ~10× over (F2) |
| H2 | realized half-spread | **≥ +0.6¢ net of fees**, computed over **ALL** fills | mean `markout_cents` − mean `fee_cents` | **BROKEN** — conditioned on >0 (F3) |
| H3 | gap-through frequency | the kill number: gap-through loss < spread income over the window; flag if `gap_through_frac` > ~15% | `house_markout.gap_through` | **DILUTED** — fabricated 0¢ markouts (F7) |
| H4 | adverse-in-catalyst | should be **low** — the ±15min gate should catch these. Note `HOUSE_CATALYST_TS` is empty unless the operator sets it, so this metric is **structurally 0 today** | `house_markout.in_catalyst` | **VACUOUS** — no catalysts configured |
| H5 | quotable-spread fraction (new, required) | unknown — this is what the probe is FOR. Expect low on weekends (1¢ spreads), higher in US daytime | needs I2 | **MISSING ENTIRELY** (F4) |
| H6 | quote uptime | fraction of passes with two live legs | needs I2 | **MISSING** |
| H7 | −$20 stop headroom | `total_pnl_cents` per pass | needs I15 | **MISSING** — only the breach is logged |

---

## 4. WHAT I VERIFIED CLEAN (and how)

- **Streak fee forensics are complete.** `settle_maker_fill` (`strategy.rs:1510-1542`) writes
  both `fee_cents` (our estimate) and `actual_fee_cents` (exchange), plus `is_maker_fill`,
  `fee_basis`, and the **raw `/portfolio/fills` body** (`fills_raw`). This is the correct
  post-mortem shape and is the best-instrumented path in the codebase. (Minor asymmetry: the
  taker path keeps `actual_fee_cents` but not the raw body — `record_fill_quiet` `:1734-1765`.)
- **Derive-fourth is fully reconcilable.** `data/derive_verify.jsonl` carries
  `predicted / official / agree / used / derived_avg / derived_margin_bp / close_unix / ticker`.
  Read on disk: 19 `streak_derive`, all `agree:true`, all `used:false`. The auto-disable path
  (`:705-734`) writes a marker file with provenance. Nothing missing.
- **Cancel-response truth is captured.** The backstop record carries `maker_cancel` (the full
  cancel response incl. `reduced_by`), `maker_order_id`, `maker_rest_secs` (`:1342-1346`) — the
  404 / partial-fill semantics are reconstructible after the fact.
- **ws divergence tape is populated and healthy-ish**: 463/510 non-null ws asks, 444 synced,
  482 with `ws_age_ms`, median inter-sample **1.02s**, max **1.29s** — no gaps *within* a logged
  run. The failure is coverage at the window's start (F1/F11), not cadence.
- **obs inter-sample cadence matches the design** — median 1.12s in fast zones, p90 12.1s
  (the lazy middle), max 24.2s. `next_poll_delay` behaves as documented.
- **Volbook rung-level counts ARE reconstructible.** The per-(ticker,reason) dedup gives exactly
  one record per rung per disposition, which is the denominator the Monday prediction needs. Only
  the *numeric* bucket detail is trapped in strings (F13).
- **Skip records carry the decision book.** `log_skip` (`:894`) fetches the orderbook — so a
  `prev_not_settled` or `window_mismatch` skip can be re-priced after the fact.
- **Settlement records are strategy-tagged**, so per-strategy P&L joins by ticker (they just lack
  entry_path/fill_price — F12).

---

## 5. PROPOSED PROBES (design only — not run, per charter)

**P1 — Market-listing latency probe (kills or confirms F1; highest value per minute).**
Demo or prod *read-only*. From T0−30s to T0+90s across ≥8 consecutive 15-min boundaries on
KXBTC15M and KXETH15M, loop at 250ms:
`GET /markets?series_ticker=…&status=open` and `GET /markets?series_ticker=…` (no status filter).
Record per response: wall clock, whether the T0-opening ticker is present, its `status`,
`open_time`, `yes_ask`, `no_ask`. Deliverable: distribution of (first-appearance − T0) for
status-filtered vs unfiltered, and (first-priced − first-appeared). Decides three things at once:
(a) is the 25s median a Kalshi listing lag or our filter; (b) would dropping `status=open` and
selecting by `close_time` recover the first 25s; (c) is the book even priced before T0+25 (if
not, the 40¢ rest cannot be beaten by anything and the ledger's dip is unreachable — which would
be a **policy-invalidating** finding worth knowing before Monday).
Cost: ~2h wall clock, zero orders, ~3000 unauthenticated GETs. **Run this before Monday.**

**P2 — Maker-leg live-fire on demo (the state machine has never executed).**
Note 39: "UNPROVEN: the full maker state machine has no live-fire pass yet (no 4-streak on
demo)." Force one: on demo, place a resting BUY at a price far below the market with
`expiration_ts = now+60`, then at +45s run the exact cancel→`reduced_by`→backstop sequence.
Records to capture: the cancel response shape for (a) fully-unfilled, (b) partially-filled,
(c) already-expired-but-not-yet-swept. (c) is unproven and matters: lazy expiry (~2-3min) means
a cancel *after* `expiration_ts` may 404 for "expired" rather than "filled", and
`supervise_one` (`:1282-1289`) treats **every** 404 as "it filled" → `mark_gone` → the backstop
is withheld forever and an alert fires for a position that does not exist. Cheap, decisive,
demo-only.

**P3 — House quotable-spread census (no orders).**
Read-only, 1 US trading day: every 30s, `GET /markets` + `GET /markets/{t}/orderbook` for the
in-band KXAPRPOTUS and KXCPIYOY rungs `pick_market` would choose. Record best_bid/best_ask/mid.
Deliverable: the H5 denominator — what fraction of US-daytime seconds actually clear the 2¢
spread gate. Answers "is the house probe idle because the gate works, or because we are blind"
without touching the order path, and sizes whether the 2-3 day probe budget can even produce a
verdict.

---

*Lane SENSORS. Findings F1-F13, instrumentation I1-I15, conformance S1-S17 / V1-V13 / H1-H7,
probes P1-P3.*
