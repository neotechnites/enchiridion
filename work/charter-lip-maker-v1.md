# Charter: production LIP maker (v1) — reward-per-dollar optimizer

Date: 2026-07-28 ~01:15Z. Status: SPEC IN PIPELINE (draft → adversarial review → Ryan-visible spec → implement Tue AM).
Origin: R168 scale plan + tonight's measured truth (R172-R176). Supersedes lip_requoter_v3 (probe-grade; retires at 03:45Z exit).

## Ground truth this spec is built on (all measured/confirmed 2026-07-27, do not re-litigate)
- Pools are PER RUNG, $100/day each on gas dailies (UI popover), summing to the event figures in the
  Rewards table (gas daily $1,700 ≈ 17 rungs). Reward window 6:00 AM–9:59 PM MDT (12:00–03:59Z).
- Score per snapshot = size × 0.5^(ticks from best); random per-second snapshots; order must rest the
  full second. Rank cutoff "top 1,000 shares" (= target size). Both YES-bids and NO-bids score.
- **$1.00/rung forfeit floor is real and taxed us ~22% tonight** (earned $6.94, payable ~$5.40:
  rungs at $0.95/$0.33/$0.17/$0.08/$0.01 all burned).
- Estimated earnings are visible live per rung (popover, signed-in) and per order (blue/gray dot +
  efficiency %, web order book). These are the calibration instruments — the binary's share model
  must reconcile against them daily.
- Collateral = price of the side bought → at-best score costs 50-100× less on the cheap (longshot)
  side of every rung. Tonight's config ignored this and pinned a $45 ceiling on expensive sides.
- Fills are pure cost (drift ≈ 5-9¢/cross-cycle pair, measured; inventory = ceiling poison).
- Competition is dynamic: near-empty books at window open (6:00 AM MDT + market open ~10pm EDT),
  walls (1,000+ at best) arriving intraday. First-mover hours are the cheapest share of the day.
- Enrollment: none exists; US member + SSN suffices. Anti-gaming: CRO may revoke for behavior
  "inconsistent with the purpose" of the program — see OPEN QUESTION 1.
- No API exposes pools/estimates (probed: market payload has no reward fields). UI-only.

## Goal (the only one)
Maximize Σ over rungs of payable reward − (drift + fees + adverse settlement EV), per day, per
dollar of collateral, without tripping anti-gaming — across ALL LIP events, not gas only.

## Required behaviors (derive exact mechanisms in spec; every constant must carry its derivation)
1. **At-best-or-one-tick placement.** 0.5^2 = 25% credit makes ≥2 ticks worthless. Requote when best
   moves. Snapshot integrity: orders must rest whole seconds — requote cadence must not shave
   coverage (v3's 60s cancel-repost gap cost ~2%; derive the optimal cadence vs at-best decay).
2. **Cheap-side-first allocation.** Sort every (rung, side) slot by collateral-per-contract at best;
   fill the budget cheapest-first. Expensive-side slots only where projected marginal reward justifies.
3. **Forfeit floor management.** Enter a rung only if projected day-share × $100 ≥ $2 (2× safety on
   the $1 floor). Concentrate: fewer rungs cleared beats many rungs burned. Late-day check: if a rung
   projects $0.60 at 6pm, either top it up past the floor or abandon it — never ride into a forfeit.
4. **Inventory recycling.** Exit inventory when exit cost (spread + fee≈0) < reward-rate ×
   freed-collateral × hours-remaining. Prefer quote-skew (shade the shedding side) over taker exits.
5. **Presence scheduling.** Present at window open (6:00 AM MDT) and at new-market open (~10pm EDT);
   these are measured near-empty. Size up in empty books, taper vs walls (matching a 1,800 wall at
   best buys share linearly at real cost — derive the indifference size).
6. **Scanner (breadth).** Rank all LIP events by pool/competition. Source problem: pools are UI-only —
   spec must design the capture (operator-seeded table from the Rewards page + daily refresh ritual,
   or authenticated scrape if ToS-clean). Seed list from tonight: gas monthly $3,300, Musk $2,600,
   gas weekly $2,100, gas next-month $2,100, EV commodities $1,800, gas daily $1,700, GDP-year
   events 6×$1,400 (Medium/Low competition), share-of-GDP $1,320 (Low), gasoline CPI $1,300,
   Chipotle $1,200. Deploy $300-600/event at the knee; breadth past that.
7. **Caps and risk, inherited from v3's lessons:** decoupled post-size vs refill-cap, per-rung
   inventory cap with stop-quoting-that-side-until-flat, global collateral ceiling (config), poison
   rungs on 409/no-order-id, error budgets, cancel-verify via reduced_by on the CORRECT endpoint,
   local ledger only (never trust resting/positions indexes — R169), coids dot-free by construction
   (engine sanitizes at the wire since 40d4a18), ledger persist + recover on restart WITH filled_cum
   and collateral reconstruction (v3 resets both on restart — fix that).
8. **Daily reconciliation.** Model-projected share vs popover estimates vs paid credits
   (Account→Activity→Credits), written to a ledger every day. Model error >2× for two days = stand
   down and re-derive.
9. **Nestor integration:** runs as a nestor strategy crate (breaker-visible, external_cash dies) OR
   standalone with the existing operator-ledger bridge — spec must pick one and derive it. Subaccount
   (R153) preferred before capital scales past $300.

## OPEN QUESTIONS the adversarial review must settle
1. **Anti-gaming line.** Penny bids at best on longshot sides at scale = capital-efficient quoting or
   revocable "snapshot farming"? The program pays for liquidity that helps takers; a 1¢ bid on a dead
   rung arguably does. Find the defensible line (e.g. only quote sides with genuine two-way traffic?
   cap cheap-side share of total posture?). Revocation loses the WHOLE program — price that risk.
2. Does $1.00 exactly pay or forfeit ("less than $1")? Ryan's popover showed a $1.00 rung — tomorrow's
   credit answers it; spec should not depend on the boundary.
3. Tie-splitting at best (by size, time priority, or pro-rata) — affects wall-matching math.
4. Pool uniformity: is every gas rung $100 or do pools vary within an event? (Two popovers both
   read $100; verify across more rungs before assuming.)
5. Discount factor granularity: "ticks from best" in cents, or price levels? At 1¢ books identical;
   matters on wider books.

## Process constraints
Implementor and reviewer briefs carry the note-23 Part II stanza. No hand-deploys — this binary is
the reviewed replacement for exactly that. Build/deploy: native aarch64 on the VPS from nestor-src
(the Mac cross-compile trap, R174). Tests must cover the money rules (forfeit floor, allocation
order, recycling inequality) as pure functions.
