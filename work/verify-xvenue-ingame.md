# Lane 2 — XVENUE IN-GAME verification ledger

Data: `~/kalshi_data/xvenue_paper.jsonl` (62 episode rows) + `~/kalshi_data/xvenue_watch.log`
(225 lines: 84 per-episode alert lines w/ `poly_depth`, 136 `[hb]` heartbeats).
Class: KXMLBGAME cross-venue (Kalshi bid vs Polymarket ask, both sides of each MLB game).
Round-trip cost assumed ~2c+ (per brief / note 36 pre-game baseline).

## Fix boundary (matcher fixed = side-aware)
Boundary is unambiguous in the log. Heartbeat `maxnet` has large positive spikes
(up to **+80.7c**, +41.6c, +24.9c…) up through `[hb 23:21]` (log line 158, 2026-07-24 local).
From `[hb 23:31]` (line 159) onward, `maxnet` goes **permanently negative and stays there** for
the next ~11h. That transition IS the fix. Local = UTC-6 (verified: last alert `23:15:59` LAASF-LAA
net 16.8c ↔ paper row t=1784956541 = 2026-07-25 05:15:41 UTC).

- **PRE-FIX** = log lines ≤158 and ALL 62 paper.jsonl rows (t 2026-07-24 20:14Z → 2026-07-25 05:15Z,
  i.e. local 14:14→23:15, all before 23:31). Buggy (side-crossed) matcher.
- **POST-FIX** = log lines ≥159, `[hb 23:31]` 2026-07-24 → `[hb 10:43]` 2026-07-25 local (~11.2h).

## POST-FIX result (the fixed matcher) — the only valid evidence
- 67 heartbeats, **84/84 games matched** on every beat, spanning live night games
  (west-coast 22:15 starts still in-play 22:xx–01:xx) plus pre-game for next-day slate → genuine
  in-game coverage.
- `maxnet` distribution across those 67 beats: **-2.3c ×43, -2.9c ×17, -2.4c ×5, -1.9c ×2**.
- **Positive heartbeats post-fix: 0. Max maxnet ever observed post-fix = -1.9c.**
- **Per-episode alert lines fired post-fix: 0** (nothing cleared threshold → nothing hit paper).
- Best case (-1.9c) is still ≥3.9c short of the ~2c round-trip cost. In-game is WORSE than the
  pre-game -1.9c baseline (floor drops to -2.9c).

## PRE-FIX artifacts (the 62 paper episodes) — all invalid, superseded by fix
- 62 rows, all `pregame:false` (in-game), 14 unique games. net min/median/max = 4.03 / 7.46 / 80.72c.
- 56/62 are non-degenerate on their face (pask≥5 & kbid≤95, median net 7.46c) — they LOOKED
  tradeable, which is exactly why the buggy side-crossed matcher was dangerous.
- 6/62 are near-settle degenerate (Poly ask ≤3c at game end, e.g. LAASF-LAA pask 1.0 net 16.83 —
  a collapsing losing side, not an arb).
- Every one of these vanishes under the side-aware matcher (post-fix maxnet negative). Per R104:
  the pre-fix "edge" is a matcher bug, NOT reconstructed money — treat as zero.

## Verdict: DEAD
In-game cross-venue does NOT beat cost. Fixed matcher shows maxnet capped at -1.9c across 84 games ×
67 samples including live play; zero positive episodes; zero paper fills. Not CONDITIONAL — there is
no gate that recovers a ≥3.9c shortfall; the only positives on record are pre-fix side-crossing
artifacts. Kill.
