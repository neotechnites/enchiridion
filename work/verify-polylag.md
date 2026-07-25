# Lane 3 — POLYLAG ledger (2026-07-25)

Data: ~/kalshi_data/polylag_pairs.jsonl — 4485 rows, 13 pairs, 346 snapshots each, 60s cadence.
Span: t 1784977372→1784998014 = **5.73 hours**, single continuous capture.
Schema: pm/km = poly/kalshi mid; pb,pa / kb,ka = venue bid/ask; nb,na = kalshi NO book.

## Core finding: no episodes exist in this capture
Threshold-crossing move counts, whole window, all 13 tickers:
- Polymarket mid moves **≥3¢: 0**, ≥2¢: 0, **≥1¢: 1** (KHAR, single tick).
- Kalshi mid moves ≥3¢: 0, ≥2¢: 0, ≥1¢: **0**.
- Max poly range any ticker = 1.6¢ (KHAR); max kalshi range = 0.0¢ (all flat).

The lead-lag test is **unrunnable**: the episode trigger (Poly moves ≥3¢) fires zero times, and Kalshi never moves at all. There is no convergence to time and no follow to price. Dead-quiet window — 2028 primaries, no news, mid-session.

## Only cross-venue signal present: a persistent static basis (NOT a lag)
Median basis (pm−km) per ticker over full window (stable, not decaying):
GNEW +10.3, AOC +9.9, JOSS +5.3, PBUT +2.7, JVAN +2.4, RDES +0.3, ABES −0.1, MKEL/RKHA −0.4, JSHA −4.4, MRUB −4.5, REMA −7.1, KHAR −7.9.
Median poly-mid vs kalshi-ask (pm−ka): GNEW +8.8, AOC +8.4 largest.

Why this is not tradeable:
- It is a **standing** difference (constant across all 346 snapshots), not a convergence — no exit, nothing "follows."
- Kalshi book spread is **3.0¢ wide on every ticker** (median ka−kb=3.0). Round-trip cost = 3¢ spread + Kalshi taker fee ≈7%·p(1−p) (~0.4–1.5¢/side here). To realize the basis you'd need convergence that never occurs.
- Different-venue pricing of the same far-out event reflects genuine platform disagreement / resolution + liquidity differences, not a hittable arb.

## Verdict: CONDITIONAL — untestable on this capture, not proven dead
The strategy cannot be evaluated because the capture caught a window with essentially zero information flow (1 Poly tick ≥1¢ in 5.7h × 13 markets). No evidence for or against a Poly→Kalshi lead-lag edge.

**Gate to reopen:** re-run only on a capture that contains real news flow — specifically ≥1 window where Polymarket mid moves ≥3¢. Until such a capture exists, POLYLAG produces no measurable episodes and no money-impact claim (verified: 0 episodes, $0 reconstructable). Do not spend further on flat captures; the cheap decisive test is "does the tape contain any ≥3¢ Poly move?" — if no, stop.
