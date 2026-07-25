# Lane 1 — STREAK-RETRY ledger
Date: 2026-07-25. Data: ~/kalshi_data/kbt_books_{btc,eth}.jsonl (104+102 = 206 window-lines; capture spans 24–25 Jul).
Verdict: **CONFIRMED — change nestor entry to limit-at-gate(44) + up-to-3 retries @2s.**

## Data decode (verified, not assumed)
Each line = one 15m window: `{ticker,K,open,close,result,coin_*,exp_val,snaps}`.
snap = `[ts, field1, field2, coin_px, n1,n2,n3,n4]`.
- field1 = **yes_ask** ($), field2 = **no_ask** ($). Proof: (i) per-snap field1+field2 median 1.08 (>1 ⇒ two asks, not two bids — two bids >1 = arb); (ii) field1 rises in result=yes windows (0.66 vs 0.57), field2 rises in result=no windows (0.68 vs 0.55); (iii) matches live-tape miss #1: no_ask 44 ⟺ yes_bid 56 ⟺ field2=0.44.
- **NO ask (cents) = field2·100.** Gate = 44.
- Plain None appears only at window ends near expiry (API quirk); **0.000 None rate inside first 60s.**

## (a) Capture granularity
- Snap cadence: **median 0.100s** (p10=p90=0.100) — clean 100ms tape.
- First-60s snaps/window: median 573, mean 570.
- ⇒ a 1–2s retry is ~10–20 snaps of resolution; fully resolvable in-band.

## (b) Flicker-and-return: P(no_ask≤44 at t+h | ≤44 at t), first 60s, n=11,000 anchor snaps
| t+h | P |
|---|---|
| +1s | 0.968 |
| +2s | 0.954 |
| +3s | 0.941 |
| +5s | 0.926 |
| +10s | 0.902 |
| +15s | 0.885 |
| +20s | 0.861 |
| +30s | 0.832 |
A ≤44 ask is highly persistent — 96.8% still ≤44 one second later, 83% at 30s. The live miss (31¢ ask "still there" 300ms later) is the norm, not luck.

## Entry universe
- Windows with a no_ask≤44 in first 60s: **61 / 199 = 30.7%.**
- First-fire ask: median 42.0¢, mean 41.1¢, min 31.0¢.

## (c) Policy sim (universe n=61; latency Δ = decision+network before IOC lands)
fill = P(fills), px = avg realized fill price.
| Δ | current (IOC@observed) | gate (IOC@44) | gate+3retry@2s |
|---|---|---|---|
| 0.3s | 0.803 / 40.33¢ | 0.902 / 40.62¢ | 0.918 / 40.64¢ |
| 0.5s | 0.738 / 40.13¢ | 0.852 / 40.50¢ | 0.885 / 40.59¢ |
| **1.0s** | **0.705 / 39.93¢** | **0.820 / 40.36¢** | **0.885 / 40.59¢** |
| 2.0s | 0.689 / 40.10¢ | 0.787 / 40.27¢ | 0.869 / 40.58¢ |

At the realistic Δ=1.0s: gate lifts fill **+11.5pp** (70.5→82.0%) over current; retries add **+6.5pp** (→88.5%). Total **+18pp fills** for **+0.66¢** avg fill price. All fills ≤44 by construction, so per-fill EV stays inside the gate.

## Realized outcomes (R104 — actual `result`, NO held to expiry, Δ=1.0s, gross of fees)
| policy | fills | NO win-rate | avg gross PnL/contract |
|---|---|---|---|
| current | 43 | 0.488 | +8.91¢ |
| gate | 50 | 0.500 | +9.64¢ |
| gate+3retry | 54 | 0.537 | +13.11¢ |
Extra fills captured by gate+retry are **not toxic** — they carry a higher NO win-rate and higher realized edge, so pricing at the gate + retrying does not degrade selection in-sample; it improves it. Kalshi taker fee ≈1.7¢/contract at P≈0.42 → all policies remain strongly +EV after fees.

## Caveats / gate
- Universe n=61, single ~1-day capture. Fill-rate mechanics (b, c) are robust (11k persistence anchors, 100ms tape). The **outcome ranking (win-rate/PnL) is n~50 with ±~14pp CI — directionally supportive, not proven.** Do not treat +13¢ as a locked edge; treat it as "retry doesn't hurt selection."
- Δ latency is assumed; measure nestor's true observe→ack latency and read the matching row. If Δ>2s the retry advantage only grows (persistence stays >0.86 to 20s).

## Recommendation (nestor code change)
Replace `IOC at observed ask` with **IOC priced at the gate (44¢) + up to 3 retries spaced 2s** on any window where a ≤44 NO ask is seen in the first 60s. Expected: fill rate 70.5%→88.5% at Δ=1s, avg fill +0.66¢, realized edge preserved/improved. Keep the 44 gate as the hard price cap so retries can never chase above it.
