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

---
# WAVE 2 (same rules, same ceilings — wave 1 came in at ~137k total, budget is deep)

## Lane 5 — FEDMENTION-PRIOR (Tuesday-critical)
File: work/verify-fedmention-prior.md
KXFEDMENTION-26JUL (43 rungs) locks at the Jul 29 FOMC presser end. Build per-rung priors:
(a) fetch the actual rung structure + current prices from the API (series KXFEDMENTION; only
*_dollars/_fp fields live); (b) download the last 8-12 FOMC press-conference transcripts from
federalreserve.gov and count target words PROGRAMMATICALLY (curl + python word counts on disk —
NEVER load transcript text into your context); (c) per word: historical count distribution → fair
probability per rung; (d) table: rung | market price | prior fair | divergence; flag ≥15¢.
This is PREP ONLY — no trading, no recommendation to trade live; Ryan decides Tuesday.

## Lane 6 — STREAK-CLOCK (feeds week-2 sizing + VPS decision)
File: work/verify-streak-clock.md
From kbt_books_btc/eth (100ms tape) + the Kalshi API (status-agnostic, min/max_close_ts) reconstruct
the past week of 15m results per series: (a) actual 4-streak signal occurrences per day, by hour;
(b) fraction whose entry-window ask was ≤44¢ (join with books); (c) expected trades/day and EV/day
under wave-1 Lane-1's measured fill rates (70.5% / 82% / 88.5% policies); (d) overnight (00-13Z) vs
daytime split — quantify "streak is a daytime strategy". Label every EV figure with its n.

## Lane 7 — DUTCH+DERIBIT first look
File: work/verify-dutch-deribit.md
Two never-analyzed captures, one lane: dutchbook jsonl (any multi-outcome book summing <$1 net of
fees? episode count, depth, duration) and deribit_gate_hourly.jsonl (Kalshi crypto ladder ivol vs
Deribit ivol — does the gate show a persistent gap, direction, tradeable after fees?). Two verdicts.

## Lane 8 — SEED-PRIOR feasibility (feeds Lane-4's listing gate)
File: work/verify-seed-prior.md
Wave-1 found new mention-grab series seed at a uniform 0.54/0.46 ignoring base rates. Feasibility:
from settled mention/count markets (API, settled history) compute per-word realized YES rates vs
the 0.54 seed; distribution of |realized − seed|; with a naive external prior (word frequency in
past source material), what per-market edge would a seed-hitter have captured? Report n everywhere.
Small ceiling: ~150k.
