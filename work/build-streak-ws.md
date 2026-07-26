# BUILD: Kalshi websocket market data for streak (2026-07-26)

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
