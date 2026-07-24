# Night Rep — Lane B (Attention & Information doors)
Generated: 2026-07-24 04:32 UTC · Opus/medium · capped pipeline-test rep (~8 ideas)

Governing procedure: note 20 Batch Playbook. Kill taxonomy: note 15. Venue rule: Kalshi/US-Poly only.
Base instrument for the gate tests: hourly BTC/ETH ladder wing-fade (BUY NO on cheap-YES rungs px 0.03–0.25 at T-55min, entry (1−px)·100+1c slip, fee 7·px·(1−px)c, one obs/rung, result join). Harness: `scripts/laneB_attn.py`.
Note: the BTCD/ETHD hourly wing-fade is itself a marginal base (BTC +0.63c, ETH +0.43c, decaying by era) — the real wing engines are WTIH/commodity (note 13). These gate tests ask whether an unwatched attention/info feed rescues or sharpens it.

## Ledger
| # | idea | mechanism (who/why wrong) | kill-test | n | win% | EV-net | verdict | files |
|---|------|---------------------------|-----------|---|------|--------|---------|-------|
| B1 | Mempool congestion (avgFee_50) gates wing sell | on-chain congestion feed nobody prices → precedes vol; wing sellers ignore it | EV low vs high mempool tercile + hash placebo | 2157 | 89 | LOW +0.69 / HIGH +0.36 (placebo spread ±0.84) | DEAD (structural: feed degenerate — median fee=1 sat/vB, no usable dispersion; split within placebo noise) | mempool_fees.json, laneB_attn.py |
| B2 | Deribit DVOL (IV) info-channel gates wing sell | pros' IV forecast; when DVOL low, Kalshi static wing curve overpriced → fade richest | EV low vs high DVOL tercile | ~520 join | 88 | LOW −0.90 / HIGH +2.59 | DEAD-for-mechanism (INVERTED vs predicted low-DVOL direction; small join-n; likely IV-elevated-but-calm mean-reversion) | dvol_btc_1min.json |
| B3 | Cross-commodity tail contagion ("NatGas tail while Brent") | attention/energy-shock leaks across commodities the wings don't co-price | P(A wing-hit \| B wing-hit) vs base(A), energy vs metal placebo | ≤6 co-days | — | lift +0 only within same underlying (WTI\|Brent +27.8pp n=5; Silver\|Gold +44.7pp n=2); NatGas independent (−) | DEAD (structural: only same-underlying correlation, already covered by cluster-cap; n≤6 unpowered) | cwing_obs_*.jsonl |
| B4 | Funding-rate sign as forced-flow direction (near-money) | perp funding = crowded lean the ladder lags | graveyard (funding_clock) | 675 | — | pooled −2.49c, real coins negative | DEAD (note 13 graveyard — door re-walked, not re-run) | funding_clock_step1.py |
| B5 | Kimchi premium ROC as stale-attention direction | Korean retail flow leads; Kalshi ignores | graveyard (kimchi-ROC lock gate) | — | — | inverted | DEAD (note 13 graveyard) | — |
| B6 | Spot-momentum "X-while-A" gate: sell wings only when trailing \|ret60\| calm | wing sellers don't condition on live momentum; an active hour walks into the tail | EV calm vs active momentum tercile + placebo + era + ETH split | 2157 | 88.9 | pooled calm +1.21 / active −1.21 (placebo ±0.84 → clears pooled) | **CONDITIONAL** (+gate momentum-calm) — but FAILS honest split: BTC era EARLY inverts (calm +0.05/active +1.68), only BTC-LATE holds (calm +2.37/active −0.99); ETH absent (calm +0.30/active +0.19, placebo noise ±3.5c). Slice artifact, gate does not clear its own split → NOT tradeable | laneB_attn.py |
| B7 | Reflexivity: contract-price velocity (T-30→T-15) predicts settlement beyond spot | flow into the contract = information | graveyard (MM-unwind) | 12564 | — | 100% regression-to-50, corr(flow)=−0.008 | DEAD (note 13 graveyard) | mm_unwind_test.py |
| B9 | Gold-token (PAXG) risk-off cross-asset gate on BTC wings | gold rally = risk-off = BTC downside tail; crypto wings don't price the gold feed | EV low vs high paxg_ret60 tercile + ETH split | 2157 | 88.9 | pooled paxg-down +2.21 / paxg-up −0.64 | CONDITIONAL but collinear with B6 (a paxg move ⇄ a BTC move) and absent on ETH; same slice, not independent | laneB_attn.py |

## Survivors (nothing tradeable; frozen numbers on the least-dead)
No door cleared the bar. Two are CONDITIONAL (never DEAD per taxonomy), both underpowered/unstable:

**B6 — momentum-calm gate on hourly wing-fade** (single most promising)
- Pooled BTC: calm-tercile |ret60|≤0.104% → EV +1.21c (n=720, win 90.6%); active-tercile ≥0.237% → EV −1.21c (n=719, win 85.3%). Monotone, right direction, clears the hash-placebo (±0.84c).
- FAILS its honest split: BTC era EARLY inverts (calm +0.05 / active +1.68); only BTC-LATE carries it (calm +2.37 / active −0.99). ETH shows no gate (calm +0.30 / active +0.19) and its per-split placebo noise is ±3.5c at n=961 — too small to trust.
- Disposition: CONDITIONAL, gate does NOT yet clear note-07's own-split bar → DECAY-BENCH-adjacent. Re-test only with (a) the WTIH/commodity wing base (deeper, the real engine) where n and depth are larger, and (b) a same-asset momentum definition, before any weight.

**B9 — PAXG risk-off gate**: same slice as B6, collinear, ETH-absent. Bench with B6.

## Notes for the slate
- The BTC/ETH hourly ladder wing-fade base is marginal and era-decaying (+0.63c → +0.13c late) — consistent with the honeymoon/decay doctrine; don't build attention gates on it, build them on WTIH/commodity.
- Placebo calibration learned: at n≈1–2k a pure hash split already spreads ±0.8c (BTC) to ±3.5c (ETH, n≈960) — the per-split noise floor. Any gate claiming <~1c of edge on these samples is noise.
- B3 reinforces the cluster-cap: crude pair (WTI≈Brent) and precious-metal pair (Gold≈Silver) each = one bet; NatGas is independent of both.
