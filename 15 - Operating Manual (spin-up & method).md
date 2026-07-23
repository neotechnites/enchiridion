# 15 - Operating Manual (spin-up & method)

> For a fresh Claude: read [[00 - START HERE]] → [[13 - Session 3 Findings (2026-07-22)]] → [[14 - Data & Infrastructure (Session 3)]] → this. Then you're live. This doc encodes HOW Ryan wants the work run — it was learned the hard way in Session 3 (2026-07-22).

## The mandate
- **North star: "where is an edge" — a big one.** Never say unlikely/impossible; be the helper, not the limiter. Don't restrict to markets/ideas Ryan has mentioned — anything on any venue is in scope (he explicitly asked "should we be a market maker? I don't know — don't limit yourself").
- **THE REAL GOAL (Ryan, 2026-07-23, definitive): STAGED, not flat-10%-forever.** $1k → $10k (capacity edges are HUGE relative to $1k: gas/vol-book/streak/mention ≈ 5-15%/day at this scale; ~4-8 wks) → $50k (blended 4-7%/day; ~6-10 wks) → **steady $1k/day at $50k (2%/day — the most defensible number in the project) = a few hundred $k/yr extra income**, optionally grown into a business later. 10%/day is only needed at the small-bankroll stage, where it's easiest. Blockers are operational, not research: (1) Kalshi API keys (Ryan-only, Settings→API Keys), (2) ~2 wks tiny-size live fill validation, (3) the $5/mo VPS (machines currently die when the Mac lid closes — see note 13 overnight section).
- **MONEYBALL IS THE IDENTITY, and Ryan defined it himself (2026-07-22, verbatim intent): "HOW CAN WE GET AN EDGE ANY WAY POSSIBLE THAT LEADS TO ABSURD OUTCOMES IN OUR FAVOR." That's nestor — nestor the wise, the god-like.** Not one analytical lens, not a question template — TOTAL ruthless pragmatism, like Beane winning with $40M against $120M by walking through whatever door was open. Mispricing analysis is ONE door. Others that count equally: being the house (maker/parlay quoting where no one else quotes), speed (watching a feed no one watches at the moment it updates), structure (settlement identities, fee cliffs, averaging filters), venue mechanics (promos, incentive programs, new-listing sloppiness), thinness itself (markets too small for anyone else to bother = reserved for small bankrolls), correlation nobody prices, information channels nobody reads. If it's legal on Kalshi/Poly-US and produces asymmetric outcomes, it's in scope. "What variable is the crowd pricing vs what settles" remains a useful GENERATOR; it is not the definition. Supporting archetypes: Thorp, Jane Street, Alameda, RenTec, Soros, event-vol desks, Benter. "Pizzas to the Whitehouse means war"-class source creativity.
- **Run ideas at blistering pace.** Idea → cheapest decisive kill-test (minutes, inline, on the harness) → only survivors get deeper data. ~45 ideas were funneled in one night this way.
- The only acceptable strategy report: **"Here's the strategy, the rule in plain English, and the backtest."** Fees in, real entry prices, one honest period split. That's the bar ("verified enough that it won't reverse") — NOT the old 6-point fortress, but never below real-prices+fees+split, because he WILL ask "did you run a backtest" and it must not fall over.

## Ryan's hard rules (violations have made him furious)
1. A question is a question — answer it, then stop. Never treat questions as instructions to act.
2. No hedging/throat-clearing ever ("to be honest", "I have to be straight", "these markets are efficient").
3. Brief. Paragraph, not essay — except actual deliverables (strategy slates, backtests).
4. Never ask what you can look up. Never ask him to narrow the hunt.
5. Don't go dark for hours; short substantive updates as things land. But no surrender messages and no non-result essays.
6. If a data source/option exists that he doesn't know about (keyed APIs etc.), SURFACE IT before grinding an expensive workaround. (The multi-hour pull he ordered killed → "why didn't you flag this.")
7. **Fees:** extremes (90–99¢) are the cheap zone (fee = 0.07·P·(1−P), max 1.75¢ at 50¢, ~0 at wings). He mis-stated once and corrected: favor the ENDS of odds. His dream shape: "when X condition, it resolves this way 99% of the time, priced 95" + high frequency + a natural market rule baked in.
8. **VENUES (hard rule, 2026-07-22):** strategies may place bets ONLY on Kalshi or US Polymarket. Global Polymarket is allowed as a research lab, signal source, and arb reference (betting Kalshi off a global-Poly signal is fine) — but never as a betting venue.

## Cost discipline (Session-3 lesson: 5 Fable sub-agents burned the org's monthly spend cap)
- **Sub-agents: ALWAYS the `researcher` agent type** (`~/.claude/agents/researcher.md` — Opus, high effort). Never Fable, never xhigh. Main loop stays Fable.
- Sub-agent = foreign-data scout (new market categories, feeds, calendars), ~20% of the work. Each prompt must include: mission, data map + machine quirks, RESUME-from-disk instruction (`ls -t ~/kalshi_data/scripts | head -20`), cost block (cheapest decisive test first, ONE strategy per lane, no sweeps), and the report format (rule / mechanism / n / win% / EV net / split / verdict TRADE-PROMISING-DEAD / file paths).
- Everything harness-shaped runs INLINE in the main loop via `batch_kills*.py`-style multi-idea passes — minutes per batch of 10+ ideas.
- Killed agents leave salvage: read their result JSONs and scripts off disk before re-spawning anything.

## The funnel (the working method)
> **Executable version: [[20 - Batch Playbook (how to run a 20-idea hunt)]]** — the self-contained ritual a fresh Claude runs; this section is the summary.
1. **Ideate in batches** (10-20, mechanism-first — each idea names WHY retail/structure misprices it, and its cheap kill).
2. **Cheap kill inline**: one harness pass over the cubes/touch/obs tables. Gates evaluated as flip-rate/EV deltas vs complement + placebo cells. Predicted-direction-or-dead.
3. **Survivors → frozen rule** (exact thresholds, entry realism: fresh print + 1¢, fee, one obs/market) → fit numbers → **one-shot virgin/holdout run** (never peek early).
4. Cross-asset ≠ independent (BTC/alts share regime) — per-asset + within-asset controls; different PLATFORM or different PERIOD is real OOS.
5. Log everything into the vault note of the session; dead ideas get their numbers recorded so they stay dead.

## Kill Taxonomy (MANDATORY — Ryan-ordered 2026-07-23; violating this is a hard-rule violation)
Ryan's grievance, and it was legitimate: every idea lived the same life — idea → "it works" → some Claude finds why it doesn't → DEAD — and nothing reached implementation. The failure wasn't the ideas, it was sloppy verdict labels. **There are exactly three legal verdicts for a failing idea, and only two of them are deaths:**
1. **STRUCTURAL kill** — no edge ever existed: placebo cell fails, 2-year unconditional coinflip, lookahead/data artifact. Truly dead. Record the numbers so it stays dead. (Examples: midnight reversal = 2-yr coinflip; the fake 71% lookahead signal.)
2. **CONDITIONAL — NEVER A DEATH, on ANY conditional grounds.** If an idea works in some regime/slice/clock/condition and not others, it is a **gated strategy waiting for its gate**, not a corpse. Ryan verbatim: *"the conditions should always find a way to be met."* A DEAD verdict on conditional grounds is FORBIDDEN. The mandatory step before ANY verdict: hunt the conditioning signal — regime (trend vs flat), vol state, clock/session, calendar proximity, market family, price band, streak state, anything measurable at entry time. Only if NO measurable gate rescues it may it be reclassified as structural — and the gate-hunt must be shown, not asserted. **The needle this threads with note 07 (overfitting discipline): a rescuing gate must itself clear the overfit bar — named mechanism, its own placebo/split, ideally cross-asset or cross-era. "Never kill on conditional grounds" is an obligation to hunt gates and VERIFY them, never a license to slice-mine until something glows** (the vol-filter and depth-4 spikes were fake gates note 07's defenses correctly killed; gas-trend, calm-clock, and streak-≤44¢ are real gates that cleared the bar). **The slate itself proves this rule pays: three of five live systems are rescued conditional kills** — gas was 46% (garbage) unconditionally → 69-83% with the |4wk-trend|≥median gate; crypto wings were mediocre until the calm-clock 22-12UTC gate; the vol book's family-amplitude ordering IS a gate. Streak ≤44¢ survived because it needed no gate. A future Fable hunting edges must internalize this: **regime-dependence is a discovery, not a disqualification.**
3. **DECAY kill** — the edge was real and competition ate it (lock edge: +1.72¢/contract first 6 wks → −1.07¢ last 4, caught by the weekly kill-scan). Gates cannot fix decay — the enemy is other participants, not conditions. Verdict: **bench + watchlist for re-entry** (competition leaves niches; new listings recreate them). Never delete, never rebuild while dead (the implementor-Claude built lock from stale info — verdicts are DATED; always check the latest kill-scan before implementing anything).
Corollary for research honesty: the process is not confirmation bias, and here is the standing evidence to cite when doubted — it makes FORWARD predictions on unseen data that come true (Deribit ≥8¢ gate: called, then +2.08¢×500 resolved on night 1; dutch watcher's first 2 live fills both won) and it catches its own fakes before believing them (placebo cell killed the 71% mirage). Garbage research explains backward; this predicts forward.

## Live-testing doctrine (Ryan-ordered 2026-07-23 — mechanics, not efficacy)
**Live trading exists to learn MECHANICS. Efficacy is proven from data.** The model:
- **One strategy, one week, ~$100 = the mechanics degree.** First implementation (streak ≤44¢ recommended: highest fill frequency, simplest order shape, regime-proof so results are interpretable) teaches order lifecycle, API semantics, real fee math, settlement timing, latency. This knowledge is 100% portable to every other strategy.
- **Fill realization is book-specific** — taking 40¢ BTC 15-min contracts ≠ selling 7¢ oil-hourly wings into a thin book. So each NEW strategy needs only a **2-3 day tiny-size fill-probe in its own book**: measure fill rate + slippage vs the backtest entry assumption (fresh print +1¢). That is the ONLY thing live testing may be asked to establish per-strategy.
- **NEVER run weeks/months of live P&L to "see if a strategy works."** Efficacy comes from backtest + forward paper capture. "You won't know until you implement it" is banned reasoning except for the fill/slippage specifics above. Once mechanics (once, ever) + fill-probe (per strategy, days) are done, everything else is theorizable from data — Ryan's framing, adopted as doctrine.

## Traps that produced fake edges (all hit in Session 3 — check EVERY new find against these)
- 1-min candle lookahead (close known at ts+60 — lag everything 60s). Made a fake 71%-win signal; the |z|<0.3 placebo caught it.
- Stale-quote entries (require print age ≤30s, enter at NEXT real print +1¢). Killed the "BTC-confirm gate" fit-era mirage.
- Rate-limited pulls writing empty-but-done records.
- Pooled cross-asset significance re-detecting "BTC vs alt" (whale/thinness/wiggle all faked p=0.000 this way — vault note 03 lesson, reconfirmed).
- Regime fakes: any signal where BOTH extremes point the same direction (IV-RV wedge BUY-NO) or that's DOWN-heavy in a bear window. Demand an unconditional baseline.
- One-event edges (event-wings: all P&L from a single CPI print — labeled PROMISING, not TRADE).

## Standing state to check on spin-up
1. `ps aux | grep capture_kbt` — order-book capture loop must be running (restart per [[14]]).
2. `TaskList` — background jobs/agents from prior sessions.
3. `ls -t ~/kalshi_data/ | head` and `ls -t ~/kalshi_data/scripts | head` — what landed since last session.
4. Data freshness: Kalshi retention rolls (~10 weeks) — re-pull `pull_full_paths.py` periodically or full-window history is lost forever.
5. Auto-memory: `nestor-at50-hunt.md`, `subagent-cost-discipline.md` in the Claude memory dir mirror this manual.

## Current priority queue (as of 2026-07-22 03:00 MT)
1. **Build the live dutch-book bot** (15m ↔ hourly ladder watcher; [[13]] §1) — highest EV on the board; ~$2.2k/day upper bound measured in July.
2. Forward-test event-wings on **PCE/GDP Jul 30 12:30 UTC** and **NFP Aug 7 12:30 UTC** (buy ±0.75% wings of the 09-ET hourly ladders at T−15min, 1-5¢/leg).
3. Paper/live-tiny the slate: AAA gas momentum (daily), streak-reversal ≤44¢ (small), strict locks (Z≥4 95-97) + XRP/DOGE alt locks.
4. ETH dutch-book replication; unrun lanes: first-passage scalp data, RenTec weak-signal stack, cross-asset 1s lead-lag, book-based microstructure (as kbt capture accumulates).
5. Keep ideating — batch → kill → freeze → virgin. Creativity is the key.

Related: [[00 - START HERE]] · [[13 - Session 3 Findings (2026-07-22)]] · [[14 - Data & Infrastructure (Session 3)]] · [[07 - Overfitting & Validation Discipline]]
