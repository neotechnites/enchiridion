# 53 - LIVE STATE (updated in place — the CURRENT block below is the truth)

> Entry: [[SENATE STATEMENTS]] → [[56 - THE MACHINE (fresh-Claude implementation guide)]] →
> this note's CURRENT block.

## CURRENT — 2026-08-04 ~7PM MT: POST-SPIRAL STATE, ENGINE STOPPED PENDING RESTART
- **Day ledger (all positive)**: accrual ~$13-14 engine-attributed (clean rate
  $35-38/day on ~$850, best hr $51.95); realized +$2.99 engine + $0.95 gas
  loops (3 round-trips ~fee-free) ; payouts $76.31 cash landed (estimates→
  cash ~95c/$ PROVEN); zero realized losses all day.
- **Afternoon spiral (lesson, not loss)**: Claude hand-edited live orders/state
  → resize-read-as-fill mass evictions, mispriced taker fills (~$29, exiting
  +1c), book restored by hand. Rule now in permanent memory: NO manual order
  surgery ever; spec→agent-build→test→one deploy; read files not memory;
  code in git (initialized TONIGHT — was never under vcs).
- **Book now (Ryan's 3pm composition, resting+earning, engine-proof manx ns)**:
  10 mention pairs $30/30 (CELH x4, LYFT DOOR/BAID/NVID+MEMB, NBIS x2, WEN),
  CTCQ, ABNB exits, gas remnants. ~$719 seats + $132 exit float. Engine
  STOPPED+disabled; env neutralized to 3pm behavior (rotation 0, crosser off,
  fragility off, deploy 1000, stops 100/200).
- **Tonight's approved build (agent running)**: (1) not-open-market ban;
  (2) phantom-fill immunity (evict only on real position change); (3) repeg
  re-qualification; (4) free-balance floor; (5) dust-floor bug fix; (6)
  calendar file (LYFT/ABNB Wed after-close → T-2h exits; pre-market stays 4am
  default); (7) recency boost x1.5 <24h listings; (8) measured-over-model
  protective floor; (9) one-sided half-funding; (10) manx-aware placement
  (engine skips operator-held markets; inherits at natural expiry). Rotation
  stays OFF (Ryan: the reshaping was one-time, not a standing rule).
- **Restart contract**: engine cannot touch/double manx book; works free
  budget only; inherits mentions Wed. Ryan rulings: no KXYT-specific ban;
  family-escrow-cap and overnight-hold dropped; universe stays open-dials.
- Key measured facts standing: 4.3%/day per $ flat across qualifying seats
  (6.4% overnight); MEMB own-the-level = only premium; fat pools crowd flat
  in hours; premium is captured AT LISTING; supply saturates ~$2.6k escrow
  (~$110/day ceiling; $2k ≈ $75-90/day IF supply proof holds Wed noon).
- Gas loop test CLOSED (+$0.95 net, 3 loops, favorite-touch loops validated;
  fat-pool rent = ordinary). Tier-A/size tests died of handling — re-run
  clean only via engine, pre-registered, untouched windows.

## PRIOR — 2026-08-04 ~3PM MT: SEATS LIVE AT $1,150, RATE MEASURED
- **Measured rate: $35-38/day on ~$850-890 escrow (4.3%/day per $; three
  clean hours agree within cents). Overnight thin-book premium 6.4%/day.
  Realized +$2.99 today, 3 evictions ALL positive-or-flat. Payouts proven:
  $76.31 legacy rewards hit cash overnight (~95c/$1 of estimates).**
- Rank-compression proven 3 ways: qualifying seats earn ~flat ~4%/day;
  model ordering = noise in the middle, signal at extremes. MEMB = the
  exception that defines the next strategy: own-the-level seat (share~1
  both sides) earns ~15%/day. Plan: screen for thin-stable touches (built
  from week's tape, validating vs 20 measured seats) + penny-the-touch test
  Wed (lead 1c in proven-quiet books = manufacture MEMBs). Size curve:
  flat $18-60, concave at $100 in deep books (tests running: MEMB/CTCQ
  upsized w/ own baselines, NBIS-UBER deep-book $100, $30 tier population).
- Engine framework proven: evict/grace/family-bench/caps all fired
  correctly; fills RARE (~0.2/seat-day) and cheap; day = fix-bugs-live
  (walk starvation, class-cap increment, 429-flatten, cap-per-side) — cost
  ~70% of one hour's rate, lesson: batch deploys only.
- Batch tonight ~9pm MT: see nestor-wt-lipv5/tools/virgil/BATCH-2026-08-04.md
  (11 fixes incl iso_ts fuel unlock + measured-rank rotation + exit rework;
  dials flow<=1000 pool>=4). Wed: supply proof at noon mention-exit ->
  gates deposit decision ($2k ~= $75-90/day measured math; capital only
  converts via quality supply — extra $150 at bottom of ranking bought $0).
- Ryan rulings: mentions default home; no reserved experiment capital;
  swing tolerance $200/day; day-stop $100/total $200; estimates=cash
  ratified (receipt now also proves it).

## PRIOR — 2026-08-03 ~EVENING MT: SEATS ENGINE BUILD (post-audit)
- **True P&L (audited, coid-level, reconciles to Kalshi −$1,112)**: loss is
  97% LIP-family: virgil −$866/2.8d, lip_v5 −$215, v4 −$138; nestor +$2
  (never the problem). Virgil fills: 91% of losses from sub-15c acquisitions
  (wings); boxing paid >$1/pair (−$65/day pure arithmetic bug); diesel −$158
  = day-one ladder, both tails filled, settled center.
- **Reward truth**: winner-take-all at touch (DF=0.5/tick; best level takes
  median 99.2% of side reward). Capital elasticity ~0.07 (more $ in same
  book earns ~nothing); scale = seat COUNT in quiet books. Pools real
  (units 1e-4 verified vs receipts). $1/period floor burned 23% of accrual.
  Realized share median 0.30%, max 10.6% → receipts calibration K≈0.06
  (survey's K=0.243 was fit to our best-ever day).
- **Audits killed**: value-anchored making (−27c/ct — market prices gas 10x
  tighter than any public model; profit = passive symmetric inventory, not
  signal-chasing), seating dead pools (fills eat 24x reward), taker
  inversion (takers lose −1.5c), set-sum arbs (zero exist), 34/79 survey
  seats (24 = marketable-seat pricing bug, 10 = stale-book evaporation).
- **What survived every check**: quiet-seat portfolio $25-40/day at K=0.06
  ($1k, 43 audited seats, 68% concentrated UST+diesel, 74% expires <24h →
  reseat 3-4x/day); taker stack $19/day bounded (open-hour +11c/ct h0-1
  cell, temp h-6 cutoff, maker leg); 7-day replay: spec +$174 vs lip_v5
  −$214 / virgil −$707 on the same days, fills CONTROLLED (1.2/seat-day,
  worst single −$11). Replay covers only 7% of pool — hourly-temp
  ($144k/day since Aug 1) is unbacktestable (books vanish at close); our
  one live datapoint: 1.86% of a $1,440 pool in 1 hour.
- **DEPLOYING TONIGHT (Ryan go)**: new seats.py (~500 lines, isolated from
  virgil.py, vgs- coids) — join-don't-lead touch seats, ≥15c floor,
  marketable guard (never bid through live offer), box guard ≤99c, $50/mkt
  escrow, family cap 4, evict-on-fill + day blacklist, $50 day-stop /
  $100 total kill, K=0.06 selection, fresh-book-at-placement. $200 day one
  → $1k if scoreboard (estimates accrual + fill ledger) matches. Swing
  tolerance ratified $200/day. Estimates=cash ratified by Ryan (Credits
  page; payments have always been ≥ estimates).
- Artifacts: VPS /home/ubuntu/{an,qual,audit,var,replay7,taker,fuelq,curse}.

## PRIOR — 2026-08-03 ~9 AM MT: RESEARCH VERDICT + CLASS-BASED MACHINE
- **Ryan's 0/54 instinct proven**: residual (unboxed) inventory reaching
  settlement lost 54/54 lifetime (P<1e-3 fair; vs measured 63.4% maker win
  rate P=2.7e-24). Mechanism: residue = the informed side's discard pile;
  in Class-A books fair value is a PUBLIC TICK STREAM (Pyth/CF candles;
  METAR locks the day's high hours pre-close; free 4:34pm intermediate CLI
  pins settlement 85.5% w/ 7.5h left; AAA ~85% predetermined by wholesale
  lags OOS R2 0.64). ~100% pickoff there is EQUILIBRIUM — exit, never
  optimize. Counterparty = ~2,000 small latency bots (not SIG), structural.
- **Viability (Whelan 313,972 obs; Bartlett-O'Hara 41.6M trades)**: Kalshi
  makers +1-2c/contract aggregate, ALL from favorite-longshot bias: maker
  ≥50c +2.6%, ≤10c −60%; final-10-min 10-40c contracts "resolve favorably
  almost never" (24M trades). Doctrine: residual must land on FAVORITES;
  flat before prints; box>95% or rewards cover; binaries have NO hedge near
  settle — flat is the only protection.
- **Ceiling ($1k, documented)**: nothing reaches $200/day. Stack: LIP $3-10
  net (program ENDS SEPT 1 — 4wk runway), weather-informed $2-8+ (running-
  max floor trades are deterministic), AAA lag model small, favorite-bias
  $1-5. Realistic $15-40/day. Reading public feeds = legal (Rule 5.17(y)
  covers pre-publication info only).
- **MACHINE NOW (b73cbac)**: websockets live (ws.py, lane ws-first);
  blocklist = prints + SIG flagships + lip-maker audited losers + long-
  dated one-shots; maker-fee series gate (130 double-taxed econ prints);
  absorbability cap (rest ≤ opposing depth); volatility backoff; asymmetric
  budgets → no benches; boxes never cancelled; era-scoped day-stop/deploy;
  hourly re-rank + rotation threshold; breaker 2000 armed (int bug found).
  Book = Class-C only (mentions/politics — no observable underlying;
  insider risk episodic not structural).
- **PENDING RYAN**: build order for (a) METAR weather feed + deterministic
  lower-bound trades, (b) AAA lag model, (c) favorite-side quoting bias.
  Rewards paid receipts: ~$32 estimates → counter (phantom question live).
- Research artifacts in session scratchpad; 8 agent reports (viability,
  ecology, blocklists, feeds, forecastability, small-capital, betting
  exchanges, ws build).

## CURRENT — 2026-08-02 ~12:45 PM MT: SPEC-CORRECT MACHINE LIVE AT $1K DEPLOY
- Rebuilt on the CFTC spec end-to-end and LIVE (commits c148109..63d5198):
  exact scorer module (lip_score.py, 14 tests) drives selection share; deficit-
  affordable universe (not target<=600 — big pools whose books already qualify
  admit at pro-rata); derived sizing max(cliff completion, sqrt water-fill) с
  $50/side resting notional; greedy yield/$ fill; source gross cap (half
  deploy, floored one pair); deploy/risk split (VIRGIL_DEPLOY_USD=1000,
  capital 1000, fresh baseline) — day-stop/-bankroll era-scoped after legacy
  settlements tripped the stop at -$233.
- RISK NOW STATE-BASED (bot-standard): fills recorded, never bench (asymmetric
  first, then removed entirely); swing = net caps + notional + source cap +
  exchange order-group breaker (armed for real — contracts_limit must be INT,
  was silently 400-bad-request all along); bleed = era day-stop -$200.
- PLACEMENT: pre-send BBO recheck on every placement (skip, never chase —
  morning gas crossed a moved book for ~$200 of instant fills, budgets capped
  it); DF/cutoff-gated behind-the-touch (df>=0.7 AND depth-ahead < TargetSize
  else cutoff zeroes us — Ryan's catch), re-evaluated EVERY fast-lane pass;
  maintain clamps legality only (lane owns pricing).
- Boxes: completing side never benched/cancelled (Ryan's law: no reason ever);
  recovery lane persists exits for all position markets.
- MEASURED: overnight new-era book accrued ~2x the 0.25-calibration floor
  (APRPOTUS 39.3 crossed $1 payable). Noon book: ~$920 working, stored
  prediction $66.74/day floor (realistic $60-130). Truth mode: 1 AM board
  $12.29/day, 11 AM board $21.45/day @ $250.
- Constants ledger (all disclosed): 0.25 calib + 30-min pre-close (lip-maker
  reconciled data), $50/side + day-stop (ratified), 6h/24h exclusions (our
  receipts), 0.005/day carry + DF>=0.7 + breaker 400 (judgment, data-pending).
- NEXT: measured-vs-predicted reconciliation (predictions stored per seat);
  markout control + observed-share blend once data accumulates; websocket
  build (deferred by Ryan); rebalance 4h + rollover trigger.

## CURRENT — 2026-08-02 ~2 AM MT: VIRGIL OFF; TRUE LIP SPEC FOUND (10-agent research sweep)
- **Virgil stopped+disabled by Ryan** after hourly-temp incident: rank artifact
  admitted 1h-window promos; toxic near-close fills; my liquidation retries
  STACKED (positions-feed lag) and flipped a winning short into 98-99c longs
  (~$300 damage, mine). HARD RULE saved to memory: never touch positions
  without fresh explicit permission; money orders never roll forward.
- **THE REAL LIP SPEC (CFTC filing Aug-2025 + Feb-2026 amendment; cross-
  confirmed by derekduenas/lip-maker, a production farmer w/ payout
  reconciliation)**: score = size x DF^(ticks from SAME-SIDE BEST BID); DF
  per-market = discount_factor_bps/1e4 (0.20-1.00); TargetSize walk from best
  = cutoff, deeper levels score ZERO, side w/o TargetSize pays nobody; since
  Feb-28 a snapshot lacking TargetSize depth on EITHER side pays NOBODY
  (market-wide, not per-user); pools per-market for the whole Time Period
  (period_reward = centi-cents for the PERIOD, $10-1000/day bounds); $1 min
  per program-period (dilution, no per-window forfeit); scoring finalized
  AFTER program end, estimates non-binding, unclaimed pool evaporates;
  presence linear (my f^1.15 was fitting the two-sided-exclusion artifact).
  Reality pays ~0.25x naive theory (lip-maker's reconciled calibration).
- **At $250: only small-TargetSize markets are viable** (share ~0 where
  target>=1000). Sizing = cliff-first (target - competition + ~30 buffer),
  THEN water-fill残 marginal yield. qual_rate (fraction of paying snapshots)
  <0.3 => market pays ~$0 regardless of placement.
- **Weather: never quote two-sided** (monotone observable ratchet; the only
  sound Kalshi MM quotes one side, blacklists range buckets; obs latency:
  api.weather.gov lags 20-45min vs aviationweather.gov METAR). One-sided
  still earns LIP (two-sided snapshot rule is market-wide).
- **Ops unlocked**: WS (wss://external-api-ws.kalshi.com/trade-api/ws/v2,
  zero REST cost, fill channel w/ post_position_fp kills positions-lag);
  real budget 100 write tok/s (create=10, cancel=2) => ~8 requotes/s;
  amend endpoint (atomic, closes cancel/fill race); decrease keeps queue;
  queue_positions endpoint; ORDER GROUPS = exchange-side breaker
  (contracts_limit per rolling 15s cancels whole group); STP required in v2;
  v1 /portfolio/orders paths are 410.
- **Safety stack to adopt**: pre-send BBO recheck, identical-quote cache,
  zombie cancel >=3c off best, pull all 30min pre-close (+$60/wk audited),
  volatility backoff, toxicity score (consec same-dir fills + markout +
  fill rate -> widen), 200ms frame-staleness discipline.
- **REBUILD PLAN (presented, awaiting Ryan's go)**: P1 wire (WS mirror,
  amend, order groups, STP/path audit) -> P2 TRUTH MODE (exact CFTC scorer
  on live snapshots, ZERO orders: per-market qual_rate + what $250 earns
  where) -> P3 live only where measurement says it pays. Mandate 250.
  Research agents' repo clones in session scratchpad repos/.

## CURRENT — 2026-08-01 evening MT: FORMULA HARD RULES + QUALIFYING DOOR (commits 8cb7059, c24ec1b)
- **Brent autopsy (Ryan caught it twice)**: first live selections kept picking
  KXBRENTMON-26AUG31 (rank 0.094) — a "new_event" promo, $20/18h window, near-empty
  book. It earned ZERO all day (zero estimate rows). Root cause: `target_size_fp`
  (the qualifying door — pool pays NOBODY until that many contracts rest) was parsed,
  carried as c["target"], and never checked; Brent's door is 1,000 vs 62 resting.
  The empty books pool-per-rival loves are exactly the ones that can't qualify —
  rival absence in month-locked small pools is CONSENSUS, not alpha (Ryan's law).
- **Three formula gates now live**: (1) door gate — skip unless qy+qn+2S ≥ target
  (qy/qn were built for this and never wired); (2) hard max fill-lock 7 days
  (kills every monthly; replaces derate-only); (3) min pool $10/day. Lock carry +
  PROGRAM_END_TS exclusion + two-pass close fetch (finalists only) still in front.
- Funding doors deliberately NOT done for big doors: Brent would cost ~$450-600
  resting to unlock ≤$10/side-window. Doors of 10-100 self-fund via our 2×30 seats
  and pass the gate naturally.
- Post-fix selection: gas 4.100 both sides (17c/82c, rank 0.169) — verifying wider
  re-pick as sweep coverage climbs (~1.7k/5.4k @ 260 books/cycle). Bankroll $378,
  in-flight $622, realized today −$31.
- Sweep raised VIRGIL_FORMULA_SWEEP_N=260; selection metadata pool_day/days_left
  persist as zeros (cosmetic, open).
- **DISTANCE DECAY + REQUOTE (3d57ae8, same evening)**: 50-min live calibration —
  tight books at the touch earn ON model (gas $0.32 vs $0.35 pred); wide books
  10-30x under EVEN at the touch (SNAP 7c spread $0.027 vs $0.49); stale prices
  lethal (SATX 36c below moved touch, dead). Kalshi pays 0.5^(cents from
  reference≈mid) — the old rank/backtest had NO decay, so dollar forecasts
  ($227/$261) were optimistic by the spread's decay factor. Fixes live: rank ×=
  0.5^half-spread (empty-and-tight is gold, empty-and-wide is a mirage) +
  maintain re-joins the fresh book every cycle (1c sticky bounds churn).
  Backstop close_time bug also fixed (b1d98a8): ticker-embedded settle date
  overrides Jan-2027 placeholder closes that excluded 93/120 candidates.
  UNEXPLAINED-then-SOLVED: the refit (7 live points + 499 measured ladders,
  logRMS 0.576) found the real mechanism = **WINDOW PRESENCE f^1.15** — credit
  scales with (elapsed window/window length)^1.15, so day-one programs pay
  ~nothing (THIS zeroed Brent + the mention markets; door and distance decay
  were both wrong: door measured FALSE — TRUMPENDORSE-A3 paid at 836/1000;
  decay cancels at the touch, rivals decay too, rho=0.878 tight/0.666 wide).
  Honest re-run: frozen S30/M40/T.05 LOSES money (−$32 on 07-31); tuned
  winner S=30 M=10 K=10 T=0.01 nets +$23/+$32/day at ~$300 collateral
  (cliff punishes thin spreading; capital NOT binding). Deployed c148109.
- **CAPITAL ONE-WAY CONVERSION SOLVED (Ryan's box demand, ~1 AM 08-02)**:
  fills were near-perfectly one-sided per market (BOS 361y/0n, ATL 0/528);
  executor L1 never-reduce REFUSED the box-completing side 237x while the
  eaten side re-armed 12+ times under the $50 rung budget. Fixes live:
  box_ok seats bypass L1 (maker box completion = Kalshi returns $1/pair,
  never-sell bars taker liquidation only); net-exposure cap (side net-long S
  stops re-arming, completing side keeps its join); dup-order leak fixed
  (SEA double-yes). Suspects left: fit's own share term unvalidated
  (degenerate share≡1 fits better — sample lacks power); period_reward may
  be PER-DAY not per-window (all windows end 04:00Z — if so long windows
  are ~4.5x richer; check one program's accrual across a 04:00Z boundary);
  lipband_capture.py records empty books (top-level orderbook_fp, 1-line fix).
- **STALE FILLS WERE NEGATIVE EV, measured** (Ryan's autopsy, ~9:45 PM 08-01):
  7/7 JUL31 rain naked positions settled $0; AUG01 cohort -$127 pending on
  $199 basis. Not variance — adverse selection on frozen quotes (fills only
  when informed flow ran through us). NOT a predictive signal: info arrives
  WITH the fill; post-fill price already correct ("take the other side" has
  no edge a priori — flow-continuation is testable from our 131 ledger fills,
  open R&D). Mandate back to 1000 (the +500 was stop-gap, returned).
- **4a523c0 (~11:30 PM MT 08-01)**: FAST REPEG LANE — daemon thread cycles
  ONLY the selected seats' books, surgically re-pegs resting orders to fresh
  joins (executor.repeg: min-life, 1c epsilon, pair guard; never creates
  sides) — v4's ~2s cadence class on our 10 seats within the 3 req/s budget.
  Plus ROLLOVER-TRIGGERED REBALANCE: >50% of selected seats dead with their
  programs -> re-pick now (n0 baseline prevents thin-selection churn); kills
  the ~90-min idle gap after the nightly 04:00Z window close. Websocket feed
  = queued R&D (client is REST-only).

## EARLIER 2026-08-02 ~3:15 AM: FORMULA MODE LIVE (commit d9948ce + coverage gate)
- Deployed VIRGIL_MODE=formula: the backtested allocator (rank = pool$/window-day ÷
  (rivals+2S); top 40, ≤5/family, S=30 BOTH sides at joins; 4h rebalance gated on
  ≥60% depth-cache board coverage; catastrophe shell only: day-stop 20%, $50 rung
  budgets, tick floor, pinned, bankroll=mandate−in-flight). Depth sweep reads
  ~120 books/cycle over the full ~3.7k reward board (fixes the NFLT100 scanner
  blindness; NFL cluster sits just under T=0.05 — admitting it = VIRGIL_FORMULA_T
  ≈0.035, Ryan's knob). Pre-deploy adversarial review: 3 money bugs fixed (stale
  maintain re-cross loop, locked-book ask-parking, dead-program persistence);
  123 tests green. THIS deploy was the LAST book-flatten (hygiene live: stops
  preserve the book, boots adopt vg-* back).
- Capital: ~$271 cash at deploy; ~$710 of already-settled EV posts through the
  morning → ~$980; ~$1,050 by Sunday night. No selling (spread+fees > waiting).
- Expected next 24h: backtest baseline ~$200-220 accrued; conservative row
  $70-100; the decisive live measurement = weather fill costs (backtest's one
  unverified leg) + per-seat accrual vs backtest prediction (monitor reports 8 AM).

## EARLIER 2026-08-02 ~1 AM: THE ANSWER, measured — and the formula that backtests $100+/day
- **Why v4 earned and Virgil didn't (three fresh-agent investigations, all numbers):**
  presence VOLUME was never the gap (both ~7k contract-hours/day). The gap is
  placement quality, 10-50x: (1) pond size — v4 avg 74 rival contracts (share 0.61),
  Virgil 491 (share 0.15); (2) cliff dispersion — v4 cleared 17 programs/kept 74%,
  Virgil cleared 2 of 187/forfeited 79%; (3) one-sided (v4 both sides 83/117 mkts,
  Virgil 24/103); (4) staleness (2s vs 148s requote; 1.0c vs 1.8c from ref = 1.78x
  score). Same-market control: gas, $30.60 vs $5.22 per 1k ch. Presence autopsy:
  book empty 41% of clock; 47 operator restarts (58% of $-gap); 59% of placements
  were no-op churn; Mallory evicted 17x by plan jitter, never by any gate; gates
  only 6% of the gap. Comp map (40M deltas): NO free pools (3 of 2,091), rivals are
  24/7 bots, depth DOUBLED in 24h = August program rollover being resaturated —
  v4's day was the July-tail lull. Unfound: KXNFLT100 cluster (25 mkts x $100/30d,
  15-25 rivals, hidden from pool/day scanners — ours included).
- **THE FORMULA (backtested on real 3-day books, tuned 7/30-31, FROZEN-validated 8/1):**
  every 4h rank ALL reward markets by pool$/window-day ÷ (rivals near touch + 2S);
  top 40, ≤5/family, S=30 contracts BOTH sides at the joins, $1k collateral.
  Net: +$48(4h tape)/+$227/+$261 — validation day best. Rate $5.8-6.9/1k ch sits
  between the real anchors (Virgil 1.69, v4 17.8). Survives depth+100%, φx2,
  share-cap 0.50 singly; knock-out of top-8 clusters still $82-98/day. CAPACITY
  binds, not capital ($1k→$261, $5k→$273). Thinnest leg: weather fill costs
  (unmeasured in ladder study) — measure live first. Uptime linear.
- **Built tonight (committed 5bbd4ea + formula-mode in flight, NOT deployed):**
  presence hygiene — restarts preserve the book + boot adopts vg-* back; sticky
  seats + 1.15x incumbent hysteresis (ends Mallory evictions); size-to-free-bank
  (60% floor, presence outranks the hurdle below it — flagged econ change).
  v4-mode draft shelved (Ryan: not running a literal v4). Formula mode: builder
  running (depth-cache full-board rotating sweep fixes the NFLT100 scanner
  blindness; V4Shell catastrophe gates only).
- Live machine: still on the pre-hygiene engine, untouched tonight. Rewards accrued
  this window: $28.66 total, only $9.92 above cliffs (172 sub-$1 crumbs = the
  dispersion disease, now formula-target #2).

## PRE-FORMULA — 2026-08-01 ~1:15 PM MT: Virgil v3 LIVE, deploying under receipt-prior Kelly
- **The allocator (Ryan-ratified by construction, built today):** deploy to the
  liquidity edge (mandate $1,000; in-flight fills release at settlement, stale
  closed-market collateral force-released); Kelly-with-RECEIPT-PRIOR source caps
  f* = (y/carry)·p/(1−p) — y floored at 5%/day on 300+-contract walls (receipts:
  v4 $71/day on $250; McMorrow 3.7%/day), carry = 1 + min(1,φ·24)·lag with the
  wall-JOIN φ prior 0.005 (v4 tape); v4 join-the-wall pricing (two-sided by
  construction, 9-fills-in-14h queue protection); ruin guard at 1σ=bank; $50/rung
  budgets; cross-alarms; no yield floor / pool cap / market clamp (subsumed).
- **Book now: $229 resting (Fox-mention $119, gas daily 4.090 $100, PHX rain $10)
  + ~$540 real open positions** (rain AUG01/02 settle tonight-tomorrow, gas AUG02
  tonight) = at the honest liquidity edge. Caps widen as accrual proves yields
  (attribution feeds y). Treasuries Monday = first full-size day.
- Day-2 lesson chain (each measured, each fixed): stale in-flight starved the bank
  ($743 counted vs ~$560 real — closed-market release added); lag must price the
  FILL, not the seat (carry, not /30); the busy-φ fallback was an artifact of at-mid
  quoting; the 1,000-contract door is fundable when affordable (our contracts count);
  top-N clamp starved ladders once Kelly caps existed.
- 3-lane adversarial review (19 findings) all deployed; suite 49 green.

## PRE-NOON — 2026-08-01 ~1:45 AM MT: v3 risk redesign building, deploy held
- **Ryan's overnight directive (his words are the spec):** deploy 100% of capital into
  markets passing his two questions (earns? / too risky?); "20% swing" bounds the
  SWING OF FILLS (avg price per contract of the position book), NEVER deployment;
  resting is not risk (fills EV-neutral); don't add where our position is already big
  (source walls); take empty pools where a reference exists. **BUILT + 3-lane
  adversarial review (units / rail-bypasses / ledger-integrity): 19 findings fixed,
  suite 49 green, commit f42c9d1. Money bugs caught pre-deploy: NO-side fills booked
  on the yes axis (a $10 lock read as $190 — the "ATL $338 fill storm" was largely
  this illusion); shared-account settlement revenue crediting our bank ($507 for $7);
  boot blind to fills-while-down (now trued up from wire positions); reconcile/plan
  thread race minting phantom fills (one lock now); source wall splitting across
  window boundaries (now keyed by market close, Denver time); --live now REFUSES to
  boot without the bank baseline. Operator notes in the fixer report. NOTHING deploys
  until Ryan's go** (his order).
- Day-2 fixes already live: feed pagination 20→140 pages (20 pages MISSED the new gas
  windows entirely — why no gas seated overnight); fill budgets keyed (market,side)
  not price (ATL filled $338 by minting a fresh $50 budget per re-pegged cent); source
  walls count unsettled fills; realized settlement P&L adjusts the working bank; price-
  scaled source wall (20%×bank×√(p/(1-p)), $200 absolute ceiling — Ryan's numbers).
- Measured tonight: post-never-cross φ = 0.18-0.27 in rain (was 1.0+ — the fill storm
  was largely our own crossing). 105 settlements returned ~$501 overnight. ATL position
  ($338 at ~6c NO) settles ~1 AM MT — outcome in the morning report.
- Treasuries Monday (weekend = no UST). Gas 26AUG02 in scan since the pagination fix;
  books empty at 1 AM, seats fund when makers populate them.
- Monitor agent on watch; 7 AM MT consolidated report (accrual/seat, ATL outcome,
  gas seating).

## PRE-v3 — 2026-07-31 ~7:30 PM MT: VIRGIL v2 (v4 economics) LIVE on $700
- **Relaunched 7:26 PM MT** after a full economics rebuild. Day-1 lessons, all measured:
  $10 dust seats earn ~nothing; quoting at stale best levels scores ~zero (halving/cent);
  quoting AT mid in same-day-settling thin books = informed-flow food ($376 converted,
  $193 of it dying JUL31 rain); restarts were resetting fill budgets (fixed: rebuilt from
  ledger); fills must charge the earn rate via v4's hurdle φ·d/p, NOT as 100%-of-float
  (that collapsed the book to $52).
- **Virgil now runs v4's exact economics** (ported from lip_maker_v4.py with provenance):
  ρ=pool/window-hours; rank/fund by ρ/(2pS) first-dollar rate, S in contracts
  (max of live book, historical tape); water-fill $10 slices to equalized r*; hurdle
  φ·d/p with d=min(7¢,p); pinned exclusion; quote at reference; pair-sum ≤99¢. Rails
  kept: never-sell, $50/rung/day fill budgets (persist across restarts), conversion cap
  20%/day WITH settlement-lag weighting, per-source swing ≤20%, tick-floor refusal
  (Ryan's order), φ floor 0.02 unless 20k+ contract-hours (Ryan: never assume φ=0).
- **30-day tape measurement (ladder_days.md, ratify-worthy): treasuries are the business**
  (+$20-26/day/series at receipt-anchored 2.3% share, breakeven 0.2-0.6%, worst day −$28);
  **gas is breakeven-to-negative** (breakeven 2.20% vs achieved 1.25-2.28%, worst day
  −$340, negative skew) — matches the Jul27 receipt day exactly (gas $38.80 paid vs
  ~$39 fill damage = wash; TREASURIES were the real earner). Tight (1¢) beats wide;
  small share beats large. Treasury churn doubled late in sample — re-measure weekly.
- **Tonight**: zero placements until Denver midnight (today's $368 conversion remembered
  → budget correctly exhausted at $700 capital/$140 cap). At midnight: rain AUG01/02
  pairs (~$10/side = ~90% of those tiny sides — saturated share, NOT dust; the overnight
  estimates feed answers whether dominated rain sides pay dollars or pennies) + gas
  26AUG02 small. Monday: treasury dailies = the real test. Monitor agent briefed,
  reports 7 AM MT.
- ~$376 in positions riding (JUL31 rain + gas AUG01 settle tonight; ~$40 long-dated).
- Kill: `sudo systemctl stop virgil` (cancels own vg-* orders only; account shared).

## PRE-VIRGIL-v2 — 2026-07-31 ~4:20 PM MT: VIRGIL LIVE
- **VIRGIL is the LIP maker now** — new standalone package `tools/virgil/` (nestor worktree
  nestor-wt-lipv5), NOT a lip_v5 version. Five boxes: alpha (rate = share×pool/2×presence,
  rank by marginal rate) / risk (conversion cap 20%/day HARD — Ryan; swing √(Σw²(1−p)/p) ≤
  20% capital on FILLED dollars by settle source; rung fill budget $50/day) / chooser
  (greedy $10 slices) / executor (vg-* coids only, never sells, 30s min resting life, REST
  polling no websockets) / attribution (estimates poll 300s — Ryan: estimates≈paid, close
  enough; measured rate recorded but NOT yet fed back into allocation = first upgrade).
- **Live since 22:00Z on $1,000**, service `virgil` on the VPS, systemd-enabled. First
  cycle: 24 orders, 0 errors, 0 instant fills, ~$340 deployed (conversion cap binds),
  predicted $448/window CEILING (thin evening books; the number to distrust). Book: rain
  city dailies + gas 4.105/4.110 + primaries/oddballs, 7 settle sources, both sides.
  Rider mode v1: only joins sides rivals already qualified (1000c); S=0/empty books
  refused (D2); tick floor (<5c) refused (Ryan's order at go). Treasuries join when
  their windows open Aug 1.
- **Jul 27 forensics (the $70 day, from the tape)**: NOT 1-2s churn — 5,546 places/14.4h,
  median rest 2s but continuous presence via instant replacement; paying presence was
  70-90% at 6c+; only 9 instant fills/261 contracts; −$196.99 IS sourced
  (external_cash.jsonl, fills+settlements). Statements 43/45 need correction, 40 refines
  to $36.28 phantom across 4 hourly-index programs — Ryan not yet ratified.
- **Open**: (1) φ=0-measured families (McMorrow $120) charge zero conversion/swing — thin
  evidence hole, propose flooring φ at phi_quiet without strong n; (2) estimate→paid
  ratio lands with tomorrow's statement = attribution's first real datapoint; (3) pool
  unit CONFIRMED $100/program/window (÷10000).
- Watch: background monitor on first 3h (service, fills, churn, accrual). Kill switch:
  `sudo systemctl stop virgil` (cancels own orders on SIGINT).

## PRE-VIRGIL — 2026-07-31 end of day (details in [[55]]'s tail)
- **v5 STOPPED** (Ryan's order, ~12:03 PM MT 07-31; 43 positions ride to settlement — it
  never sells). The $1-earned day confirmed the dispersion diagnosis: $10 seats accrue
  under the $1 cliff and forfeit.
- **v6 STOPPED + DISABLED** (cannot return on reboot) after seven same-day deploys, all
  pulled by Ryan — churn at the truncation boundary, then EV-crumb books (ledger carried
  the dead book across deploys; fixed by clean-slate archiving), then an unexplained SME
  placement lane. Committed on v6-build: sticky book (proven live, zero churn), measured-
  rate loop, seed mode (untested on wire), multi-market-per-cluster, rank-truncation dials
  (C_DIALS=2000 vs C_CASH=deposit), v4-rank chooser (corrected full-wall denominator).
- **DEPLOY GATE #0 (permanent)**: no v6 deploy until `lip_v5.dryrun` prints the production
  would-buy book offline and WE verify it against expectation. NOTHING runs until Ryan says.
- Open: seeds on the wire? · what book the corrected chooser builds (~4 undisturbed min
  from cold boot) · the estimator vs the tape ($19.7 projected vs ~$2 payable — THE open
  question; prime suspect S mismeasurement).

## OLDER LOGS
The dated running logs (2026-07-29 → 07-30 build/incident record) live in this file's
git history. Page them in only when a specific question needs them.
