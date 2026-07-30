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
