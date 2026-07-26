# BUILD: Kalshi websocket market data for streak (2026-07-26)

## DECISIONS (note-23 Part II: DERIVED from repo/API evidence | JUDGMENT | UNDERIVED=needs demo/prod truth)
1. WS endpoint = api_base() with https->wss + `/trade-api/ws/v2`. DERIVED: matches the
   REST host pattern (prod api.elections.kalshi.com, demo demo-api.kalshi.co); two indep
   docs (eishan05, IntelIP/Neural) give exactly `wss://<same-host>/trade-api/ws/v2`. One
   doc (docs.kalshi quick-start) shows an alt host `external-api-ws.kalshi.com` — UNDERIVED
   which prod host is canonical; demo connect test resolves it (demo host is agreed).
2. WS auth = sign `{ts_ms}GET/trade-api/ws/v2` RSA-PSS/SHA256, same 3 KALSHI-ACCESS headers
   on the HTTP upgrade request. DERIVED: identical to REST sign_headers (path-only, no query),
   all 3 docs agree. Reuses the existing SigningKey. UNDERIVED: whether market-data ws requires
   auth at all (public REST needs none) — we always send it; demo test confirms handshake.
3. Subscribe cmd = `{"id":N,"cmd":"subscribe","params":{"channels":["orderbook_delta"],
   "market_tickers":[T]}}`, one ticker per cmd so each gets its own `sid` for clean
   unsubscribe. DERIVED from docs; JUDGMENT: per-ticker (not batched) for churn hygiene.
4. Book model: Kalshi book = two bid sides (`yes`,`no` price-level arrays). yes_ask = 100 -
   best_no_bid; no_ask = 100 - best_yes_bid. DERIVED: identical convention to the existing
   REST `orderbook_mid()` in kalshi.rs (verified 2026-07-25 live schema).
5. Level/price parse tolerant of int-cents (`yes`/`no`) AND string-dollars (`yes_dollars`/
   `no_dollars`), seq top-level, delta `price`|`price_dollars`+`delta`+`side`. JUDGMENT:
   mirrors the codebase's *_dollars/_fp tolerance doctrine; exact wire form is UNDERIVED and
   the demo test prints raw JSON to confirm. Prefer plain cents arrays, fall back to _dollars.
6. seq gap -> mark book unsynced + drop levels + unsubscribe so the 1s reconcile re-subscribes
   -> fresh snapshot resets seq. JUDGMENT (docs don't specify get_snapshot format); while
   unsynced streak falls back to REST.
7. Integration: STREAK_WS=1 AND ws quote synced AND age<1s -> override cand asks; else REST.
   Divergence logged ALWAYS (flag-independent) to data/ws_divergence.jsonl, gated to the entry
   window (first 60s) to bound volume. DERIVED from charter. A dead ws NEVER blocks an entry
   (background task, non-blocking Mutex read, REST is the floor).
8. ws task spawned in BOTH `run` and `streak` standalone; runs regardless of the flag so
   divergence tape accrues immediately. If no key (paper/public) it still attempts connect and
   backs off quietly. DERIVED from charter ("run alongside while flag OFF").

## DEMO EVIDENCE (2026-07-26, ws::demo_probe against demo-api.kalshi.co, KXBTC15M-26JUL261330-30)
- Endpoint RESOLVED: `wss://demo-api.kalshi.co/trade-api/ws/v2` -> handshake **HTTP 101
  Switching Protocols** (Decision 1 host+path correct; prod host is api.elections mirror).
- Auth RESOLVED: signed `{ts}GET/trade-api/ws/v2` headers ACCEPTED (Decision 2). ALL-channels-
  need-auth holds.
- Subscribe/ack RESOLVED: `{"id":1,"cmd":"subscribe","params":{"channels":["orderbook_delta"],
  "market_tickers":[T]}}` -> `{"type":"subscribed","id":1,"msg":{"channel":"orderbook_delta",
  "sid":1}}`. seq top-level, starts at 1 (snapshot) then deltas 2,3,4… advance by exactly 1.
- Wire schema RESOLVED (corrected Decision 5 — the docs were WRONG on the key): live SNAPSHOT
  side arrays are `no_dollars_fp`/`yes_dollars_fp` = `[["0.4400","335.00"],…]` (string-dollar
  price + string-qty), NOT `no`/`no_dollars`. First demo run silently loaded an EMPTY snapshot
  (book only rebuilt from deltas) — BUG CAUGHT AND FIXED here (parse_side_levels now checks
  `_dollars_fp` -> `_dollars` -> bare cents). DELTAS use `msg.price_dollars`(str) + `delta_fp`
  (str, signed) + `side` + `ts_ms`. Post-fix the 3-level snapshot loaded and best-ask =
  100 - best_opposite_bid computed correctly (best no bid 0.85 -> yes_ask 15, live).
- Book stayed synced across 15s of live deltas; age_ms tracked receive latency (7-1345ms;
  the 1s freshness gate correctly rejects the between-delta stretches on this thin demo book).

## PROD SEQ-GAP BUG + FIX (2026-07-26, root-caused against prod directly)
SYMPTOM: on prod the maintainer reconnect-looped — within ~200ms of every connect,
"connection ended (seq gap — resync via reconnect)". ROOT CAUSE (prod raw capture,
ws::demo_probe::prod_seq_probe, 2 tickers on one connection): `seq` is ONE monotonic
counter PER SID (per connection for a channel), NOT per-ticker — and it spans EVERY
seq-bearing frame, including a `type:"ok"` control frame. Observed stream under sid=1:
`snapshot(seq1,BTC) · ok(seq2) · snapshot(seq3,ETH) · delta(seq4) · delta(seq5) …` unbroken.
My original per-ticker gap check saw BTC jump `seq 1 -> 4` (the `ok`+ETH-snapshot seqs fell
between BTC's frames) and declared a false gap on every connect. FIX: track sequence per
top-level `sid` at the CONNECTION level (`seq_in_order`), counting all seq-bearing frames;
a per-sid gap = a genuinely dropped frame -> reconnect (true-gap behavior preserved).
apply_event no longer does per-ticker seq detection. VERIFIED on prod: with BOTH KXBTC15M +
KXETH15M subscribed, one connect held 15s with ZERO gap-reconnects, both books live and
updating (BTC yes_ask 77->78->79, ETH 73->74, age 2-88ms, synced). Snapshot/delta/`ok`
schema note: `ok` frames carry sid+seq and no book payload (parsed as Other, still counted).


## Why (R155/R156): the REST path costs money
Streak polls GET /markets + orderbook ~1/s in entry windows. Reconstruction proved total quote
staleness of ~0.5-3+s (poll age + Kalshi's REST cache layer, which lags the matching engine
seconds during bursts — same infra family as the 36s settled-filter lag and the eventually-
consistent resting-orders list). At 06:00Z Jul 26 the engine traded 48-51 while REST served a
44-ask book. Fix: Kalshi's websocket streams orderbook deltas from the event flow — latency
becomes network RTT (~tens of ms), no poll age, no cache.

## Charter (enchiridion 23 Part II binding: enumerate decisions DERIVED/JUDGMENT/UNDERIVED;
## the API docs are evidence, not authority — verify behaviors empirically on demo where possible)
- Engine module `crates/engine/src/ws.rs`: authenticated websocket client for Kalshi trade-api
  ws v2 (wss endpoint; auth per existing sign_headers pattern — figure out the connect
  handshake from docs + verify). Subscribe orderbook_delta (and ticker if useful) for a dynamic
  set of tickers (streak's active KXBTC15M/KXETH15M windows change every 15 min — subscription
  churn must be handled).
- Maintain an in-memory book per subscribed ticker (apply deltas over snapshot; detect sequence
  gaps → resnapshot; timestamps on every update).
- Expose to strategies: freshest best-ask/bid per side + book age. STREAK INTEGRATION BEHIND
  STREAK_WS=1: when the flag is on AND the ws book for the ticker is fresh (<1s), the entry
  path uses it; otherwise falls back to the existing REST read (never block an entry on ws
  health). Reconnect with backoff; a dead ws NEVER halts trading — REST is the floor.
- VALIDATION LOGGING (the deliverable that decides switchover): while flag is OFF, still run
  the ws alongside and log ws-vs-REST ask divergence per entry-window pass to
  data/ws_divergence.jsonl (ts, ticker, ws_ask, rest_ask, ws_age_ms) — one day of that tape
  proves the latency win and any correctness gaps before the flag flips.
- cargo test + clippy green; no git (Fable reviews/commits); tokio-tungstenite or equivalent
  (add dep via workspace); ceiling ~450k tokens. Decisions section at top of this file.
