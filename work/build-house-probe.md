# BUILD: maker execution module + house fill-probe (H10/H9) — 2026-07-25

---
## DESIGN DECISIONS (enumerated per enchiridion 23 Part II) — implementor, 2026-07-25

Legend: **DERIVED** = forced by data/protocol/API; **JUDGMENT** = reasonable choice, defensible, not forced; **UNDERIVED** = assumption that needs empirical confirmation (flagged for demo shakeout / Fable).

### Safety mechanics (all NON-NEGOTIABLE per charter — implemented in code)
- **expiration_ts = now + 75s on every resting order** — DERIVED (charter §1: "≈75s"; a dead process leaves nothing resting >~1min). Kalshi V2 semantics: `expiration_ts` in future → GTD (auto-cancel at ts); omitted → GTC (never used); past → IOC. We ALWAYS set a future ts. Load-bearing safety property.
- **Omit `time_in_force` on resting orders** (set expiration_ts instead of `immediate_or_cancel`) — UNDERIVED: Kalshi V2 create-order rests when time_in_force is absent and expiration_ts is future. Must confirm on demo the API accepts a create with expiration_ts and NO time_in_force and returns a `resting` order (not an instant IOC). This is the single most important demo check after auto-cancel.
- **self_trade_prevention_type = "cancel_both"** on resting legs — JUDGMENT: our bid (mid−1) and ask (mid+1) never cross (≥2¢ apart), so STP should never fire; cancel_both is the safe-if-it-ever-does choice. Confirm demo accepts the enum value (UNDERIVED on exact string).
- **Cancel-all-house-orders on startup AND on shutdown** — DERIVED (charter §2). Startup: GET /portfolio/orders?status=resting → cancel_order each id (sweeps orphan quotes from a prior crash). Shutdown: SIGTERM/ctrl-c handler cancels every tracked live order before exit.
- **−$20 cumulative hard stop, in code** — DERIVED (charter §3, protocol). Probe P&L (realized round-trips + open-position mark + fees) tracked live; breach → cancel all quotes, sticky-halt, alert.
- **−5¢-markout-in-60s gap-through stop** — DERIVED (protocol). Any single fill marked out worse than −5¢ within 60s → same sticky halt.
- **Live-gated: HOUSE_PROBE=1** — DERIVED (charter §5, mirrors volbook VOLBOOK_LIVE). Standalone `house` banned in live by nestor_bin; real orders refused unless HOUSE_PROBE=1; paper = log-only shadow quoting.

### Quote parameters
- **Quote offset ±1¢ (bid mid−1, ask mid+1)** — DERIVED (protocol §Protocol: "bid at mid−1¢, ask at mid+1¢"). Expressed on the single YES book as bid=("yes", mid−1) and ask=("no", 99−mid) via existing `order_price_dollars`.
- **Mid = (best_yes_bid + best_yes_ask)/2** from the orderbook, rounded — JUDGMENT (protocol says "mid"; standard definition). Falls back to yes_ask−1 if only one side quoted; stands down if no two-sided book.
- **Spread gate ≥2¢** — DERIVED (protocol §1: "only post when resting book spread ≥2¢; at 1¢ the edge collapses").
- **Size 1–5 contracts/side** — DERIVED (protocol + charter §4). Default **1** contract (JUDGMENT: smallest decisive size for a mechanics probe; ramp only after fill-rate seen). Bounded by risk stake = worst-case fill exposure.
- **Re-quote triggers: mid move ≥1¢, own-fill (flatten opposite side), 60s staleness** — DERIVED (protocol). Staleness is also handled passively by expiration_ts; the 60s active re-quote is belt-and-suspenders.
- **Poll cadence 3s** — JUDGMENT (fill/mid polling; fast enough to catch mid moves inside a 75s order life, slow enough to stay well under rate limits). UNDERIVED vs an ideal cadence; 3s is a safe first value.

### Catalyst windows (T±15min pulls) — charter §4 "derive the window list per vehicle"
- **KXAPRPOTUS** — the RCP national-average approval updates on no fixed public minute-level schedule (rolling poll releases). JUDGMENT/UNDERIVED: without a published release calendar we CANNOT enumerate exact T's; we instead pull on any **mid jump ≥3¢ between polls** (a proxy for an unscheduled catalyst) and require an operator-supplied window list via `HOUSE_CATALYST_TS` (comma-sep unix ts) when known. Flagged UNDERIVED — the protocol assumes a derivable cadence; first principles say RCP has none at minute resolution. **STOP-AND-WRITE-DOWN divergence: protocol §2 presumes a scheduled poll cadence to gate on; reality is unscheduled. Resolution: gap-jump proxy + optional operator ts list.**
- **KXCPIYOY** — CPI release is scheduled: **08:30 ET on the BLS release day**. DERIVED window: pull T−15min..T+15min around 08:30 ET on the market's CPI print date (derivable from BLS calendar; operator supplies the date via `HOUSE_CPI_RELEASE` = YYYY-MM-DD, else the market close date is used as a conservative proxy). Partially UNDERIVED (release date needs the BLS calendar, not in-code).

### Vehicle selection
- **KXAPRPOTUS front weekly, single in-band strike 0.30–0.70** — DERIVED (protocol §Vehicle). Pick the open market whose YES-mid is nearest 0.50 within [0.30,0.70].
- **KXCPIYOY nearest "Exactly" rung, 0.10–0.90 band** — DERIVED (protocol §Vehicle fallback). Pick the "Exactly" rung whose YES-mid is in [0.10,0.90] and nearest a live print.
- Both tickers/series exact strings are UNDERIVED until confirmed against the live `/markets` listing (series tickers assumed from protocol; confirm on demo/prod-read).

### Risk accounting
- **Maker probe stake = worst-case fill exposure** (count × limit for the buy leg) counted against the daily budget like any position — DERIVED (charter Build shape). Routed through a parallel maker path with its own accounting; the IOC path and risk semantics are untouched.
- **Probe P&L for the −$20 stop is tracked in the house crate** (not the shared RiskManager), because RiskManager settles on binary outcome, not intraday round-trip markout — JUDGMENT. The house crate keeps its own realized+unrealized cent ledger for the stop; positions still register with RiskManager for portfolio caps.

---


Ryan authorized implementation + probe. This is the implementor's charter. The probe PROTOCOL
(gates, metrics, kill/promote) is work/probe-house.md — read it fully; it is evidence of intent,
not authority (enchiridion 23 Part II: enumerate + derive every design decision before coding;
flag UNDERIVED; where protocol and first principles diverge, STOP and write it down).

## What this adds (and the risk that makes it different)
Nestor is IOC-taker-only. This module adds RESTING two-sided quotes — the first orders that can
exist while we're not watching. Every safety decision below is therefore NON-NEGOTIABLE:

1. **Every resting order carries `expiration_ts` ≈ 75s** (Kalshi V2 create-order supports it).
   A dead process must leave NOTHING resting beyond ~1 minute. This is the load-bearing safety
   property — verify on DEMO that expiration actually cancels, before anything else.
2. **Cancel-all-house-orders on startup AND on shutdown** (SIGTERM/ctrl-c handler): startup
   sweeps any orphan quotes from a prior crash (query resting orders, cancel by id).
3. **−$20 cumulative hard stop enforced IN CODE** (probe P&L tracked incl. fees; breach → cancel
   all quotes, sticky-halt the probe, alert). Also the protocol's −5¢-markout-in-60s gap-through
   stop.
4. **Quote gates in code:** spread ≥2¢ else stand down; catalyst-window pulls (T±15min around
   the vehicle's scheduled updates — derive the window list per vehicle and write it down);
   1-5 contracts per side, never more.
5. **Live-gated like volbook:** standalone banned in live by nestor_bin; scheduled in `run` ONLY
   when HOUSE_PROBE=1; refuses real orders without it. Paper mode = log-only shadow quoting.

## Build shape (constraints)
- Engine: add a `maker` capability in crates/engine (place resting limit w/ expiration_ts,
  cancel by order_id, list own resting orders, poll fills for resting orders). Do NOT touch the
  IOC path or risk semantics; maker orders route through a parallel, clearly-named path with
  their own risk accounting (probe stake = worst-case fill exposure, counted against the daily
  budget like any position).
- Strategy crate `crates/house` implementing the Strategy trait: the two probe books from the
  protocol (KXAPRPOTUS front-weekly in-band strike; KXCPIYOY nearest "Exactly" rung 0.10-0.90),
  quote loop (re-quote on mid-move ≥1¢ / own-fill flatten / 60s staleness — but expiration_ts
  handles staleness passively), full participation logging to data/house_probe.jsonl:
  quote-minutes per side, fills w/ timestamps + mid-at-fill, markout at +60s, gap-through flags,
  spread at quote, pulled-window flags. A `house-report` subcommand summarizes the protocol's 4
  metrics from the log.
- **DEMO FIRST:** a `house-demo` path (KALSHI_API_BASE override to demo + demo key from
  secrets/Demo.txt per existing kalshi.rs support) proving: resting placement, expiration
  auto-cancel, cancel-by-id, fill detection, startup orphan sweep. Write the demo evidence into
  this file's Decisions section. NO prod order until Fable reviews.
- cargo test green (existing suite + yours), clippy clean, NO git operations — Fable reviews and
  commits. API quirk: only *_dollars/*_fp fields are live. Enumerate ALL design decisions
  (quote offsets, re-quote thresholds, window lists, sizing, timeouts) as DERIVED / JUDGMENT /
  UNDERIVED at the top of this file.
- Token ceiling ~500k: if tight, deliver engine maker capability + demo evidence first, the
  strategy loop second.

## Deliverable
Branch-ready diff + Decisions section + demo-run evidence + 5-line summary (works / UNDERIVED /
untested).

---
## DECISIONS & EVIDENCE (implementor, 2026-07-25)

### What was built (branch-ready, NO git ops — Fable reviews/commits)
- **Engine maker capability** — `crates/engine/src/kalshi.rs`:
  - `place_resting_limit_raw(ticker, side, count, price_cents, expiration_ts, coid)` — POST
    /portfolio/events/orders with a FUTURE `expiration_ts` and NO `immediate_or_cancel`
    (GTD resting); `self_trade_prevention_type=cancel_both`. The taker `place_limit_buy` path is
    UNTOUCHED.
  - `resting_orders(ticker: Option<&str>)` — GET /portfolio/orders?status=resting (startup orphan
    sweep + expiration audit). `cancel_order(id)` already existed — reused.
  - `parse_resting_orders` (tolerant) + `RestingOrder` struct + `orderbook_mid` (best bid/ask/mid).
    All pure + unit-tested (7 new tests).
- **`crates/house`** — the maker sleeve. `signal.rs` (pure gates/quote-math/markout/−$20 ledger/
  metrics, 14 tests), `strategy.rs` (the stateful two-sided quote loop: sweep-on-start, spread &
  catalyst gates, fill detection + flatten, +60s markout/gap-through, −$20 & −5¢ sticky stops,
  HOUSE_PROBE live gate, shadow logging to `data/house_probe.jsonl`), `report.rs` (`house-report`
  4-metric summarizer, 2 tests).
- **nestor_bin wiring** — `house`/`house-once` standalone (paper/shadow; ctrl-c sweep via
  `tokio::select!`), `house-report`; `house`/`house-once` added to the **live-standalone ban**;
  scheduled in `run` ONLY when `HOUSE_PROBE=1`; tokio `signal` feature enabled.
- **Green:** whole workspace `cargo build`, `cargo test` (13 result-ok blocks, 0 failed; +23 new
  tests), `cargo clippy --all-targets` **0 warnings**.

### EMPIRICAL findings from live PUBLIC data (paper smoke, 2026-07-25 ~20:25Z)
1. **Series tickers CONFIRMED valid:** `KXAPRPOTUS` (8 open weekly strikes) and `KXCPIYOY` (89 open
   rungs) both resolve. The earlier UNDERIVED flag on tickers is RESOLVED.
2. **Orderbook schema was WRONG in my first cut — FIXED.** Live shape is
   `{"orderbook_fp":{"yes_dollars":[["0.4800","30.00"]],"no_dollars":[["0.0100","130.00"]]}}` —
   string-DOLLAR prices under an `_fp` envelope, NOT `orderbook.{yes,no}` integer cents.
   `orderbook_mid` now handles both; added a verbatim-schema test. **This is exactly the class of
   bug the demo shakeout exists to catch — caught here on public data.**
3. **KXCPIYOY has ZERO "Exactly" rungs — only "Above X%" cumulative rungs.** A hard `exactly_only`
   filter would mean the CPI book NEVER quotes. **DIVERGENCE (protocol §Vehicle presumes an
   "Exactly" rung; reality has none on this series).** Resolution: `prefer_exactly` — use "Exactly"
   rungs when the ladder has them, else the nearest-centre in-band rung. JUDGMENT, flagged. (The
   "Exactly" rungs the protocol means may live on a different series or appear only near a print —
   unresolved; confirm with Fable.)
4. **Full pipeline VERIFIED end-to-end in paper:** POTUS selected in-band strike
   `KXAPRPOTUS-26JUL31-40.9`, parsed the real book (bid 29 / ask 30 / mid 30), and **correctly
   stood down on the 1¢ spread gate** (protocol §1: never quote a 1¢ market). CPI picked
   `KXCPIYOY-26NOV-T4.7`, got a one-sided book, stood down. EXIT=0, no orders (paper), no errors.
5. **Current books are thin/tight** (far-dated CPI, coarse POTUS weekly ladder mid-day): no vehicle
   met the ≥2¢ spread gate this instant. Stand-down is correct; a real 2-3 day daytime run near
   catalysts is where quotable windows appear. Stand-downs are silent by design (matches volbook's
   resting-state logging philosophy).

### DEMO maker-mechanics run — **BLOCKED on the demo key id (Ryan)**
The demo `KALSHI_API_KEY_ID` is **not on disk**: `secrets/Demo.txt` is the PEM only, and `.env`
holds the PROD key (`./secrets/prod.pem`, id …8850, `NESTOR_ENV=live`). Every existing demo network
test is `#[ignore]` and reads the demo id from env at runtime — same blocker as
`demo_duplicate_coid_behavior`. The maker shakeout test is written and ready:
`crates/engine/src/kalshi.rs::maker_demo_probes::maker_demo_resting_lifecycle`, proving all five
mechanics (RESTING placement did-not-IOC / appears in resting list / cancel-by-id / **expiration
auto-cancel** / orphan sweep). Run once the demo id is supplied:
```
KALSHI_API_BASE=https://demo-api.kalshi.co \
KALSHI_API_KEY_ID=<DEMO key id> KALSHI_PRIVATE_KEY_PATH=secrets/Demo.txt \
NESTOR_TEST_TICKER=<open demo ticker ~40-60c> \
cargo test -p engine maker_demo_resting_lifecycle -- --ignored --nocapture
```
**UNTESTED until that runs** (require a real resting order): the 201 response shape for a resting
order (fill_count 0 / remaining==count / status "resting" / order_id — i.e. that omitting
time_in_force + future expiration_ts REST rather than IOC — the top UNDERIVED item), whether the API
requires `time_in_force`, `self_trade_prevention_type="cancel_both"` acceptance, expiration
auto-cancel timing, the `/portfolio/orders` resting schema (parser is tolerant but unconfirmed), and
the −$20 / −5¢ stops firing on real fills.

### Files changed
- `crates/engine/src/kalshi.rs` (maker methods + parsers + tests + ignored demo probe)
- `crates/house/` (new crate: Cargo.toml, lib.rs, signal.rs, strategy.rs, report.rs)
- `crates/engine`/workspace `Cargo.toml`, `nestor_bin/{Cargo.toml,src/main.rs}` (wiring + gates)

---
## DEMO EVIDENCE (Fable, 2026-07-25, demo acct, KXBTC15M windows) — the UNDERIVED items resolved
1. **RESTING PLACEMENT:** omit-time_in_force assumption WRONG — API requires it (400 'required').
   Valid combo (empirical): `time_in_force="good_till_canceled"` (single-L; "good_till_cancelled"
   and "good_till_date" fail oneof) + FUTURE `expiration_ts` + `self_trade_prevention_type=
   "taker_at_cross"` ("cancel_both" fails oneof) → HTTP 201, fill_count 0, remaining==count,
   order_id present (did NOT IOC). kalshi.rs patched accordingly.
2. **EXPIRATION AUTO-CANCEL: WORKS BUT LAZY.** now+8s order survived polls to +128s past expiry,
   gone by +143s → enforcement sweep ≈ every 2-3 min. WORST-CASE ORPHAN EXPOSURE = TTL(75s) +
   ~3min ≈ ~4min of stale quotes, NOT ~1min as chartered. Accepted for the probe at size 1
   (≈$2 worst-case per book); revisit TTL if size grows.
3. **CANCEL-BY-ID: WORKS** (`reduced_by:"1.00"` in response = truth).
4. **RESTING LIST = EVENTUALLY-CONSISTENT INDEX** (orders lag seconds to appear AND to
   disappear; one run showed still-listed right after a proven cancel). Rule: responses are
   truth; the list is ONLY for orphan sweeps where lag is harmless. Same indexer-lag family as
   the settled-filter bug (R114).
5. **ORPHAN SWEEP: WORKS** (listed 1, canceled, 0 remain).
Fill detection not exercised (2¢ bid never crossed) — reuses the proven fills() parser; first
real probe fill validates it.
