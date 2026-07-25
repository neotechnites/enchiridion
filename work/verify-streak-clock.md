# Lane 6 — STREAK-CLOCK ledger
Date: 2026-07-25. Feeds week-2 sizing + VPS decision.
Strategy (note 13): after a ≥4-streak on a crypto 15m series, buy the REVERSAL side in first 60s if ≤44¢, hold to settle.

## Data
- Results: Kalshi API `markets` (status-agnostic, min/max_close_ts), series KXBTC15M + KXETH15M.
  759 settled windows each, **2026-07-17 17:00Z → 2026-07-25 16:45Z = 7.99 days**, ~contiguous (1 gap each).
  Result dist near-balanced (BTC 388y/371n, ETH 390y/369n).
- Entry-window asks: kbt_books_{btc,eth}.jsonl, 100ms tape, **only ~35h coverage** (24 Jul 03:45 → 25 Jul 15:30, 206 windows). field1=yes_ask, field2=no_ask (decode confirmed by Lane 1).

## (a) 4-streak signal occurrences — RAW (before price gate)
- **170 signals / 7.99 days = 21.25/day** (BTC 76 = 9.50/day, ETH 94 = 11.75/day).
- Reversal win rate (held to settle, R104 actual results): **0.547 (n=170)**. BTC 0.605 (n=76), ETH 0.500 (n=94).
- Per day: 6, 9, 22, 23, 20, 26, 19, 30, 15 (Jul17→25; 17th & 25th are partial days). Lumpy — long runs cluster signals.
- By hour (UTC), n / winrate: 06Z n=17/0.35, 13Z n=20/0.50 are the busiest; small-n hours noisy. Full table in raw run.

## (b) Gate-pass fraction (reversal ask ≤44¢ in first 60s) — join with books
- Only **23 of 170 signals fall in the ~35h book window; 21 have valid reversal-side asks.**
- **Gate-pass (a ≤44 reversal ask EXISTS in first 60s): 7/21 = 0.333.** (First-observed-ask ≤44 basis: 3/21 = 0.143.)
- Gate-passers: winrate 0.714 (n=7, noisy), avg min-ask 0.387.
- Corroboration (broader n, all 206 book windows): a ≤44 ask exists on *some* side in first 60s in 126/206 = 0.61. Reversal-specific 0.33 is consistent with Lane 1's "30.7% of all windows have a ≤44 ask".
- **Caveat: gate-pass 0.333 rests on n=21. This is the weakest number in the lane.**

## (c) Expected trades/day & EV/day by fill policy
trades/day = 21.25 signals/day × 0.333 gate-pass × fill_rate. Fill rates + avg fill price from wave-1 Lane 1 (Δ=1s).
EV: week-wide reversal winrate **54.7% (n=170)**, fee 1.7¢/ct (P≈0.40), flat $4 ≈ 10 contracts.
EV/ct(net) = winrate·100 − fill − 1.7.

| policy | fill rate | trades/day | EV/ct (¢) | $/trade | **EV/day** |
|---|---|---|---|---|---|
| current (IOC@observed) | 0.705 | 4.99 | 13.08 | $1.31 | **$6.53** |
| gate (IOC@44) | 0.820 | 5.81 | 12.65 | $1.26 | **$7.35** |
| gate+3retry@2s | 0.885 | 6.27 | 12.42 | $1.24 | **$7.78** |

n labels: trades/day driven by n=170 (freq) × n=21 (gate) × n∈{Lane1 61-window fill sim}. EV/ct driven by n=170 winrate.

Sensitivity on winrate (gate+3retry):
- gate-passer winrate 71% (n=7, optimistic): EV/ct 29.1¢ → **EV/day $18.25**.
- coin-flip 50% (pessimistic floor): EV/ct 7.7¢ → **EV/day $4.83**.
- All three scenarios are **+EV after fees**; edge is robust in sign, uncertain in magnitude (winrate CI ±~8pp at n=170).

## (d) Overnight (00-13Z) vs Daytime (13-24Z) split
| segment | signals/day | winrate | trades/day (gate+retry) | EV/day (gate+retry) |
|---|---|---|---|---|
| overnight 00-13Z | 12.25 (n=98) | 0.531 | 3.61 | $3.89 |
| daytime 13-24Z | 9.00 (n=72) | 0.569 | 2.66 | $3.89 |

- **The "streak is a daytime strategy" claim (R134) is NOT confirmed by this reconstruction.** Signals are MORE frequent overnight (12.25 vs 9.00/day). Gate availability is similar day/night (all-window ≤44-exists: overnight 0.592 vs daytime 0.642; reversal-specific gate-pass n too thin to separate: 4/11 vs 3/10).
- The daytime advantage that exists is in **win QUALITY** (56.9% vs 53.1%), not frequency or gate-pass. Because overnight's higher count offsets daytime's higher winrate, **modeled EV/day is ~equal ($3.89 each)** — edge is NOT concentrated in daytime.
- R134's "daytime" impression came from 2 live nights where adjacent windows priced 45-71¢ (tiny live n). Over the full 35h book capture I do not reproduce a rich-overnight gate skew. Treat "daytime-only" as UNVERIFIED; do not restrict trading to daytime on current evidence.

## Verdict / gates
- CONFIRMED tradeable, +EV in all winrate scenarios. Gate = the ≤44¢ price cap (already live). No clock gate justified — the daytime restriction is not supported; keep 24h operation.
- **VPS implication:** ~40% of raw signals occur overnight (00-13Z, 98/170) and carry positive modeled EV (~$3.89/day of the ~$7.78 total). A lid-closing Mac that dies overnight forfeits roughly HALF the strategy's EV/day. This is a concrete $/day argument FOR the $5/mo VPS.
- **Week-2 sizing:** expect ~5-6 fills/day (gate/gate+retry), ~$7-8/day gross EV at flat $4 under the 54.7% base winrate. Scale sizing off ~5.8 trades/day, not the raw 21/day.

## Caveats (label everything)
- Frequency (21.25/day) and winrate (0.547) are solid: n=170 over 8 days, both series, actual settled results.
- Gate-pass (0.333) and all day/night gate splits rest on ~1 day of book coverage (n=21 joined). WEAKEST link — a fuller book capture should re-measure.
- EV/day combines week-wide winrate with Lane-1's single-day fill sim; magnitude uncertain, sign robust.
- 8 days is one regime; edges die in weeks (standing doctrine) — re-scan weekly.
