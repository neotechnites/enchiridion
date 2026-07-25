# Verify: DOGE MAX-ladder implied-vol ramp (V2) — multi-issuance gate

Date: 2026-07-25. Task: run the named gate (MULTI-ISSUANCE realized backtest) on the
prior finding R115 — "DOGE max-price ladder implied vol ramps pathologically (0.46→1.55
vs smooth BTC/ETH smiles), buy NO on top rungs ≈ +21¢ net, placebo-clean via relative-ivol,
capacity $50-150." Data: public Kalshi API (elections.kalshi.com/trade-api/v2); all
money figures reconstructed from records (R104). Underlying spot from Binance daily klines
(data-api.binance.vision, DOGE/BTC/ETH USDT).

## Series discovery (crypto series catalog)
DOGE MAX family: KXDOGEMAXY (yearly), KXDOGEMAXM (monthly, empty), KXDOGEMAXMON (monthly
one-touch), KXDOGEMAXW (weekly), KXDOGEMAX1/69/125 (fixed-threshold).
- **KXDOGEMAXY-DOGE-26DEC31**: the ONLY live yearly issuance, opened 2026-07-22, closes
  2027-01-01, 8 rungs (008–015), all `active`/unsettled. This is the SAME single issuance
  R115 was built on — it cannot be its own multi-issuance test.
- Historical yearly/weekly issuances: markets purged from API (event shells return 0 nested
  markets). Not reconstructable.
- **KXDOGEMAXMON**: 2 SETTLED issuances retrievable → **KXDOGEMAXMON-DOGE-26MAY31** and
  **KXDOGEMAXMON-DOGE-26JUN30**. These are the multi-issuance vehicle (monthly re-issue = V8).
- Controls with settled data: **KXBTCMAXMON** (May,Jun,Jul), **KXETHMAXMON** (May,Jun),
  KXSOLMAXMON (May,Jun,Jul).

Ladder rule (rules_primary): "if DOGE after issuance … is ever above $K → Yes" (one-touch,
CF DOGEUSD_RTI trimmed-mean minute checks). NO wins iff threshold never touched.

## Reconstruction — DOGE monthly top-rung NO rule (x ≥ 1.6× issuance spot)
Spot at issuance: May-01 ≈ $0.107, Jun-01 ≈ $0.101. Realized monthly max (Binance):
May ≈ $0.1186 (peak 05-14), Jun ≈ $0.1016 (peak 06-01). Both months DOGE flat-to-down →
every rung ≥ $0.12 (May) / ≥ $0.11 (Jun) settled NO. Confirmed against API `result` field.

Entry = first-hour (rich window) VWAP of YES prints; NO entry = 1 − YES; fee =
ceil(0.07·p(1−p)) per contract (≈1¢ at these prices). All 4 top-rung NO bets WON (settled NO).

| issuance | rung (x)      | 1st-hr YES mean | NO entry | 1st-hr vol (contracts) | settle | net/contract |
|----------|---------------|-----------------|----------|------------------------|--------|--------------|
| MAY-31   | $0.17 (1.59×) | 0.480           | 0.520    | 4.9                    | NO ✓   | +0.460       |
| MAY-31   | $0.18 (1.68×) | 0.283           | 0.717    | 8.2                    | NO ✓   | +0.263       |
| JUN-30   | $0.16 (1.58×) | 0.539           | 0.461    | 6.5                    | NO ✓   | +0.519       |
| JUN-30   | $0.17 (1.68×) | 0.197           | 0.803    | 8.6                    | NO ✓   | +0.177       |

Per-issuance (contract-weighted, first-hour entry):
- **MAY-31: n=2 rungs, ~13.1 contracts, +$4.43, avg +0.337/contract.**
- **JUN-30: n=2 rungs, ~15.1 contracts, +$4.88, avg +0.324/contract.**

Edge decays fast: entering at the FULL-DAY VWAP instead (most volume trades AFTER YES
collapses to ~0.03–0.09) gives NO entry 0.88–0.97 → net only **+0.03 to +0.11/contract**.
The rich window is ~1 hour; capacity there is ~5–9 contracts/rung (~$5/issuance), well
BELOW the claimed $50–150.

## Control test (kills the placebo-clean / DOGE-specific claim)
Same first-hour method on BTC/ETH monthly max, same 2 months (all coins flat/down → all
OTM rungs settled NO):
- **BTC**: ladder tops out ~1.28× spot (no 1.6× rungs listed), but lower OTM rungs also
  print rich early YES that settle NO — e.g. May $85k (1.11×) YES 0.35→NO wins; Jun $77.5k
  (1.05×) YES 0.75 vol 903 →NO wins.
- **ETH Jun-30**: HAS ≥1.6× rungs WITH trades — $3250 (1.62×) YES 0.071, **$3500 (1.74×)
  YES 0.419 →NO wins +0.41**, $3750 (1.87×) YES 0.151 →NO wins. Same rich-top-rung-settles-NO
  pattern as DOGE.

→ The "buy NO on overpriced top rung at issuance" edge is **NOT DOGE-specific**; ETH (and
BTC at lower moneyness) show the identical pattern in the same window. R115's central pillar
— "placebo-clean via relative-ivol, DOGE pathological vs smooth BTC/ETH" — is CONTRADICTED
by realized early-trade data. The unifying cause is a market-regime artifact: May+June 2026
were quiet/down for all crypto, so every OTM rung on every coin settled NO, and thin naive
early flow left some top rungs with rich YES prints a NO-seller could pick off.

## Verdict on the gate
- Ramp/overpricing **PERSISTS across issuances** — NOT a one-issuance artifact. 2 settled
  DOGE monthly issuances both show rich early top-rung YES, both directionally profitable
  (all 4 NO bets won, +0.18 to +0.52/contract net, first hour).
- BUT the multi-issuance test DEMOTES the thesis on three counts: (1) effect not DOGE-specific
  (ETH/BTC replicate it → placebo claim falsified); (2) n=2 months, BOTH quiet/down — the
  fat-tail scenario (coin rallies, top rung touches YES, NO loses ~−0.5 to −0.9/contract)
  is UNSAMPLED, so "overpriced" cannot be distinguished from "correctly-priced fat-tail that
  didn't fire"; (3) capacity ~$5/issuance, first-hour-only.
- **Status: stays PROMISING, does NOT promote to TRADE.** Real but marginal, regime-conditional,
  trivial capacity, and the distinguishing (DOGE-specific) claim did not survive controls.
  Would need issuances that include a crypto RALLY month to test the losing tail before any
  size. The live KXDOGEMAXY-DOGE-26DEC31 yearly is issuance #3 (unsettled) — capture it.

## Files / evidence
- API: /markets?series_ticker=KXDOGEMAXMON&status=settled (2 settled events, 15 markets);
  /series/KXDOGEMAXMON/markets/{ticker}/candlesticks (first-hour YES prints).
- Controls: KXBTCMAXMON (3 events), KXETHMAXMON (2 events).
- Spot: Binance data-api.binance.vision klines, DOGE/BTC/ETHUSDT daily, Apr–Jul 2026.
- Local ~/kalshi_data/kbt_books_doge.jsonl is KXDOGE15M (15-min price ladder), NOT the MAX
  ladder — does not cover this series; not used.
- API quirk confirmed: plain `status` on events reads None; `*_dollars`/`*_fp` fields live;
  `status=active` filter on /markets is buggy (returns 0 where default returns rows) but
  `status=settled` filter works.
