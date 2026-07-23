# 16 - Session Handoff (2026-07-22, account switch)

> ⚠️ **SUPERSEDED by [[18 - LIVE STATE (2026-07-23)]]** — that is the spin-up entry point now; the read order below is outdated. Kept for the account-switch record only.

> Ryan switched Claude accounts mid-pipeline. This file + [[17 - Conversation Log (Session 3)]] make the switch invisible. **Read order for the new instance: [[00 - START HERE]] → [[13 - Session 3 Findings (2026-07-22)]] → [[14 - Data & Infrastructure (Session 3)]] → [[15 - Operating Manual (spin-up & method)]] → this file → [[17 - Conversation Log (Session 3)]].** Note 15 is the operating contract (Ryan's rules, funnel method, cost discipline, venue rule). Then resume exactly as below.

## WHERE WE ARE (as of the switch)
The session was mid-way through the **"moneyball pipeline"**: 16 genuinely-novel ideas (8 crypto-mechanics + 8 niche-market), being killed/verified by opus sub-agents in batches of ≤4 concurrent, with the main loop doing ideation and the FINAL hard verification of survivors (Ryan's explicit division of labor: "the sub agents should do some backtesting, but you should do the real hard work at the end").

### Moneyball scoreboard
**DEAD (5, all logged with numbers in note 13):** wrong-ticker BRTI fade · funding-clock windows · halftime clock · geo theta farming · MM inventory unwind.
**PROMISING (1):** wing vol-seasonality — **BTC-only weekend OTM wing fade**, +0.72¢/contract both eras, weekday control negative (mechanism-clean). Awaiting MY hard verification + maker-fill variant (note 13 §7).
**PROMISING (2 more, landed at the wire — both in note 13):** midnight reversal (00:00 UTC window fades the just-closed daily candle; BTC +19.9¢/ETH +11.4¢, era-decay caveat) — the 23:45 "defense" primary was DEAD. **DEAD (2 more):** Billboard mid-week leak (Kalshi absorbs the building chart same-day; 0/155 favorites lost; n=6).
**DERIBIT DENSITY: landed, then HARD-VERIFIED and DOWNGRADED (note 13 §8b has the full story).** The agent's +7-8¢ lanes were calm-fortnight + population flattered; full-10-week reconciliation shows weekday tail-fading is fee-noise and only WEEKEND wings survive all regimes. Deployable = weekend wing fade (modest); the Deribit net>2¢ gate validates FORWARD via the daily scan logger (`deribit_gate_log.py` loop, runs alongside capture_kbt). Wing-seasonality (BTC) verified as the same finding from the other direction. Midnight reversal stays PROMISING-forward. **MONEYBALL ROUND CLOSED (post-switch):** poll-clock DEAD (liquid series settles on arithmetic + mean-reverts; fuzzy series intraday-dead), climate-ratchet DEAD (brackets finer than the instrument + settlement revisions + n-ceiling), FedWatch DEAD (1¢ rung at 45k/day; providers disagree 18pp on the anchor itself). Final: 16 ideas → 11 dead with numbers, 3 promising (weekend wing fade, midnight reversal fwd, Deribit-gate fwd-logger running), 2 deferred (chart-vs-filter awaits kbt books; xG lag = live design). Bench (cross-coin RV, barrels-vs-vibes, box-office, steam, awards) intentionally NOT spent on — low prior/forward-only; spend there only if Ryan asks. Zero agents in flight.

### Queue after those (approved, 4-at-a-time)
Batch 3 remainder: **poll-release clock** (documented slow post-poll repricing; 538/RCP timestamps vs Kalshi political paths) · **climate anomaly ratchet** (ERA5 daily makes month ~determined by day 20 vs NOAA-settled markets) · **FedWatch→Kalshi Fed** (low prior — rates people already play Kalshi Fed markets). Bench: cross-coin RV (low prior), barrels-vs-vibes (event-study only), box-office Thursday previews, sharp-line steam-following, awards precursor matrix. Deferred: **chart-vs-filter** (needs kbt book capture to accumulate ~1-2 wks), **xG lag** (forward-only design).

### After the pipeline: MY hard-verification pass on all survivors
Entry realism (fresh print +1c / maker variants), era+asset splits, regime controls, placebo cells, venue check (Kalshi/Poly-US only). Then update the slate ranking in note 13 and tell Ryan.

## STANDING CONFIRMED SLATE (pre-moneyball, note 13 — unchanged)
1. Cross-horizon dutch book (BTC 15m↔hourly ladder; capture model done: $486/day conservative floor, $2,240 ceiling; ETH replicates thin; **live capture bot = top build**). 2. AAA gas momentum (+20.5¢/tr). 3. Streak reversal ≤44¢ (holdout-passed). 4. Locks strict-params + XRP/DOGE alt locks; take-don't-rest. Promising: event-wings (**forward dates: PCE/GDP Jul 30 12:30 UTC → KX_D-26JUL3009 wings at 12:15; NFP Aug 7 12:30 UTC → KX_D-26AUG0709**), intraday-temp first-print sniper (AUS/DC/CHI).

## THINGS THAT DIE WITH THE SESSION — restart checklist for the new instance
1. **Order-book capture loop** (`capture_kbt.py`) — CHECK `ps aux | grep capture_kbt`; if dead: `caffeinate -is python3 ~/kalshi_data/scripts/capture_kbt.py >> ~/kalshi_data/kbt_capture.log 2>&1 &` (or as a harness background task). This compounds the microstructure dataset — keep it alive always.
2. The 3 mid-flight agents above — relaunch with resume prompts.
3. Task list — recreate one pipeline task if useful (old task #7 text is superseded by this file).

## COST DISCIPLINE (hard-learned; Ryan is watching spend closely)
- Org spend cap was hit TWICE this session (first by 5 Fable sub-agents, again mid-batch-1). Sub-agents: ONLY `researcher` (opus/high) or `researcher-med` (opus/medium) agent types — files at `~/.claude/agents/researcher{,-med}.md` (same machine, should persist across accounts; recreate from note 15 if missing). Max 4 concurrent.
- Agent prompts are SHORT and point to `~/kalshi_data/AGENT_CONTEXT.md` (machine quirks, cost rules, verification bar, report format — READ IT, it saves every future prompt).
- Main loop: filter every tool output (≤30 lines), scripts print summaries, batch messages. Recommend `/effort high` to Ryan for the main loop (he ran xhigh; thinking bills as output).
- Never anything sneaky he'd discover later (his words re: rate limits).

## KEYS / ACCOUNTS (Ryan's, free tiers, already in scripts)
- KalshiBackTest `<key: see SECRETS.local.md>` (Bearer; free tier = newest 50 mkts of 100ms L2; in capture_kbt.py).
- Predexon `<key: see SECRETS.local.md>` (x-api-key; free endpoints patchy; paid parquet books since Mar 5 @$80/GiB, $50 credits if card added — never added).

## VENUE RULE (hard): bets only on Kalshi or US Polymarket. Global Polymarket = research/signal/arb-reference only.

## EXACT NEXT ACTIONS (in order)
1. Restart kbt capture loop (check first).
2. Relaunch the 3 mid-flight agents (resume prompts; AGENT_CONTEXT.md referenced).
3. As slots free: batch-3 lanes (poll clock, climate ratchet, FedWatch).
4. Hard-verify wing-seasonality (BTC-only) + every survivor that lands.
5. Keep ideating new moneyball batches between agent landings — per Ryan, ideation is the main job; sub-agents ~20%.
6. When the pipeline drains: update note 13 slate ranking, deliver the consolidated "strategy, rule, backtest" report, and put the dutch-book capture bot build back on the table (top standing build).
