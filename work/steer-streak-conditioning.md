# STEER: Streak conditioning batch (2026-07-26, Ryan-ordered) — three cells, real backtests

> Ryan's go: "dont change nestor, but please go down these paths of research with real
> backtesting to see if they have merit." NO code changes to nestor — findings go to
> ledger + Fable. All three cells condition the SAME population (15m crypto windows /
> streak fades), so load the data once and run all three.

## The three cells (each: fees in, placebo where applicable, honest split, n reported)
1. **UNCONDITIONAL CHEAP-SIDE CALIBRATION.** Hypothesis (Ryan's): buy ANY side priced
   ≤44¢ in the first minute of a 15m window, no streak required. Prior expectation from
   the vault: DEAD — Kalshi is calibrated unconditionally (favorite-buying dead n≈4,700;
   Brier 0.024 vs Deribit) — but first-minute-specific calibration has never been
   measured directly. Deliverable: win rate vs implied price by price bucket (≤25, 25-35,
   35-44) unconditional, and the same table conditional on 4-streak (the strategy's own
   population) as the contrast. If unconditional buckets beat their breakeven, that is a
   BIGGER strategy than streak — flag loudly.
2. **BTC-CONTEXT GATE.** Hypothesis: an ETH streak driven by a concurrent BTC move is
   information (fade loses); an idiosyncratic streak is fish (fade wins). Condition fade
   outcomes on |BTC move| (and sign agreement) over the streak's window span at signal
   time. KNOWN TRAP (note 15): the original "BTC-confirm gate" died as a stale-quote
   artifact — use spot/candle series with proper lagging (no lookahead: only data known
   at entry), never quotes. Placebo: shuffle BTC windows against ETH streaks; the gate
   must die on shuffled data. Symmetric test: ETH-context for BTC streaks. Mechanism is
   named; if the split is real, report the gate threshold + win rates both sides + what
   fraction of signals it removes.
3. **WIN RATE BY STREAK LENGTH.** Does reversal probability stay ~52% at length 5, 6,
   7+ or decay? Deliverable: reversal rate + n by streak length (4,5,6,7,8+), both
   platforms if data allows. Decides whether a bow-out-at-N rule adds edge (decay) or
   burns it (flat). Note expected n collapse at long lengths — report CIs, don't
   overclaim either direction (note 07).

## Data (all on-disk, Mac copies current through 2026-07-26 ~22:00Z cutover)
- ~/kalshi_data/ — the full archive: Kalshi 15m settled results (66d corpus, 6,229 mkts),
  the Polymarket 189d corpus (49,720 mkts — the origin streak backtest's data; find it,
  `ls`/grep before re-pulling ANYTHING), kbt 100ms books, obs logs, spot captures.
- Resume-from-disk discipline: `ls -t ~/kalshi_data/scripts | head -30` — the origin
  streak backtest scripts exist; reuse their loaders/streak reconstruction rather than
  rewriting (and cite which script you reused).
- BTC/ETH spot for cell 2: derive-sampler captures + any candle pulls on disk; if a
  gap forces an external pull, free public sources only, and say so.

## Discipline
- Note 07 (overfitting): every rescuing gate needs mechanism + placebo + split. Cross-
  asset is NOT independent (BTC/alts share regime — note 03): report per-asset.
- Money-impact claims by reconstruction only; label UNVERIFIED anything inferred.
- Traps list (note 15) applies: candle lookahead (lag 60s), stale quotes, pooled
  significance, regime fakes (demand unconditional baselines), one-event edges.
- No nestor changes. No subagents. Cheapest decisive pass per cell, in the order above
  (cell 1 is the cheapest and its loader feeds 2 and 3).

## Report → work/verify-streak-conditioning.md (commit to enchiridion, no push)
Per cell: verdict (TRADE-shaped / gate-worthy / DEAD-with-numbers) + the table + n +
split + what would change nestor IF adopted (parameters only — adoption is Fable+Ryan's
call). Brief, numbers first.
