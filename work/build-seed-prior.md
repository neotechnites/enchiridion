# Lane — BUILD SEED-PRIOR (Gate #2, external word prior) — 2026-07-25

Extends verify-seed-prior (Gate #1 seed-reality) / verify-listing48h. Task: turn the
"flat 0.54/0.46 seed ignores per-word base rates" finding into a concrete, LOO-validated
external prior artifact that maps to each market at listing and fires a divergence trade.
R104: all P&L reconstructed/hypothetical, no fills.

## Corpus (fresh pull, bigger than verify lane's 492)
- `scripts/seed_prior_pull.py`: iterated all **397** `category=Mentions` series, paginated
  `/markets?series_ticker=…&limit=1000` (curl, 6 workers, JSON-key validation, *_dollars/*_fp
  discipline). The verify lane only saw 492 because it didn't paginate — full pull finds far more.
- Settled markets (result ∈ {yes,no}): **14,998**; dropped **588** "Event does not qualify"
  admin rows → **N=14,410** word-markets across **133** settled families, **1,915** distinct words.
- Realized YES = **0.4516** (6,507/14,410). Flat seed 0.54 is systematically too high (mirrors
  verify's 0.417 on the smaller sample). This 0.4516 mean-YES is the naive baseline to beat.
- Raw corpus on disk: `~/kalshi_data/seed_mentions_settled.json`.

## Artifact — `~/kalshi_data/seed_prior.json` (190 KB)
Hierarchical Beta-smoothed backoff, word/pattern → P(YES) with n + method per entry:
- `word_family_prior` (n=839 cells, key `KXSERIES||word`, n≥3): word rate **within** its family,
  shrunk to the family base rate (strength 3). Most specific.
- `word_global_prior` (n=867 words, n≥2): word rate pooled across all families, Beta(GY·4)-smoothed.
- `family_prior` (n=133 series): series base rate — this is the **unseen-word** fallback.
- `global_prior` = 0.4516 — final fallback for a brand-new family.
- Backoff at runtime: `wordfam → 0.5·wordglobal + 0.5·family → family → global`.
Example of why the hierarchy matters: `trump` word-global p=0.24 (said only 24% across all shows),
but in a Trump-topic family the wordfam cell lifts it to ~1.0. Family priors span 0.16
(KXHANNITYMENTION) to 0.76 (KXNETANYAHUMENTION) — the flat 0.54 seed fits almost none.

## LOO validation (leave-one-out, proper: held row removed from every table)
Trade rule = fade the flat seed using the prior's direction: p≥0.5 → buy YES @ 0.54; p<0.5 → buy
NO @ 0.46. EV = P(win) − price, ¢/contract. "Confident" = |p−0.50| ≥ 0.15.

| policy | dir hit | EV all | n_all | EV conf gross | EV conf net(−2¢) | n_conf | conf hit |
|---|---|---|---|---|---|---|---|
| blind YES @ seed | 0.452 | −8.84¢ | 14410 | — | — | — | — |
| blind NO @ seed (naive fade) | 0.548 | **+8.84¢** | 14410 | — | — | — | — |
| family-only (UNSEEN-word model) | 0.588 | +10.16¢ | 14410 | +22.55¢ | **+20.55¢** | 2990 | 0.697 |
| word-global only | 0.678 | +18.75¢ | 13373 | +27.15¢ | +25.15¢ | 6465 | 0.761 |
| **hierarchical (deployed)** | **0.684** | **+19.13¢** | 14410 | **+28.38¢** | **+26.38¢** | 7798 | 0.774 |

Edge vs naive fade: +19.1¢ vs +8.8¢ all-in (+10.3¢ lift); on the confident 54% subset +26.4¢ net.

## Robustness cuts
- **Exclude degenerate cells** (word×family that always/never fires, n≥3): edge only falls to
  +16.3¢ all / +24.5¢ conf (n=13,257). Edge is broad-based, not just "home run said every game."
- **Earnings family only** (the ONE family where 0.54 seed is CONFIRMED, n=791): +7.3¢ all,
  confident subset just **+5.8¢** (n=100) — fee-marginal. This is the load-bearing caveat below.

## The load-bearing caveat (unchanged from Gate #1)
The fat +19–26¢ is dominated by NON-earnings families, whose opening seed is UNVERIFIED — the flat
0.54 seed was directly observed only on earnings-grab (wave-1). If MADDOW/WNBA/etc. actually seed
near their own base rates, their fade edge is illusory. On the one family with a confirmed flat seed,
edge is thin (+5.8¢ conf). So: the **prior is real and validated** (it predicts word truth at 0.68
LOO hit); the **money edge is conditional** on flat-0.54 seeding generalizing beyond earnings — that
is Gate #1's open item, resolvable only by a sub-20-min capture at listing on ≥1 non-earnings family.

## Runtime procedure (when listing_events.jsonl shows a NEW mention family)
1. On `NEW_SERIES` (or first `BOOK_LIVE`) for a `*MENTION*`/mention-grab series `s`, confirm the seed
   is flat: every market's opening `ya≈0.54 / yb≈0.46`, mid≈0.50 (else abort — Gate #1 failed for `s`).
2. For each market, normalize its `yes_sub_title` word `w` (lowercase, collapse spaces) and look up
   `p_hat` via backoff: `seed_prior["word_family_prior"]["{s}||{w}"]` → `0.5·word_global[w] +
   0.5·family[s]` → `family[s]` → `global` (0.4516). A never-before-seen word in a known family
   correctly gets the family base rate, not the flat seed — that is the generalization.
3. `div = p_hat − 0.50`. Fire when **|div| ≥ 0.15**: `p_hat>0.5` → buy YES at the 0.54 ask;
   `p_hat<0.5` → buy NO at the 0.46 ask. Size ∝ |div| (or fixed small; R104 no fills modeled).
4. Exit when the book reprices to true value (verify-listing48h: within first 1–2h). Prereq before
   any capital: ≤1-min poll for the first 2h of a new mention series to measure the true seed-window
   duration (the 20-min capture floor can't), then paper-trade the prior on ≥30 fresh markets.

## Files
- `~/kalshi_data/seed_prior.json` — the artifact.
- `~/kalshi_data/seed_mentions_settled.json` — raw settled corpus (14,410 rows).
- `~/kalshi_data/scripts/seed_prior_pull.py`, `seed_prior_build.py` — pull + build/LOO.
