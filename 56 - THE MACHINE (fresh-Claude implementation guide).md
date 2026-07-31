# 56 - THE MACHINE — fresh-Claude implementation guide

> You are a fresh Claude taking over the Kalshi LIP maker. READ ORDER: [[42 - THE SENATE
> (the system)]] FIRST (the laws bind this lane) → this note → [[53]]'s CURRENT block.
> Then PAGE IN by task, never whole: [[55 - V6 THE CAPITAL MACHINE]] (design + amendments +
> deploy-day record) · [[54 - THE ALLOCATOR LAW]] (the law + capital scaling) ·
> [[47 - THE LIP PROBLEM]] (the measurements). Notes OUTRANK code comments; code comments
> outrank your intuition; nothing outranks the tape. Ryan's rules: answer questions,
> don't act on them; never wait when work is approved; never state clock times unchecked
> (times below are MT unless marked); measure, don't opine; every refusal logs its reason.
> VAULT LAW: never create a new numbered note — EDIT the note that owns the topic
> (dated line, delete what it supersedes). The archive is git history, not more files.

## 1. WHAT THE BUSINESS IS
Kalshi pays liquidity rewards: per market per side per program window,
credit = score_share × pool/2 × presence, FORFEITED below $1.00/window. Scoring counts
CONTRACTS (cheap contracts = more score/$). Windows are per-program (feed-driven, mostly
daily). Profit = credits − fill losses. Fill losses are priced by a measured per-price-
band bleed table (calib2.json, 8,240 settled markets; SIDE-split per Lane-A review: cheap
NO ≈ 0.5-0.58 loss/$, cheap YES far less). The program ENDS Sept 1, 2026. Target:
$200/day at $1-2k (UNPROVEN — machine currently DOWN, see §6). v5 = safe/small/data at
$300; v6 = the capital machine (marginal queue; after deploy day: rank-truncation dials +
sticky book + measured-rate loop + seed mode).

## 2. THE CODEBASE (repo nestor, worktree ~/Documents/senate/nestor-wt-lipv5, pkg tools/lip_v5/)
Branches: `lip-v5-build` = LIVE v5 (frozen fallback once v6 boards); `v6` = approved v6
merge target; `v6-build` = the working v6 (Opus lane + grafts); `v6-f` frozen (Fable lane,
its winning ideas grafted). Suite: `cd tools && python3 -m unittest discover -s lip_v5/tests -t . -q`.
Module → law map:
- `runner.py` — outer loop: scan → classify → slots → engine.cycle. Startup NEVER adopts
  orders (safety law); ledger replay rebuilds positions/cash only. Retirement diff gated
  on `scanner.last_scan_complete` (absence in a partial map is not evidence).
- `scan.py` — programs feed → classify (book reads, top-K by need-reference incl. accrual)
  → build_slots. Carries φ shrinkage (`phi_posterior`, leave-one-out priors, k=μ/v).
- `alloc.py` — THE LAW: `law_need` (capital-to-target from pool/S/time/φ/accrued),
  `law_rank`/`law_sort_key` (ONE ordering, AST-guarded), two-sided seats (shared envelope,
  split-guarded oversize), bleed charged in the ranking (`effective_usd`), skip reasons
  enumerated — every skip logged with numbers.
- `bleed.py` — g table derived from `data/calib2.json` (side-split, PAVA-on-totals,
  N_MIN=300, g≥0 clamp). Reproducible by test from the file.
- `marginal.py` (v6) — the queue: entry blocks at cost-to-target, deepen lane gated by
  evidence (own-exposure > k AND ≥3h — the quiet-afternoon incident's lock), switch toll
  (stranded sub-cliff accrual + transit presence), banked accrual SUNK in entry (Lane-A
  fix). Heap-POP is the single enforcement point.
- `dials.py` (v6) — C → p(funded mix, anchored 0.09@19.7¢ ref, ratio-coupled) →
  N = max(30, formula) → rail = C/N. `p_fill_implied` logged for falsification.
- `quiet.py` (v6) — family-pooled zero-fill evidence opens untouched strikes; any fill
  disqualifies; asserted φ disqualifies. Licenses presence, never manufactures it.
- `probe.py` (v6) — the 120/480 boot probe (treasury walls + gas wings, wings/walls only),
  cap = d×C expression, lane accounting seeded with position basis, rail exemption
  expressed ONCE and visible at place() (Lane-B fix F5), verdict = earned vs PREDICTED.
- `alarm.py` (v6) — bug alarm replaces loss stoppers: halts only on model-INCONSISTENT
  losses. Variance never halts the earner.
- `engine.py` — Maker: place/cancel (one path to the wire), reconcile (120s: positions
  true-up, settlements → capital release, order sync one-directional), estimates poll
  (60s, per-program accrued = REVENUE GROUND TRUTH), cluster rails at place() (the rail
  has ONE expression — the plan may never propose what place() refuses).
- `guards.py` — halts, day stop (v5) / alarm (v6), B14 burst breaker (3-in-60), place
  rails, cross-bot exclusion (nestor shares the account!).
- `clusters.py` — settle-source grouping + worst-case dollar math (mark, else basis).
- `exchange.py` — real wire dialect (count_fp, *_dollars strings) + FakeExchange test
  harness (insta-fills, takes, settlements). presence.py = exposure meter (contract-hours
  — the φ denominator). cashfeed.py = shared-account cash truth published to nestor.
  cutover.py = v4 adoption relics (triage is measurement-only; nothing places from it).

## 3. SAFETY LAW (owner-dictated, never relax)
Never sells. Positions ride to settlement. Halt = cancel OWN orders only (coid/books
scope — the account is SHARED with nestor) then idle; persisted halt refuses restart
until an operator resume note. Startup ignores ALL inherited orders (book starts empty).
Nothing is cap-exempt. Every constant derived or owner-cited. Every refusal logged.
Placements ≥30s apart per rung structurally (plan-cancel min-resting-life). Instant fills
(fill_count on place response) start the cooldown AT PLACE TIME.

## 4. OPERATIONS (VPS ubuntu@129.146.115.241, key ~/.ssh/senate_vps_ed25519)
Layout: code kalshi_data/v5/lip_v5/ · data ~/nestor/data/lip/ (v5_ledger.jsonl = THE TAPE:
place/cancel/fill_obs/accrual/settlement rows; v5_halt.json; close cache; presence jsonl)
· competition recorder ~/kalshi_data/competition/deltas-*.jsonl.gz (every book change,
all reward markets, @reboot cron) · calib2.json (the calibration set).
DEPLOY = `rsync -az --delete tools/lip_v5/ VPS:kalshi_data/v5/lip_v5/` then STOP-then-
START (`systemctl stop` cancels own orders + flushes; verify 0 own orders resting via the
orders API before start if paranoid; KILL preserves orders — do NOT use it under the
ignore-inherited law or you mint orphans). HALT RESUME = python:
guards.HaltState(path).resume("operator(you, reason): root cause…", now) — always with the
root cause; then reset-failed + start. Watchdog cron pages ntfy topic senate-nestor-2732e947.
The estimates endpoint: GET /v1/incentives/users/0db0997e-.../estimates via
runtime.signed_v1 (KALSHI_USER_ID env in the service; raw /v1 signing, no /trade-api
prefix). Kalshi UI numbers OUTRANK our books — Ryan's screenshots are ground truth.

## 5. THE BUILD PROCESS (how changes happen here — Ryan-mandated)
1. Decision derived WITH Ryan → written to the vault note FIRST (his words verbatim).
   The note is the spec; builders read the note, not your summary.
2. Two independent builders (blind to each other) for big changes; one for surgical.
   Isolated worktrees, local commits, mutation-checked tests (revert → named test fails
   on the exact SYMPTOM — dollars/orders/logged reasons, not internals), suite green at
   every commit.
3. Adversarial review (Fable): re-runs mutations personally, greps for silent paths and
   cap leaks, adjudicates forks against the note's words. For deploy-grade: THREE lanes
   (theory / implementation / red team), NINE approvals required. Reviews run on FROZEN
   commits only (never a tree being edited — learned 2026-07-31).
4. DEFEND verdicts: reproduce every claimed defect yourself before accepting; refute
   with receipts or concede with proof. Your confidence is a hypothesis, never a gate.
5. Deploy only after review + (for capital changes) Ryan's word. Record everything in
   the vault immediately (standing order).
6. **DEPLOY GATE #0 (permanent, 2026-07-31): v6 never deploys blind.** `lip_v5.dryrun`
   prints the EXACT production would-buy book against the live board offline; WE verify it
   against expectation before anything runs. Ryan is NOT an approval step — the burden is
   ours; he only ever sees a machine already doing the right thing. Nothing runs until
   Ryan says.

## 6. CURRENT STATE POINTERS (check, don't trust — this note ages)
Note 53 = THE live-state note, updated in place; its CURRENT block is the truth. Note 55's
tail = the 2026-07-31 deploy-day record: seven deploys, all pulled by Ryan; **v5 AND v6
both STOPPED; v6 disabled (cannot return on reboot)**. Deploy gate #0 (§5.6) is the
re-entry condition. Open questions, in order: (1) do seed orders emit on the wire (fix
committed, untested live); (2) what book the corrected v4-rank chooser builds (needs ~4
undisturbed min from cold boot); (3) THE open question of the program — the estimator is
unvalidated and the tape contradicts it ($19.7 projected gross → ~$2 payable; prime
suspect: competition-depth S mismeasurement). The proof gate for scaling: per-fill
realized-vs-table at settlement, estimate→paid conversion, uptime.
