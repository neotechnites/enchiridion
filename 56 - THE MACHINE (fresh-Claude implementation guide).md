# 56 - THE MACHINE — fresh-Claude implementation guide

> You are a fresh Claude taking over the Kalshi LIP maker. READ ORDER: this note →
> [[55 - V6 THE CAPITAL MACHINE]] (the design + every amendment/errata/review record) →
> [[54 - THE ALLOCATOR LAW]] (the law + capital scaling) → [[47 - THE LIP PROBLEM]]
> (the measurements everything rests on). Notes OUTRANK code comments; code comments
> outrank your intuition; nothing outranks the tape. Ryan's rules: answer questions,
> don't act on them; never wait when work is approved; never state clock times unchecked
> (times below are MT unless marked); measure, don't opine; every refusal logs its reason.

## 1. WHAT THE BUSINESS IS
Kalshi pays liquidity rewards: per market per side per program window,
credit = score_share × pool/2 × presence, FORFEITED below $1.00/window. Scoring counts
CONTRACTS (cheap contracts = more score/$). Windows are per-program (feed-driven, mostly
daily). Profit = credits − fill losses. Fill losses are priced by a measured per-price-
band bleed table (calib2.json, 8,240 settled markets; SIDE-split per Lane-A review: cheap
NO ≈ 0.5-0.58 loss/$, cheap YES far less). The program ENDS Sept 1, 2026. Target:
$200/day at $1-2k (currently proving at $600). v5 = safe/small/data at $300; v6 = the
capital machine (marginal queue + quiet-ladder walls).

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

## 6. CURRENT STATE POINTERS (check, don't trust — this note ages)
Note 53 = the running live-state log. Note 55's tail = latest review/fix state (F1-F9
round). The proof gate for scaling: per-fill realized-vs-table at settlement, estimate→
paid conversion, uptime. Known-open: p_fill_implied unmeasured until cluster-days tape;
probe families need a live-board symbol check at deploy (armed-probe-zero-slots pages);
smoothing window derives from recorder data at boot (60s fallback off-VPS).
