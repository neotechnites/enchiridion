# 40 - LIVE STATE (2026-07-26 night → VPS era) — spin-up entry point (supersedes 39)

> **Read order unchanged from 39:** this file → [[21]] → [[23]] → [[38]] → [[24]] → [[19]] →
> [[33]] → [[15]] → [[17]] **R73-R163 = the story.** Then act — never re-derive, re-ask, re-kill.

## WHERE THINGS RUN (Sun 2026-07-26 ~23:25Z)
- **NESTOR LIVE ON THE VPS**: Oracle A1 "senate" **129.146.115.241** (Phoenix, 2 OCPU/8GB ARM,
  free forever, PAYG-exempt from idle reclaim). systemd: `nestor.service` (auto-restart),
  `athena-supervisor.timer` (5min, KillMode=process), `healthwatch.timer` (10min → ntfy.sh
  topic **senate-nestor-2732e947** — Ryan's phone alerts). All 8 athena daemons up. Order RTT
  **40ms measured** (Mac was 170-300). ssh: `ssh -i ~/.ssh/senate_vps_ed25519 ubuntu@<ip>`.
- **Standby micro**: 129.153.223.166 (E2.1.Micro, free) — services disabled, dead standby /
  future capture-redundancy. **Mac**: dead standby, nothing runs. OCI CLI configured on Mac
  (~/.oci) for infra ops. TCC-blindness class: DEAD (Linux).
- **Bankroll $93.09** (peak 106.03, dd 12.2%): first win +6.03 then tonight's FIVE fade losses
  on ONE ETH run that hit NINE straight (-2.22/-3.82/-3.76/-3.15 + 30¢-fade… see R162-163;
  ~1-in-52-per-window event; two correct no-trades >46¢ during it). Daily halt now **35%**
  (Ryan-ordered; derivation in nestor.toml — defect containment, not edge; divergence $2 stays).

## WHAT CHANGED TODAY (all live)
1. **Fitted execution policy** (39 §landed → implemented): rest 10x@40¢ GTD at T0, cancel+IOC
   8x@46¢ backstop T0+45s, 44 gate REMOVED, cancel-response-is-truth machinery, entry_path
   tagging. First live maker episode: crossed_at_post @21¢.
2. **Deep review (R161)**: 4 lanes, 12 fixes live — divergence widens by resting reservations
   (+ resting_collateral.jsonl decides prod's collateral branch), house in the risk regime
   (global-halt stand-down, coid-scoped sweep, cash bridge), partial-maker-cancel, resting-409
   benign, 404-after-expiry ≠ filled, fee truth $0.0001 ceil + exchange fee_cost, house metrics
   repaired, heartbeats/pending-records. NEW API TRUTHS: fee_cost/is_taker on fills; maker
   fills FREE (demo); coid dedupe OUTLIVES orders; cancel reduced_by synchronous truth.
3. **Conditioning verdicts (R163)**: cheap-side-without-streak DEAD; run-detection tells
   unearned (sign REVERSED — macro-driven streaks reverse MORE); bow-out burns edge; take-
   profit DEAD (marks calibrated-to-cheap); 2yr REGIME MAP: bull legs do NOT lengthen streaks,
   no regime cell <50%, June→July decay = SAMPLING NOISE (competition ruled out — reproduces
   in raw spot). Max streaks: ETH 15, BTC 11. Mid-window FV DEAD (market beats model, w*=0).
   **T0-after-streak is the only owned edge in 15m crypto — proven.**

## THE IMMEDIATE WORLD
1. **Monday 3 firsts**: volbook 17:30-18:30Z window (predictions to reconcile line-by-line:
   verify-volbook-execution.md — ≈11 rungs, ≥90% first-IOC fill, ≥2 touches = 2.5σ tripwire,
   red day 14% likely); house first quotable spreads (metrics now truthful); streak under the
   full policy (S-table in review-nestor-sensors.md — maker 24% / backstop 18% / no-trade 58%).
2. **Queued code (small)**: direct-ticker polling (market visible pre-T0 by constructed ticker,
   priced T0+5-10s vs list's T0+6-36s — recovers ~20s of window, probe-proven); repeat-skip
   alarm miscounts post-entry scans (cosmetic); kbt compact() discards ladder (2 lanes blocked
   — real top-of-book capture for 15m crypto).
3. **Weekly Fable review ~Jul 31**: duties per 38 + decay-bench sweep + the S13 ceiling-walk
   evidence (45-46¢ fills) + entry-price-bucket win rates (Ryan's floor question).
4. CALENDAR CORRECTED (SOROS lane 2026-07-27): **Wed Jul 29 = FOMC** (statement 18:00Z,
   Warsh presser 18:30Z) **+ MSFT/META after close**; **Thu Jul 30 12:30Z = GDP+PCE+claims**.
   EVENT-WINGS EVIDENCE COLLAPSED (lane-SOROS-jul27): winning legs were stale-quote artifacts
   (n=1 CPI date), Thu print wings test DEAD (5.0% base vs 1.20x premium), MSFT/META gap wings
   have NO VENUE (no bracket spans after-hours). Replacement door: FOMC-presser +/-1.0% BTC
   strangle Wed 18:45Z bracket (5/16 vs 6.8% base, p=0.004, ~3.8x net) — GATE: take <=20c pair
   ask at 18:45Z, stand down >=30c. Class B, 8 trades/yr. LIP/maker separation post-probe (R153).

## OPS (VPS era — most 39 gotchas dead)
Restart: `ssh ubuntu@129.146.115.241 'sudo systemctl restart nestor'` · logs: nestor/logs/
run.log · state: nestor/data/state.json (one writer) · deploy: build on box (nestor-src/,
cargo 2m33s) or musl cross from Mac (x86 micro only) · demo creds: SECRETS.local.md +
secrets/Demo.txt · subagents: opus researcher/researcher-med ONLY · R132 token doctrine holds ·
API truths: 39's list + fee_cost/is_taker/free-maker-fills/coid-outlives-order/reduced_by.
