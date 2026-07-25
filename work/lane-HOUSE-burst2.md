# Lane: THE HOUSE — Burst 2 (2026-07-24)

Archetype: THE HOUSE — quote where NOT adversely selected. Burst-2 steer ordered an
ADVERSARIAL SELF-TEST of burst-1's H1 (markout≈0 on KXAPRPOTUS), reconciliation with
BEZOS B3 (far-dated house), and a live-probe protocol. Directive: "if the adversarial
test fails, kill your own survivors honestly."

Spin-up done (18→21→23→20→32→34→33). Graveyard: maker-death proven ONLY on crypto (3×
fast-BRTI). Burst-1 H1 = first proven exception (slow poll-average underlying). No spawn
tool available to me → family-generalization run myself via bounded API pull (KXCPIYOY).

## THE ADVERSARIAL SELF-TEST (the decisive burst-2 work) — H1 SURVIVES and HARDENS
`house_adversarial.py` on 35,335 in-band KXAPRPOTUS fills. Challenge: a ~0 MEAN markout can
hide fat adverse tails on news jumps a symmetric maker can't cover. Findings:
- **Distribution is SYMMETRIC** (p1=−19¢, p99=+20¢) — fat tails BOTH ways = mean-reversion,
  NOT one-sided adverse continuation (crypto's tails are one-sided adverse). Mean −0.036¢.
- **Naive symmetric maker net = +0.60¢/fill** at median 2¢ spread, fees in, WITHOUT any gate.
- **Jump split** (|300s move|≥3¢): calm fills (71% vol) markout −0.099¢; jump fills (29% vol)
  only +0.116¢ — jumps are NOT catastrophic because the underlying mean-reverts (a jump-through
  is ~as likely to revert as continue). Maker net stays positive even quoting through jumps.
- **Avoidability:** top-1% of (ticker,hour) buckets hold **41% of all adverse mass** → adverse
  fills sit in a few catalyst hours (scheduled poll drops) = AVOIDABLE by a quote-pull gate.
- **Load-bearing risk = the spread.** Spread proxy p25=1¢/p50=2¢/p75=4¢. At 1¢ spread net
  collapses to +0.10¢/fill (< noise) → the edge REQUIRES a "quote only when spread≥2¢" gate.

## FAMILY GENERALIZATION (turns a one-series quirk into a door) — CONFIRMED
Pulled 20,585 KXCPIYOY trades (34 settled CPI "Exactly X%" point-ladders), ran identical markout:
- **In-band markout −0.91 to −0.98¢ at ALL horizons; adv% 24-28%** (taker systematically WRONG).
- Same 2¢ median spread. **Naive maker net +1.47 to +1.54¢/fill** — RICHER than approval.
- Mechanism: retail overpays specific CPI points (Mesh THORP note: uniform ask ignores the peaked
  realized distribution → far points overpriced); the maker selling them collects spread AND the
  overpricing. → Two independent slow-underlying series both show markout ≤0. Door, not quirk.

## Ledger

| # | idea | mechanism / fish | kill-test | numbers (n, EV, split) | verdict | files |
|---|------|------------------|-----------|------------------------|---------|-------|
| H9 | **Deployable gated political house** (= tradeable H1): two-sided quote KXAPRPOTUS in-band ONLY when spread≥2¢ AND outside catalyst windows | Fish: retail crossing a wide poll-average book in dead-time between scheduled poll drops; underlying mean-reverts so a jump-through reverts into profit. Placebo = crypto's one-sided adverse markout. | Adversarial markout + jump-decomposition + avoidability (does adverse mass cluster at avoidable catalysts?) | n=35,335 in-band fills; naive net **+0.52/+0.60/+0.43¢/fill** (60/300/1800s), fees in; era-split held (burst-1); adverse mass 41% in top-1% hours (gate-avoidable); spread-gate required (1¢→+0.10¢) | **PROMISING** (gate to TRADE = live fill-probe, `work/probe-house.md`) | house_adversarial.py |
| H10 | **Econ point-ladder house** (NEW door): sell-side quote the discrete "Exactly X%" econ ladders (KXCPIYOY family) where retail overpays specific points | Fish: point-lottery retail buying specific CPI outcomes; the SELL maker collects spread + the overpricing on far points. Distinct from H9 (directional poll churn) — this is discrete-distribution overpricing. | Same markout: is the taker right (maker adverse) or wrong? | n=20,585 trades / 34 ladders; markout **−0.91 to −0.98¢** all horizons (taker wrong); adv% 24-28%; naive net **+1.47 to +1.54¢/fill**; 2¢ spread | **PROMISING** (strongest burst-2 find; gates: fill-probe + thin/~monthly capacity; must quote SELL side of overpriced far points) | house_adversarial.py (markout fn), cpi_trades_all.json.gz |
| H11 | **Unified slow-underlying house thesis + B3 far-dated arm** (reconciliation) | ONE thesis: passive quoting is not adversely selected wherever the underlying is SLOW / mean-reverting / discrete-settle and info is PUBLIC+LUMPY. Taker-only = a fast-BRTI artifact. H9 (near-dated poll) + H10 (econ ladder) proven on-disk; B3 (far-dated >30d) = same door, MORE dead-time, THINNER flow. | Two proven arms on disk; far-dated arm needs book history (none on disk) | 2 independent series confirm (markout ≤0, adv%<33%, both 2¢ spread) | **CONDITIONAL-research** (far-dated B3 arm capture-gated: far-dated political/econ book snapshots + post-print mid-drift) | — |
| H12 | **Catalyst-avoidance quote-pull overlay** (pure upside on H9/H10) | Fish: n/a — risk-avoidance. Pull all quotes T±15min around scheduled poll releases / prints; the discrete adverse jumps live there. | Avoidability test (done) | top-1% (ticker,hour) buckets = 41% of adverse mass at ~1% quoting-time cost → removing them is near-free EV | **PROVEN overlay** (folds into H9/H10; no separate deploy) | house_adversarial.py |
| H13 | **Inventory mean-reversion carry** (2nd income on top of spread) | Fish: momentum takers push price off the mean; maker who leans against earns spread AND reversion drift on held inventory | Does post-fill inventory mark FAVORABLY beyond spread, by family? | KXCPIYOY: favorable at all horizons (−0.98¢ = taker wrong = inventory drifts to maker). KXAPRPOTUS: favorable ≤300s but +0.137¢ ADVERSE at 1800s (poll-average carry decays) | **CONDITIONAL(+gate: discrete-ladder family only)** — carry is durable for econ point-ladders, spread-only for poll-average; do NOT hold poll inventory >5min | house_adversarial.py |
| H14 | Live-probe protocol (deliverable, not a bet) | The one unknown trade-data can't answer: real fill realization + between-print gap-through | — | Design only, no execution; blocked on Kalshi keys | **BUILD-TICKET** | work/probe-house.md |

## Verdict summary
- **2 PROMISING** (H9 gated political house +0.60¢/fill; H10 econ point-ladder house +1.5¢/fill — a NEW door, richest of the two). Both era/family-robust, both gate to TRADE only on the live fill-probe.
- **1 CONDITIONAL-research** (H11 far-dated B3 arm, capture-gated).
- **1 PROVEN overlay** (H12 catalyst-avoidance, 41% adverse mass at 1% cost — folds into H9/H10).
- **1 CONDITIONAL+gate** (H13 inventory carry, discrete-ladder-family only).
- **1 BUILD-TICKET** (H14 probe protocol, ready when keys exist).
- **Adversarial verdict: H1 did NOT fail — it hardened and GENERALIZED.** The taker-only doctrine is confirmed fast-underlying-specific; the slow/discrete-settle family is the house's native habitat.

## Net-new HOUSE facts for the Mesh
1. **The house door generalizes across the slow-underlying family, not just KXAPRPOTUS.** Two
   independent series (poll-average KXAPRPOTUS + econ point-ladder KXCPIYOY) both show passive
   markout ≤0 (taker wrong, adv% 24-33%), both 2¢ spread, naive maker net +0.6¢ (poll) / +1.5¢
   (econ ladder). Econ point-ladders are RICHER because retail overpays discrete points.
2. **Adverse selection on slow underlyings is time-clustered at scheduled public catalysts**
   (41% of adverse mass in top-1% of ticker-hours) → avoidable by a T±15min quote-pull gate;
   catalyst-avoidance is a near-free EV overlay, not a cost.
3. **Spread is the load-bearing gate:** quote only where resting spread ≥2¢; at 1¢ the edge
   (+0.10¢/fill) drowns in fees/noise. The house rule is book-selection, not universal quoting.

## Capture-demand surfaced (note 32 §5)
- **H11 → far-dated (>30d) political/econ book-depth snapshots + post-print mid-drift** — unblocks the far-dated house arm AND BEZOS B3/B8. Same daemon serves both lanes.
- **H9/H10 → live KXAPRPOTUS + KXCPIYOY book capture** — the fill-probe input (`work/probe-house.md`); the ONE thing trade prints can't answer.
