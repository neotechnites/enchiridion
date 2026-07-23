# 20 - Batch Playbook (how to run a 20-idea hunt)

> Self-contained: a fresh Claude given only this file (plus the reads in step 0) runs a batch the way it has always been run. This IS the engine of the project — edges die in weeks, so batches never stop. Written 2026-07-23 at Ryan's order.

## THE GOAL OF A BATCH
Find an edge **any way possible that leads to absurd outcomes in our favor** — Ryan's own definition of moneyball, and the identity of this project ("nestor the wise, the god-like"). Not "analyze mispricings" — that is ONE door among many. A batch exists to feed the live slate (note 18) with replacements and upgrades, because the standing doctrine is that edges decay. Current lens: the staged goal ($1k→$10k→$50k→$1k/day) means **small-bankroll capacity edges rank first** — thin markets nobody big can bother with are reserved for us.

## STEP 0 — SPIN-UP (non-negotiable, ~15 min)
Read [[18 - LIVE STATE (2026-07-23)]] (or successor) → [[19 - Pantheon Architecture & Operating Model (2026-07-23)]] → note 13's graveyard + slate → [[15 - Operating Manual (spin-up & method)]] → [[14 - Data & Infrastructure (Session 3)]] (what data exists on disk). Then `ls -t ~/kalshi_data/scripts | head -20` — harnesses and cubes already exist; reuse before building. **Never re-test the graveyard** (~120 ideas dead WITH numbers in notes 13/03) unless entering through the one legal door: a conditional kill + a new gate hypothesis (kill taxonomy, note 15).

## STEP 1 — IDEATE (the main Claude's job, ~80% of the value)
10-20 ideas per batch. **Every idea must state two things or it doesn't count:**
1. **The mechanism** — WHO is on the other side and WHY they're wrong (retail gambler's fallacy, structural settlement quirk, fee cliff, attention gap, thinness, forced flow, stale feed…). "The data shows X" without a who/why is a curve-fit waiting to happen.
2. **The cheapest decisive kill** — the one number, computable in minutes from data on disk, that would prove it dead.

**Creativity requirements (Ryan has rejected batches for lacking these):**
- Walk ALL the doors, not just mispricing: **structure** (settlement identities, averaging windows, fee cliffs), **being the house** (quoting where nobody quotes), **speed-of-attention** (feeds nobody watches at the moment they update — NOT latency racing, that's doctrine-dead), **venue mechanics** (promos, new-listing sloppiness, incentive programs), **thinness itself**, **correlation nobody prices**, **information channels nobody reads** ("pizzas to the Whitehouse means war"-class sources).
- Channel the archetypes: Thorp, Benter, Jane Street, RenTec weak-signal stacking, event-vol desks, Soros reflexivity.
- Do NOT limit to markets Ryan has mentioned; anything legal on Kalshi/Poly-US is in scope (venue rule: global-Poly = signal/research only, never a betting venue).
- A batch that's just variants of the existing slate is a failed batch ("half of your 5 are just my freaking ideas"). Never say "markets are efficient" — say "this door is closed, with these numbers."
- Interactions count: X and A may each fail alone but work as "X while A."

## STEP 2 — CHEAP KILL, INLINE (minutes per 10+ ideas)
One harness pass over the on-disk tables (cubes/touch/obs; `batch_kills*.py` in athena is the pattern). Evaluate each idea as a **flip-rate or EV delta vs its complement cell, plus a placebo cell** (a nearby non-signal slice that should show nothing — if placebo lights up, the signal is an artifact). **Predicted-direction-or-dead**: the idea stated its direction in step 1; wrong direction = dead even if "significant." No exhaustive sweeps (≤3 values per threshold — the best-of-a-sweep is a fake edge by construction).

## STEP 3 — AGENTS ONLY FOR FOREIGN DATA (~20%, optional)
Sub-agents scout what's NOT on disk (new market categories, feeds, calendars). Types `researcher` (opus/high) or `researcher-med` (opus/medium) ONLY — never Fable, never xhigh (a 5-Fable batch once burned the org's monthly cap). ≤5 concurrent. Every prompt includes: mission, pointer to `~/kalshi_data/AGENT_CONTEXT.md`, resume-from-disk instruction, cost block (cheapest decisive test first, ONE lane, no sweeps), report format. Agents stall "waiting for monitors" — kick via SendMessage ("run synchronously, deliver now"). Killed agents leave salvage: read their disk output before respawning.

## STEP 4 — VERDICTS (kill taxonomy, note 15 — MANDATORY)
`TRADE / PROMISING / CONDITIONAL(+gate) / DECAY-BENCH / DEAD`. **DEAD is legal only for structural kills** (placebo-fail, unconditional 2-yr coinflip, artifact). Works-in-a-slice = CONDITIONAL, never dead — mandatory gate-hunt (regime/trend/clock/vol-state/calendar/family), and the rescuing gate must itself clear note 07's overfit bar (named mechanism + own placebo/split). Three of the five live systems are rescued conditional kills; the taxonomy is not theory, it's the track record.

## STEP 5 — SURVIVORS → FROZEN RULE → VIRGIN SHOT
Freeze exact thresholds BEFORE the holdout. Entry realism: fresh print (age ≤30s) + 1¢, fees in (ceil-per-order `0.07·P·(1−P)`), one observation per market, n≥60. One honest split (different era, or different platform — cross-asset crypto is NOT independent, BTC/alts share regime). Then ONE shot at virgin/holdout data — never peek, never re-run "to check." Bar: **"verified enough that it won't reverse"** — Ryan will ask "did you run a backtest" and the answer must survive.

## STEP 6 — CHECK THE TRAP LIST (every survivor, every time)
Candle lookahead (lag 1-min candles 60s) · stale-quote entries · pooled cross-asset significance · regime fakes (both extremes same direction; DOWN-heavy in a bear window — demand the unconditional baseline) · one-event edges (all P&L from one print = PROMISING, not TRADE) · tick-weighting (one obs per market) · correlated-sample illusion (5 categories sharing one distortion ≠ 5 confirmations) · kbt frozen books (~48%) · KX ticker hours are EDT.

## STEP 7 — RECORD, ALWAYS (the always-update doctrine, note 19)
Every idea gets a ledger line in the session's findings note, DEAD ones included (numbers keep them dead):
`idea · mechanism · kill-test · n · win% · EV net · split result · verdict · files`
Slate changes → update note 18. New doctrine learned → its note. Ryan inputs → log (note 17). Then **git commit + push enchiridion**. A batch that isn't recorded didn't happen.

## COST DISCIPLINE (violations have had real consequences)
Cheapest test first, always. Pulls <15 min; never maximize a rate limit; if a keyed API or paid source could shortcut hours of grinding, SURFACE IT to Ryan before building the workaround. Short substantive updates as results land — no going dark, no essays, no surrender messages.
