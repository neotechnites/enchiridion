# Charter: lip_v5 — the from-first-principles rewrite (Ryan-ordered 2026-07-28 ~8:15am MT)

Status: CHARTER → spec (adversarial pipeline) → build → STAGED-INERT. v5 deploys ONLY on Ryan's
explicit word. v4 is FROZEN (ws parser fix excepted) and keeps running; v5 is not v4-plus-patches —
it is re-derived from the objective with v3/v4's lessons as measured inputs, reusing any v4 code
that survives re-derivation on its merits.

## The objective (the only one)
Maximize Σ over (market, side, second): resting_dollars × proximity_weight(0.5^ticks) × pool_rate,
NET of: fill costs (drift + fees≈0 maker) + inventory carry (capital × time-to-liquidity, priced!)
+ operational risk — subject to Ryan's bankroll envelope and program-revocation risk.
Ryan's law, now axiom 1: **earning = capital × time × proximity, in the RESTING state.** Every
design decision must name which term it grows or which cost it cuts.

## The PayPal axiom (the $16 lesson, 2026-07-28 — the rewrite's reason)
KXEARNINGSMENTIONPYPL-PERP: ~$16 of fills in hours, accrued rewards ≈ $0, market close DEC 31,
mark loss large. Mechanism: **a market that fills you fast pays you in risk, not rewards** —
at-best quotes that get taken immediately have near-zero presence-seconds (filled ≠ resting), so
capital converts to inventory exactly where accrual is thinnest; and a LONG-DATED market has no
settlement to liquidate the mistake, so carry cost ≈ capital × months. Treasuries/gas worked for
the mirror reasons: quiet books (high presence-seconds/dollar), same-day settlement (carry ≈ hours).
Derived requirements:
1. **Liquidity horizon is a first-class venue input.** Time-to-certain-liquidity (settlement or
   early-determination) discounts every venue: dailies (hours) ≥ weeklies >> open-ended mention/
   event markets (months). Long-horizon venues need order-of-magnitude better reward evidence,
   tiny caps, or exclusion. No cap may assume settlement bails it out unless settlement is near.
2. **Toxicity is measured, not assumed: presence-seconds-per-dollar-hour per (market, side)** is
   THE health metric, computed from our own tape (order resting intervals vs fills). Venues where
   fills/hour × horizon dominates accrual-rate get sized down or killed automatically. (P6's
   revealed-usefulness inverted: heavy taker flow against us = WE are the fish there.)
3. **Verified accrual before size.** A venue's modeled earning is a hypothesis until the exchange
   confirms it (popover estimate read, or paid credit). Size ratchets UP only on verified accrual;
   entry size everywhere is probe-sized. (PYPL got $16 of exposure on zero verified accrual.)

## What v4 proved and v5 keeps (re-derive, but these survived adversarial fire)
Dollar-denominated resting-aware net caps · closing-room netting + closing exemptions (collateral,
refill, halt-gate) · window guards BOTH ends + runway + program-period floors ($1 forfeit) ·
make-before-break with reserve + ceiling degrade · settlement release + position reconciliation
vs exchange (arm the freeze with the 3 documented guards from day one) · ledger-replay restart
with normalized fill vocabulary + schema-mismatch abort · staged human gates for spending paths
(taker-exit class) · detect-and-page defaults · the W2 trust gate pattern for any new data source
· fail-loud-never-confident-nonsense.

## What v5 derives fresh (the failures no patch fixed)
1. **Accounting that tracks reality at velocity.** v4+nestor share one account through a hand-
   patched external-cash band that halted nestor FOUR times in 24h. v5 owns its cash accounting as
   a computed feed (its ledger IS the source; publish expected-cash deltas to a file nestor's
   breaker reads), and is subaccount-ready day one. Zero hand entries in steady state.
2. **Rate-budget as a scheduled resource.** The 429 starvation of streak: v5 gets an explicit
   request budget (shared-account aware), WS-first with REST strictly for verification/fallback,
   and degrades breadth before it degrades another bot's calls.
3. **Venue portfolio construction from measured curves**: pool-rate ÷ competition × horizon
   discount × measured toxicity × verified-accrual multiplier — replacing v4's rank heuristics.
   Dose-response sizing built in (vary size across rungs to keep measuring the share curve).
4. **Presence-first quoting**: cadence/at-best maintenance designed around snapshot survival
   (rest whole seconds; the WS book drives re-centering), with per-venue quote shading by
   measured adverse-selection, not uniform join-best.
5. **The estimate reader as a sensor**: whatever surface exposes per-rung estimates (endpoint or
   browser automation) feeds the daily calibration loop; model error >2× for 2 days on a venue =
   that venue stands down (not the whole bot).
6. **Operational simplicity debt paid**: one config file, constants each carrying derivation +
   mirror-question answer (note 23 Part IV), staged-deploy commands as separate human-gated calls
   (R186), no shared-tree builds (worktree only), NTFY_DISABLE honored everywhere by construction.

## Process
Spec author writes spec-lip-v5.md from this charter (same §-structure discipline as v1: every
constant derived, test plan for money rules as pure functions, open questions with safe-under-
either-answer defaults). Adversarial review; build in an isolated worktree; full suite; staged.
v4 keeps earning untouched throughout. Ryan flips the switch when he chooses; cutover plan (state
handoff: v4 SIGTERM cancel-all + settlement of its inventory OR ledger import — spec must design
it) is part of the spec, not an afterthought.
