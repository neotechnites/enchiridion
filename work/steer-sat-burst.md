# Steering — Saturday burst (2026-07-25, ~1% budget, all-at-once)

Ryan's directive: eat the 1% now, one burst, see what comes of it. Four lanes, researcher-med,
**hard ceiling ~250k tokens each** — if you hit it, write the ledger with what you have and stop.

## Rules (all lanes)
- Write your ledger to the file named in your brief. Raw numbers, verdicts, no prose padding.
- **No git operations** — Fable commits everything after synthesis.
- **No subagents.**
- Money-impact claims must be RECONSTRUCTED from records, never inferred (R104). Label anything unverified.
- Kill taxonomy (enchiridion 15): CONDITIONAL is never a death — name the gate that makes it tradeable.
- API quirk if you touch Kalshi: plain fields (yes_ask/volume/open_interest) read None — only *_dollars / *_fp fields are live.
- Data lives in ~/kalshi_data/ (ls it first; captures are jsonl, some gz-rotated).

## Lane 1 — STREAK-RETRY (highest value: feeds the live bot)
File: work/verify-streak-retry.md
Live tape today: 3 IOC entries, 1 filled. Miss #2 (KXETH15M-26JUL251000-00, NO limit 31): book
snapshot ~300ms after the miss shows the 31¢ ask STILL THERE — a 1-2s retry likely fills. Miss #1
(KXETH15M-26JUL250945-45, NO limit 42): post-miss book shows NO ask at 44 (yes bid 56).
Question: from kbt_books_*.jsonl (btc + eth), in the FIRST 60s of each 15m window, quantify:
(a) capture granularity first — report it, work within it;
(b) given best ask ≤44 at time t, P(ask still/again ≤44) at t+1s…t+30s (flicker-and-return rate);
(c) simulate three entry policies on windows where a ≤44 ask existed in the entry window:
    current (one IOC at observed ask), limit-at-gate (one IOC at 44 — fills at real ask via price
    improvement), limit-at-gate + up-to-3 retries spaced ~2s. Report fill-rate and avg fill price each.
Output: policy recommendation with numbers. This decides a nestor code change.

## Lane 2 — XVENUE IN-GAME (pending verdict from note 36)
File: work/verify-xvenue-ingame.md
Matcher was fixed (side-aware); pre-game verdict was NO EDGE (maxnet −1.9¢). The open question is
IN-GAME: during live MLB games, does cross-venue maxnet ever exceed total round-trip cost (~2¢+)?
Use the xvenue capture jsonl. Report: episodes count, maxnet distribution in-game vs pre-game,
duration of any positive episodes (can a taker actually hit both legs?). Verdict: dead / conditional+gate.

## Lane 3 — POLYLAG (13 political pairs)
File: work/verify-polylag.md
polylag_pairs.jsonl — change-based episodes. Question: does one venue lead? When Polymarket moves
≥3¢, does the Kalshi pair converge, in what median time, and is the follow tradeable after Kalshi
taker fees given the captured Kalshi book at episode time? Same test in reverse (Kalshi leads).
Report: episode counts, lead-lag direction, net-after-fee per hypothetical follow. Verdict + gate.

## Lane 4 — LISTING-48H (first look since baseline)
File: work/verify-listing48h.md
listing_events.jsonl + listing_books.jsonl (baseline was 12,179 series). Report: NEW_SERIES count
since baseline, how many produced live two-sided books in their first 48h, spread width and any
obviously-anchorable mispricing (e.g. vs sibling markets, vs underlying) in those books. Verdict:
is the sloppy-window real enough to design a probe? If yes, sketch the probe (no build).
