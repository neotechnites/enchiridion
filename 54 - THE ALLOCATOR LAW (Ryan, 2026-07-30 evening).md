# 54 - THE ALLOCATOR LAW — Ryan, 2026-07-30 evening

Supersedes note 52's allocator half (water-fill, D5′, D9-retirement, pass-2, displacement).
Safety rails and measurement discipline unchanged.

## The law (Ryan verbatim intent)
- Rank every market by **capital needed to earn $1.50 in the next 24h**. Inputs: pool,
  competition (S), time left, φ, accrued (subtracts; ≥$1.50 banked ⇒ market done, fund
  next-best). Fund cheapest first until capital gone.
- **One order per cluster** (markets that resolve the same). ~30 winners at $300.
- **$10/market allocation, $300 total** — the only strategy constants (later derived from
  capital). $10 = order sized to the need + requote budget. Examples (his): need $5 @ φ2.5 →
  $5 order, requote on fill; need $10-dollar-hours @ φ24 → ~42¢ × 24 requotes; need >$10 →
  can't afford, skip; need ≪$10 → oversize to full $10 (capital has nowhere else).
- Floor: $1.50/24h AND ≥$1 by window end (forfeit).
- φ: per-market measured → price-bucket average → global average.
- Former gates fold into the formula: qualification cost (empty side = self-qual cost →
  effectively unaffordable at $10, logged skip), entry band stays (kept avg price ~15¢ —
  Ryan wants that preserved). p6 → φ evidence only. Free-ride flag, D11, pass-2,
  displacement, water-fill: DELETED.
- Capital events (settle/sale): normal cycle re-ranks — easiest wins.
- Full-universe ranking (programs feed cheap for all; book reads only for top candidates).

## Also today (same session)
- NEW SAFETY LAW deployed + APPROVED (worker+Fable-manager loop, 778 green): never sells;
  halt = cancel OWN orders only then idle; startup ignores inherited orders, book starts
  empty; no cap exemptions. Root cause of $93 order: halted closing pass sized from blind
  books, cap-exempt, GTC-survived restarts.
- v5 RUNNING on that build. Allocator-law build in flight (wt-alloc, Opus worker +
  Fable manager).

## THE CAPITAL-SCALING PROCEDURE (Ryan, 2026-07-30 — execute when capital goes to $1–2k)

Goal: derive the new per-cluster allocation A (today $10) and the new price floor, from capital.

1. **A = C/N, and N is capital-independent.** N ≥ z²·p(1−p)/(d−p)² where d = day-stop
   fraction (0.2), z = confidence (2), p = per-cluster daily wipe probability. Under the
   always-filled worst case (φ dropped deliberately — at $10/24h assume it fills) and no
   informational edge (else we'd be takers), p reduces to the tail of FAIR draws — because
   the price floor caps the calibration gap, making filled inventory EV≈0 variance, not bleed.
   Re-measure p from cluster-days tape (loss ≥80% of cluster allocation / cluster-days)
   before scaling. With p≈8–10%: N≈25–36 → at $2k, A≈$55–80.
   (ERRATA, [[55]]: the literal always-filled/board-price reading of p is DIAGNOSTIC-only —
   it gives p≈0.9 ⇒ N→∞. The measured 8–10% cluster-days prior owns the LEVEL of p; the
   funded mix enters as a ratio of calibration-degraded settle-against probabilities.)
2. **The floor is a second dial, not a constant.** The 6¢ band edge + ~15¢ selection average
   exist to cap the calibration gap (posted-vs-realized, 8× overpriced at 1–2¢ → fills bleed;
   ≥~15¢ posted≈realized → fills are fair variance). This trades rung availability for safety.
3. **At higher capital, lower the floor as good rungs exhaust.** Thickening rungs hits share
   concavity (√ saturation); admitting cheaper rungs hits bounded bleed but linear credit.
   Rule: lower the floor until marginal admitted rung's (expected credit − measured bleed) =
   marginal value of thickening the existing book. Both sides come from the price-bucket
   calibration measurement, not opinion. Evidence the frontier is near: sub-5¢ cohort was
   net +$3.88 even on v4's undisciplined book — dominated at $300, marginal at $2k.

## COMPETITION RECORDER — live 2026-07-30 ~14:00 MT (v6's S(t) dataset)
kalshi_data/scripts/competition_recorder.py on VPS (pid varies, @reboot cron). WS
orderbook_delta for EVERY in-window rewarded market (3,605 at start), every change,
ms-stamped, full snapshot per book every 15 min (reconnect refresh) → exact S(t)
reconstruction at any second; time-of-day competition analysis trivial. Output:
~/kalshi_data/competition/deltas-YYYYMMDD.jsonl.gz (~10-30MB/day est). Both allocator
builds done (Opus: allocator-law, 700 green; Fable: allocator-law-f, 697 green) — differ on
short-window target (strict 1.50 vs pro-rate-clamped-at-$1) and oversize scope; Fable
manager adjudicating head-to-head, grafts via send-backs, one survivor.
**(Resolved 2026-07-30 ~21:50 MT: the Fable branch WON with three Opus grafts — record in
[[55]] "§7 UPDATE — APPROVED FINAL".)**
