# 53 - LIVE STATE — 2026-07-29 ~23:00 MT, note-52 build done

> Read [[42 - SPIN-UP]] → [[52 - THE LIP STRATEGY (settled with Ryan, 2026-07-29 night)]] →
> this.  Supersedes [[50]]'s state half.  The strategy is note 52; this is where the build
> stands against it.

## STATE — DEPLOYED AND LIVE, 2026-07-29 ~23:40 MT (Ryan's explicit go)
- **v5 LIVE on the VPS** (`lip-v5.service` active, commit `0f576a1`).  Ryan, verbatim: "go
  ahead and deploy it, you have my permission to run it" — recorded in `v5_go.json` (G3).
- Deploy record: old code tar-backed-up in `~/kalshi_data/v5/`; old v5 state archived to
  `~/nestor/data/lip/archive-20260730-prelaunch/` (coid seq KEPT in place — collision guard);
  suite run ON THE VPS (python 3.12): 683 with 2 environmental fails (tests that assert
  nestor's state file is unreadable; on the VPS the real one exists and reads — the
  deployment working, leaking into a bare-machine fixture).
- **First real order within ~3 min of arming: `KXUST10AD-26JUL30-T4.73`, ask, 4 @ 13c
  ($3.48 collateral)** — settles inside 24h, lot + reserve shape.  Accrual ticking, cash
  feed publishing (nestor's breaker fed), 2 venues admitted and classify still converging.
- Branch `lip-v5-build` pushed.  **683 tests green** locally.
- **nestor** live on the VPS, untouched.
- **lipband capture LIVE on the VPS** (`lipband-capture.service`, since ~22:15 MT):
  books + trade tape on ~400 LIP markets every 10 min → `~/kalshi_data/lipband/`.
  This is note 52 §7.1 — φ by price band, nibbled-vs-swept, band supply.  Usable in ~a day.

## WHAT WAS BUILT (all of note 52 §8, commit 0f576a1)
1. **D4 settlement gate** — entry gated on the MARKET close ≤168h; unknown close refuses;
   held exempt; request-free far-filter in candidates; CLASSIFY_MAX_MARKETS 400.
2. **D5/D6 cap stack** — cluster reserve $10 = ceiling/30; lot container $5 = reserve/2;
   ONE rung per cluster; refills EMERGENT (reserve/lot − 1); ENTRY_FLOOR = $1.50 =
   CREDIT_TARGET × MARGIN; v1's NET cap superseded (it killed the replenish on the first
   fill — the lot re-posts whole, the cluster rail ends the period).
3. **D11 plan-side variance** — V per cluster against the ceiling, every money cluster
   charged at its RESERVE; skipped ≠ refused-forever (steers the average price).  Rail stays.
4. **D12 period lock** — funded rungs (money resting/held) are never zeroed, dropped, or
   rescue-evicted on an unmeasured p_recover while the cliff is reachable.
5. Tests 658 → 683; seven new guards mutation-checked.

## MEASURED AGAINST THE LIVE BOARD (shadow: real public reads, fake wire, 2 probes × 5 min)
- The gates fire on real payloads: KXTXAGCOM refused at settle_h=11,075; the ≤24h treasury
  dailies admitted.
- **The first lot container ($2.50) passed 683 tests and refused EVERY venue on the live
  board** — note 47 §4's own median cost-to-clear is $3.68.  Recalibrated to $5 (= reserve/2).
  [[45]]'s thesis, again, caught by the probe and not the suite.
- With $5: 734 markets classified in 5 min, 2 venues admitted, **one funded rung:
  `KXUST10AD-26JUL30-T4.73`, 41 contracts at 12¢, $4.92, settling inside 24h** — the
  strategy's exact shape (emptiest band, near settlement, lot + reserve).
- **Breadth is 1, not 30, at probe end.**  Two known causes: (a) classify convergence — the
  probe's 5 curl-bound minutes only reached the top of the ρ rank, which is the far/deep end;
  production at 4 Hz converges in well under an hour, helped by the widened budget and the
  far-filter; (b) genuinely, most of the top-ρ venues' cheapest lots exceed $5 at their real
  rival depth (7 of 9 surveyed venues unprobeable).  **How many of the ≤7d clusters admit a
  ≤$5 lot is THE open supply question** — answered for free by letting a longer shadow run,
  or by the first live hour.

## THE THREE BUGS THE BUILD FOUND (beyond the container)
1. **The replenish died on the first fill** under v1 §8.1's NET cap — presence-by-re-posting
   was structurally impossible in every prior version.  (This alone may explain a large part
   of the measured 10.6% presence.)
2. **The rescue was a churn engine**: p_recover defaults to 0 (never measured), so ABANDON
   always beat HOLD for funded-but-below-floor rungs — rivals deepen the book, the gate
   cancels mid-period, the accrual forfeits.  Now: unmeasured probability cannot evict a
   funded rung (note 49 R1); computed-impossible still abandons.
3. **Charging variance at actuals instead of reserves** admitted forty 2¢ clusters where the
   reserve charge stops at four.

## NEXT (in order)
1. Ryan's go → deploy to VPS (`~/kalshi_data/v5/`, `lip-v5.service`), arm via his
   `v5_go.json`.  Deploy note: `systemctl kill -s KILL` + `start` preserves the book;
   a normal `stop` cancels it ([[47]] §7).
2. First live hour: watch breadth converge; the supply question above answers itself.
   `entry_band_refused` / `settle_horizon_refused` / venue-unprobeable counts are the
   instruments.
3. lipband capture → φ by band + nibbled-vs-swept (note 52 §5's switch rule) + re-derive the
   cheap-end EV bias from raw settlements (note 52 §7.2).


## NIGHT LOG (2026-07-29 ~23:00 - 23:55 MT; earlier draft misread the VPS journal's UTC clock as MT)
- **~22:10** nestor's divergence breaker halted it — v5's fills were booking as ZERO
  contracts: the wire moved to `count_fp` fractional dollar-strings and the parser read the
  dead `count` field.  Fixed against a CAPTURED payload (commit `651e866`, tests 683→688);
  the fake now speaks only the wire's dialect.  Bonus evidence: `fee_cost` on our real maker
  fills is **$0.000000, both sides** — the UI's per-ticket fee column is a projection.
- **~23:00** discovery was crawling: my own lipband capture was eating the per-IP public
  rate limit and v5's AIMD had collapsed to 1 req/s.  Throttled the capture (1 req/s,
  250 tickers, 20-min cycles).  v5 then converged fast: **33 venues, 546 slots,
  $80.99/day projected reward** by 23:47.
- **~23:47** B14 place-burst breaker HALTED v5, correctly: `KXTRUMPSAYMONTH-26AUG01-PELO`
  ate three replenish lots in three seconds, one fill at 7c against our 8c bid — the ask
  collapsed through our price; maker bids were crossing into informed news flow.  ~$15 of
  inventory, bounded by the cluster rail.  **The mention MECHANISM wearing a non-MENTION
  ticker** (note 43 §4): `KXTRUMPSAY` family denied by substring (commit pushed).  Halt
  flattened the book; closing sheds rest.  Denied-family sheds cannot re-post (series_denied
  has no closing exemption) — the resting shed either fills or the ~$15 rides to Aug 1.
- **~23:53** RESUMED with the deny fix live (the schedule-for-03:05 script fired early — same UTC/MT confusion — which turned out right: v5 trades until the 1-3 AM MT maintenance, coasts through it, resumes after).  Held KXTRUMPSAY positions (~$17) have no slot under the deny and their sheds cannot re-post (series_denied has no closing exemption): they ride to Aug 1 settlement, bounded.
  nestor stays halted until v5's feed is verified true; resume ritual after that.
- Breaker inventory for the night: B14 caught a real fire within 4 hours of first arming.
  The cap stack held: every cluster ≤ $10 worst-case throughout.


## MORNING LOG (2026-07-30, ~06:20-07:30 MT)
- **The night's take: the φ dataset is REAL.**  121 orders, 23 fills / 571 contracts across
  9 series, every one with true size/price/fee; 2,166 presence rows; 6.7MB of board-wide
  lipband capture.  **Maker fees exist per-series**: $0.87 charged total (TRUMPSAY $0.57,
  APRPOTUS $0.21, A100WS $0.07, EURUSD $0.03; treasuries/MLB $0.00).  Fee-aware venue
  pricing is now mandatory — the fee rides on every `fill_obs` row since the dialect fix.
- **Second burst-halt, 00:54 MT, KXAPRPOTUS — and it sat halted 5.5h.**  Same class as
  TRUMPSAY: the replenish re-posted instantly into the flow that ate it.  My watch missed
  it twice over (session watchdog killed by the environment; the VPS cron didn't check the
  halt flag).  Ryan caught it at ~06:20.
- **The structural fix, deployed: `POST_FILL_COOLDOWN_S = 90`** — entry re-posts wait 90s
  after a fill (> B14's 60s window, so the burst breaker is unreachable through the
  replenish; sheds/fully-closing exempt).  Mutation-tested: a flow that eats every lot on
  contact can no longer trip the breaker.  Family bans stop being the tool for this class.
- **The VPS watchdog now pages on the halt FLAG** (the gap), plus dead-service and
  stale-feed.  Cron, session-independent, ntfy.
- **Close cache persisted** (`v5_close_cache.json`): settlement dates are fixed at listing
  but were memory-only, so each of tonight's three restarts re-learned thousands of closes
  at ≤4 req/s — the hour-long ramps.  Restarts now resume warm.  (The websocket remains
  unwired; it streams books only — orders are REST regardless — worth wiring for 6→32
  live-book breadth, not the placement bottleneck.)
- Commits: `651e866` (fills dialect), deny TRUMPSAY, cooldown (690 tests), close cache.
- v5 LIVE and rebuilding; nestor still halted pending a true-feed verification + resume.


## MID-MORNING LOG (2026-07-30, ~07:30-08:30 MT)
- **nestor RESUMED** (~07:45): stop → `./nestor resume` → start; halted=false, bankroll
  $89.39.  Its overnight halt was v5's zero-count feed under-declaring — cause fixed.
- **Startup burst + dailies-first + close cache, deployed**: 7 Hz for the first 10 min,
  close-learns ordered by soonest program end, closes persisted.  Measured: restart now
  reaches 7 venues / 55 slots / $17 resting in 60 SECONDS (was 30+ minutes).
- **THE 1.155 INCIDENT + FIX** (Ryan caught it): held 3 lots of EURUSD 1.155 with $0.26
  accrued; v5 funded sibling 1.153 from zero — cluster ownership was slot-derived and the
  held rung had no slot that cycle.  `owner_seed` now derives ownership from
  positions+orders (restart-proof), ACCRUED rungs first; owner keys carry the SIDE so D9's
  one-side-per-cluster survives.  Tests 693.
- **Rate limits**: the 10 req/s is Kalshi's DEFAULT tier and our own config marks it
  UNDERIVED.  Higher tiers exist on application — **Ryan action: apply to Kalshi for an
  elevated API tier** (LIP participants are the target audience).  Cheapest 3-10x speedup.
- **The cooldown question is RYAN'S OPEN CALL**: 90s live today.  His argument: φ on hot
  rungs is exactly what we must measure; the cooldown censors it.  Counter: each φ sample
  there costs dollars to informed flow; lipband measures sweep rates free.  Options on the
  table: keep 90 / drop to 25 (still breaker-proof, 3.6x sampling) / remove.
- Morning fills continue heavy: ~$200 inventory at peak — caps holding, every fill recorded
  with true size+fee.  φ day one is being written.


## THE 1.155 SAGA, CLOSED (2026-07-30 ~09:30 MT) — and what it taught
Ryan's one screenshot ("1.153 earned 1c, 1.155 earned 26c — put the money on the right
rung") pulled a thread that surfaced FIVE defects, each fixed + tested (tests → 700):
1. Cluster ownership was slot-derived → seeds from positions+orders (`owner_seed`).
2. …whose accrued lookup was slot-derived too → ticker→program now fed from the programs
   feed, request-free, every cycle.
3. Accrual ranked as a boolean → ranked by DOLLARS (1c may not tie with 26c).
4. A pot with no tracked position (pre-archive) couldn't own → accrued-only programs seed
   ownership side-wildcard from the accrual ledger.
5. Displacement covered the plan but not the RESCUE → the forfeit gate's TOP_UP re-entered
   the displaced rung with a 3-lot; both doors now honor seniority.
Plus **SF-4b**: `v5_accrued_overrides.json` — the exchange's DISPLAYED per-market rewards
outrank our model (measured inverted: model $0.186/$0.063 vs display 1c/26c).  Trade API
404s on 23 probed reward paths; the popover rides Kalshi's web-session API — **Ryan lever:
devtools-capture that request once and we get a standing truth feed.**
End state: 1.153 fully recalled.  1.155 unquoted FOR NOW and correctly so: its book has 18
rival contracts against the 1,000-contract qualifying target, so a post there earns zero
credit until depth returns — and funding the gap ourselves is the banned land-grab.  The
owner logic watches; depth's return puts the lot there the same minute.
DOCTRINE (Ryan): **banked accrual is senior capital** — the allocator weights the existing
pot above committed basis, everywhere, both entry and rescue.


## SF-4c LANDED (2026-07-30 ~10:45 MT) — THE ACCRUAL TRUTH FEED
**Ryan found the endpoint in the web app's traffic; the trading key signs /v1.**
`GET /v1/incentives/users/{user_id}/estimates` → `{program_id, reward_centicents}` for every
program with accrual — the UI popover's own source, centicents = 1e-4 dollars.  This is the
capture note 47 §8.1 called impossible (the trade API 404s it; 23 paths probed).
- Polled every 60s, FIRST in the loop (measured: from inside cycle() the verify lane never
  saw a free token — discovery drains the bucket to its reserve every iteration).
- Each poll re-anchors `self.accrued`; the model interpolates between polls; changes ≥ 0.5¢
  persist as `accrual` rows (src=exchange_estimates) so restarts replay TRUTH.
- First live poll: 59 programs re-anchored; book-wide real accrual ~$7.5 by mid-morning.
- The model was measured 2–4× off in BOTH directions (1.153: model $0.186 vs true $0.015;
  1.155: model $0.063 vs true $0.255).  Every accrual-driven decision — owner seats,
  displacement, rescue, cliff sizing, ratchet verification — now runs on the exchange's own
  numbers.  KALSHI_USER_ID in the service env + nestor/.env.
Tests 704.  Commits through `lip-v5-build` head.


## THE SHED STORM (2026-07-30 ~08:15-09:05 MT) — POSITIONS RIDE, FULLY
After the truth-resync adoption, TWO auto-exit paths began liquidating the adopted book at
the opposing best: cutover triage (26 Skubal offered at 3c vs 16c basis), and — found only
after triage was disabled and the sells CONTINUED — `update_shed_targets`' independent
"(★)-fails-now" shed, which crossed to a 95c ask to close an 18c-basis NO (guaranteed
−$2.80/lot).  Ryan caught both from the Orders tab; service stopped; both paths now OFF
(`CUTOVER_TRIAGE_ENABLED=False`, `INVENTORY_AUTO_SHED=False`).  **Doctrine, settled: the
entry equation may not price exits — exit costs the SPREAD (tape: −$40.30, −$123) and the
D4 gate bounds every ride at ≤7 days.  Exits are: day stop, halt closing pass, settlement.**
- The day stop FIRED on the storm (−$22.26: sheds' realized losses + marks) — right call,
  wrong floor: the breach check was the one call site not passing the ceiling, so it used
  the $20 toy floor instead of $60.  Fixed; resumed with operator note.
- The cooldown test's old pass was an ARTIFACT of the shed (its "replenish" was the shed);
  rewritten: a lot eaten in seconds measures huge φ and (★) refuses the rung — the φ
  discipline asserted as itself.
- Triage's flag was frozen as an import-time default arg — a dormant guard with an
  untunable call site; resolved at call time now.
- The TRUE-UP shipped (Ryan: "probe every minute, true up"): recon every 120s adopts
  DOWNWARD divergences silently (hand cancels/sells, settlements — hand intervention no
  longer halts the book); upward divergences freeze + page.  Only explicitly-listed rows
  are judged (truing on absence would zero real books on a partial response).
- **KXTRUMPSAY deny LIFTED** (Ryan: measure, don't ban) — the cooldown/φ/fees/cluster rail
  cover the mechanism.  EARNINGSMENTION + KXRAIN keep real-dollar-tape denies; Ryan's word
  lifts them.
- PELO ($19 position vs $10 cap): burst-night scar — placements passed rails against fills
  the poll hadn't landed yet; the cooldown closes the class.  Ryan may hand-sell any
  position now; the true-up absorbs it.
Tests 704.  v5 LIVE, unhalted, book rebuilding buy-side only.

## HANDOFF (2026-07-30 ~10:20am MT, pre-compact) — read this first after compaction
**LIVE:** v5 running on VPS (commit ~`lip-v5-build` head, 710 tests), unhalted, books TRUE
(resynced from exchange after the closing-flag replay bug starved budget to $0 at a phantom
$315/$300). nestor trading. Truth feed (SF-4c) anchoring accrual/60s. True-up recon/120s
(down-adopts silently, up freezes). Snapshot+reinstate live: any restart redeploys the book
in ~90s. Positions RIDE (triage + auto-shed both OFF). ~15 rungs resting; more with evening
dailies or the $600 ceiling (Ryan may deposit; one constant `MAX_TOTAL_COLLATERAL_USD`).

**A researcher SUBAGENT IS RUNNING in `nestor-wt-lipv5` (DO NOT touch that repo until it
reports via task notification).** Its queue, in order:
1. Six review fixes (adversarial review, full text in this session pre-compact; summary:
   ①None×float crash in admit_venues→halt ②crash-gap fills re-booked on restart (seed dedupe
   from ledger) ③rate-refused reconcile burns its 120s cadence ④replay double-counts 4
   histories (Σ fill_obs for v5 tapes, v4 inference behind flag) ⑤unreadable halt/peak file
   fails OPEN (must fail closed) ⑥recovery sweep parses dead `price` field → $0 collateral).
2. Then TWO Ryan-approved allocator features (constants-free): **pass-2 idle-capital sweep**
   (leftover budget buys $5–10 lots at 0 refills; floor-reachability is the only admission
   test) + **calculable displacement at capacity** (displace worst RESTING rung iff
   E_new > E_worst_remaining + accrued_at_risk, strict; positions never displaced; log both
   sides). Agent flags spec-vs-machinery collisions for Ryan review.

**WHEN THE AGENT REPORTS:** verify suite locally (`cd tools && python3 -m unittest discover
-s lip_v5/tests -t . -q`), review its flagged judgment calls WITH RYAN, then ONE deploy:
rsync tools/lip_v5/ → VPS `kalshi_data/v5/lip_v5/`, `systemctl kill -s KILL lip-v5` +
`start` (reinstate restores the book ~90s). Push commits. DEFERRED by Ryan: review finding
#5-of-report (settlement release missing — fix before tonight's settlements if possible),
#10 (nestor-position freeze/page spam), dormant guards batch. Review's full report is in
the pre-compact transcript; key unfixed items also matter for the $600 raise.
**Standing Ryan rules today:** review findings = discuss first, don't auto-fix; never
hand-place; positions ride; measure don't ban; his UI screenshots outrank our books.

---

## 2026-07-30 ~10:20 MT — fix agent returned

All six fixes + both allocator features landed on `lip-v5-build` (8 commits, `8707e55`..`f87ebad`). **745 tests green locally, verified independently.** Every fix mutation-checked (revert → suite fails on the exact symptom). Not yet deployed — collisions under discussion with Ryan first.

Notable deviations the agent flagged (full detail in session transcript):
- Fix 3 needed a second change: deferred-divergence (`position_divergence_deferred`) so a fill can't freeze its own market during the recon window; can't hide a true divergence.
- Pass-2 funds ONE lot at cliff size (not water-fill); ★ hurdle still binds in pass 2.
- Displacement only fires against incumbents that won't reach the $1 floor — healthy rungs never displaced (anti-churn, free).
- One test invariant changed: "cap below cliff funds nothing" now only true under scarce budget — that IS the feature.

Next: Ryan reviews collisions → one deploy (rsync + KILL restart, reinstate ~90s) → push.

## 2026-07-30 ~10:45 MT — DEPLOYED (8 commits live), pushed

KILL-restart clean. Book survived intact: 7 resting rungs stayed live on the exchange through the KILL, recovery sweep (fix 7's real-dialect parser) adopted them — reinstate correctly had 0 to re-place. `dedupe_seeded fills:4` (fix 2 live). No halt, cycling, nestor untouched.

**Finding: pass-2 funded ZERO while ~$170 of budget sits idle** (budget = 300 − ~$117 inventory basis − reserve; pass-1 spends only ~$5/cycle). Perfect candidates exist (below_cliff_dropped rungs needing ~$5). The feature only logs successes — blocked reasons are invisible. Leading hypothesis: D11 plan_var — 16 clusters carry inventory (several OVER the $10 cap: AAAGASD $27.5, AAAGASW $23), each charged at full container, variance budget likely exhausted → every new cluster refused. Proposed: one-line blocked-reason tally log, redeploy, read the tape. Awaiting Ryan.

## 2026-07-30 ~11:20 MT — basis poison fixed, idle capital MEASURED

**Root cause of the inflated clusters:** gen_adopt_truth.py's fallback fabricated basis=0.5 when
fills didn't cover a position and the book read failed. 55 NO @ fake $0.50 = $27.50 on a ~$6
position. Ryan was right: nothing over $10.

**Fix deployed:** new generator (kalshi_data/scripts/gen_adopt_truth.py) uses the exchange's own
`market_exposure_dollars` / |position_fp| — pure truth, hard-errors on unpriceable rows, no
fallback. Ledger archived (archive-20260730-basispoison), re-adopted 20/20 clean. True inventory
basis **$60.02**. Zero place_refused since restart. pass2_refused logging live (commit pushed).

**The idle $234, measured (alloc_reasons per cycle):** venue_cap 192 · cluster_owned 270 ·
cluster_cap 81 · slot_cap 12 · below_hurdle 6 → spend ~$2/cycle. Pass-2: all 76 candidates
cluster_owned (D5 second-rungs — working as designed). The real limiter is VENUE ADMISSION:
non-admitted venues cap 0, ramp bounded by probe queue (OVERSIZED_PROBE_MAX=2). That guard
predates the estimates truth feed, which now verifies a venue's payout in minutes. Lever to
discuss with Ryan.

## 2026-07-30 ~11:40 MT — sole-qualifier bug + venue universe measured

**The bug, plainly:** our highest-earning venue class (sole-qualifier: S≤0, zero rivals) reads
UNPROBEABLE because floor_q needs a rival score to clear → cap $0 → we refuse to quote where we'd
take pool/2 alone. The 7 unprobeable venues (UST2/7/10/30AD, H100WS, H200WS, RTX5090WS) have
**288 open markets with ZERO quotes and ZERO trades from anyone** — we'd be the only maker.
**Fix (calculable):** when S≤0 the binding floor is the forfeit cliff — probe = smallest lot
earning ≥$1 from pool/2 over the remaining window. No rival needed. Not yet implemented.

**Prices there:** no market prices exist (empty books — we set them). Empirical anchor: our
UST10AD basis averaged 9.5¢/contract.

**Treasury correlation (history check, 13 settled days):** tenor daily directions agree 9/9 to
10/11 across all six pairs — near-perfect. One-RATES-cluster ($10 reserve for all four) is
EMPIRICALLY RIGHT; $50 spread across tenors = 5 clusters of risk on one bet. Quote all four
venues for presence, keep the single cluster reserve.

**Venue universe right now:** 234 venues with in-window unpaid pools, 3,705 markets, ~$299k/window
total. We are funded in 8. Scanner surfaces 15.

**Ladder placement truth (Ryan Q):** score/$ ∝ 1/price ⇒ the allocator consolidates to the
WINGS of a ladder (cheap improbable contracts: YES above spot, NO below), never the middle —
direction-symmetric tails, not one side. Live book confirms: avg collateral side 15.8¢, mode
single digits. **Tension surfaced:** D5 = one rung per cluster, and all four UST tenors are one
RATES cluster ⇒ only ONE treasury rung ever funded, leaving 3 empty-book pools unclaimed. The
$10 cluster reserve already bounds correlated dollars; D5 additionally bounds rung COUNT.
For φ≈0 empty-book venues the calculable answer is: bound dollars (keep $10), relax the
one-rung count inside a cluster. Ryan decision pending.

## 2026-07-30 ~12:00 MT — build round 2 dispatched
Agent (same one that built pass-2) building: A sole-qualifier admission, C empty-book quoting,
B D5′ dollars-not-count. I deploy on its report after suite verify. Ryan approved all three +
deploy.

## 2026-07-30 ~12:20 MT — THE 17-HOUR EXPERIMENT (Ryan asked: working / will work / can work)
$9.13 accrued, 69 of 106 touched rungs earned. Treasuries = 45% of all earnings ($4.07 from
UST10AD+UST30AD). Only ONE program cleared the $1 forfeit floor so far (TRUEV $1.04, banked).
Yesterday's UST batch (~$3.3) ended sub-$1/rung — forfeit-exposed. 48/69 programs are <10¢ dust.
Presence (2.8h tape): median 50%, p25 3% — UST10AD ran 3% presence today because OUR OWN caps
(basis poison + cluster full) suppressed the quote. Per-rung economics in good venues ≈ Ryan's
$2/rung/day. Verdict: mechanism works; spread too thin below the cliff; every bottleneck is
identified and in the current build round. (Build note: agent's Fix A already landed —
sole-qualifier venues ADMITTED.)

**Correction per Ryan (only post-restart, only live estimates):** since the 10:57 MT fixed
restart (0.58h): growth $0.036, run-rate ~$1.48/day, and only 2 of 49 live programs grew
(PANAMAWEEKLY +1.9¢, RTX5090WS +1.6¢) — everything else flat. Caveat: ledger only records
estimate moves ≥0.5¢, so slow rungs are censored (true ceiling ~$11/day). $4.92 of current
estimates sit in ENDED windows (excluded). Live-window estimate mass ($0.58 UMG, $0.54 EPST,
$0.39 MCMORROW…) is ALL sub-$1 → forfeits unless quoted over the cliff before window end.
The fixes in build are precisely the rung-count unlock this measures the need for.

## 2026-07-30 ~13:50 MT — INCIDENT: round-2 recall cancel wave + rollback
Round-2 deploy (sole-qualifier, D5′, band fix) cancelled the entire resting book: the D5′
displacement recall fired on accrual rank alone (owner_accrued > sibling accrued) with no
scarcity condition — 94/105 candidates recalled, requoter q=0 cancelled ~10 rungs. No fills,
no losses. Rolled VPS back to eb2fc4c (stable, unhalted, accrued $9.70 and climbing). BUT the
old build can't rebuild the book either — cancelled rungs sat in position-owned clusters that
old-D5 refuses. Agent is fixing: recall only under genuine scarcity (cluster dollars can't fund
both). Redeploy of corrected round 2 restores both the book and the unlocks. Lesson: the
1.153/1.155 rule is a CONFLICT RESOLUTION, not a standing purge.

## 2026-07-30 ~14:15 MT — corrected round 2 LIVE (a95b11b, 763 green, pushed)
Recall is now a contest resolution (scarcity-conditioned); the wave's shape is a pinned test.
Second latent bug fixed (recalls now free their dollars to the cluster tally). Post-restart:
zero recalls, cluster_owned gone, no cancels, unhalted. BUT rebuild is slow: spent $0.84,
placed 0 — the binding gates on FRESH entry are free_ride_refused (305) and p6_would_refuse
(172). The flushed book had grandfathered these gates. FREE_RIDE_ONLY is now the single gate
blocking BOTH the rebuild and the sole-qualifier venues → the empty-book fork discussion with
Ryan is the critical path to redeploying capital.

## 2026-07-30 ~14:45 MT — book REBUILT ($42.47 resting, 14 orders, above pre-wave $33)
Normal discovery rebuilt the book under the corrected build. Reinstate post-mortem (with new
refusal tally, commit pushed): it runs ONCE at init racing a half-paginated program feed and a
discovery-drained rate bucket — {"no_live_program":16, "rate_budget_exhausted":1}, 21 rungs
never evaluated. Reinstate has NEVER successfully replayed in production (both morning "wins"
were recovery adopting still-live orders). Agent building: re-entrant reinstate (pending list,
adjudicate under current program map until deadline) + snapshot shrink-guard (a collapsing book
can't clobber last-known-good inside a derived freshness window). Close-cache flush bug also
real (every=25 counter resets each restart) — flagged for same round.
