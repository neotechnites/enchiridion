# HOUSE live-probe PROTOCOL (design only — NO execution) — 2026-07-24 burst-2

> The ONE thing trade-print markout cannot answer: **does a two-sided resting quote actually
> get FILLED at the assumed spread, and does the mid gap through it between prints?** Efficacy
> (non-adverse-selection) is PROVEN from data (KXAPRPOTUS + KXCPIYOY markout ≤0, adv%<33%).
> This probe measures ONLY fill realization + between-print gap risk. Per note 18 doctrine:
> live testing is for MECHANICS, never efficacy; 2-3 day fill-probe in the book, tiny size.

## Vehicle
KXAPRPOTUS current front weekly, single in-band strike (0.30-0.70¢ zone, deepest churn).
Fallback / second book: KXCPIYOY nearest-print "Exactly" rung in 0.10-0.90 band.

## Pre-conditions (gates, from the adversarial test)
1. **Spread gate:** only post when the resting book spread ≥ 2¢ (median). At 1¢ spread the
   edge collapses to +0.10¢/fill (< noise) — do NOT quote inside a 1¢ market.
2. **Catalyst gate:** pull all quotes from T-15min to T+15min around scheduled poll releases
   (RCP update cadence) / the CPI print. (Top-1% of ticker-hour buckets hold 41% of adverse
   mass — this window is where it lives.)

## Protocol
- Post two-sided IOC-ineligible resting limit: bid at mid−1¢, ask at mid+1¢, size = 1-5 contracts.
- Re-quote on: (a) mid move ≥1¢, (b) own fill (immediately re-post the other side to flatten), (c) 60s staleness.
- Run 2-3 trading days, daytime US hours (churn concentrated there).
- Hard stop: −$20 cumulative, or any single fill marked out worse than −5¢ within 60s (gap-through detector).

## Metrics to record (the deliverable)
1. **Fill rate:** fills / quote-minutes at each side. Target ≥ a handful/hr to be worth capital.
2. **Realized half-spread capture:** avg (exit − entry) on round-trips vs the assumed 1¢.
3. **Gap-through frequency:** fraction of fills where mid moved ≥3¢ against within 60s with NO
   chance to re-quote (the between-print risk trade-data can't see). This is the kill number:
   if gap-throughs eat the spread, the trade-print markout was optimistic.
4. **Adverse-fill timing:** do bad fills cluster at the catalyst windows the gate should have caught?

## Kill / promote
- PROMOTE to TRADE if: fill rate ≥ target AND realized half-spread ≥ 0.6¢ net of fees AND
  gap-through loss < spread income over the window.
- KILL (to DECAY-BENCH or re-gate) if: fills only arrive when the mid is ABOUT to gap (i.e.
  every fill is a pickoff the trade-average hid) → then the poll/econ book IS adversely selected
  at the quote level and burst-1/burst-2 markout was a trade-frequency artifact.

## Blocked on
Kalshi trading API keys (Ryan) — same blocker as the whole live slate. Zero build until keys exist;
this file is the ready-to-run ticket.
