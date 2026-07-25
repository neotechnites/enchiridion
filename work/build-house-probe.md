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
