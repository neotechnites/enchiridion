# VERIFY: ops-first-principles.md — adversarial pass (2026-07-27, pre-funding)

> Brief: break the map. Find the money flow, process interaction, or 72h scheduled event it
> misses or hand-waves. Read-only: no ssh, no orders, no changes. Method is code, not opinion —
> every finding below cites the line that produces it.
>
> **Headline: the map's §I ledger table is wrong in one structural way.** It says flow 1 is seen
> by the risk ledger and flows 2/4/6 are seen by external_cash.jsonl. In fact
> `reconcile_exchange_truth` §1b adopts **every** position in the account into the risk ledger
> with no series filter, so flows 2 and 4 land in **both** ledgers. Six of the ten findings are
> downstream of that one line.

---

## RANKED FINDINGS

### F1 — Orphan adoption swallows side-operation positions (the missing leg)
`crates/engine/src/reconcile.rs:268-302` iterates the **whole account's** positions and calls
`risk.adopt_orphan` for anything not in local state. No series filter, no coid filter, no
allowlist. LIP gas rungs and Wednesday's manual strangle are in the same account, so nestor
adopts them. `adopt_orphan` (`risk.rs:524-553`) then:
- `day_spent += count × entry` → they eat the **$60 daily flat budget**
- pushes into `state.open` → they eat `total_at_risk` → the **0.50 × bankroll portfolio cap**
- `pending_payout()` = Σ count (`risk.rs:578`) → each contract adds **$1 of positive breaker
  tolerance**
- `settle()` books their P&L into nestor's **bankroll, peak and day_loss**
- each adds one `market()` call to every 60s reconcile pass (rate budget the map says has no slack)

**Consequence:** side-operation cash is double-counted (external_cash shifts `expected_cash`
down; adoption shifts it down again), side-operation risk consumes nestor's risk capacity, and
side-operation P&L moves nestor's kill-switch inputs. **Cheapest fix:** a series allowlist in
§1b — adopt only tickers whose prefix is a nestor series; log-and-alert on the rest. ~3 lines,
kills F1/F2/F6 together.

### F2 — Silent capacity starvation Tuesday night (highest probability of actually firing)
`nestor.toml`: `daily_budget_usd = 60`, `max_portfolio_frac = 0.50`, `cluster_cap_frac = 0.15`.
At bankroll $93.09 the portfolio ceiling is **$46.50** and a cluster ceiling is **$13.96**. The
map's own calendar puts a **$300 LIP window + arm C** on Tuesday night, concurrent with the
overnight streak episodes it calls the headline event. Via F1, adopted LIP stake goes straight
into `total_at_risk` and `day_spent`; ~5 rungs of one-sided fills exhaust $46.50.

`risk.rs:400-420` then returns `Rejection::DailyCapHit` / `PortfolioCapHit` on every streak
signal. **This is not a halt.** `state.halted` stays false, the process stays up, bankroll does
not move — so none of health_watch's three checks fire, and nothing in the map's alarm chain
distinguishes "capped out" from "no signal tonight". Nestor looks perfectly healthy and earns
zero through the window. **Cheapest fix:** F1's allowlist; plus an explicit alert on a
cap-class rejection (today it is an ordinary numeric skip).

### F3 — The tolerance collapse the map schedules for Tuesday morning
Positive tolerance today = `$2 + Σresting + pending_payout + ext_pending` ≈ **$189** (reproduced
from code, see CLEAN #4). Every bookkeeping error in external_cash is currently invisible
underneath it. The map's calendar removes **both** terms within a few hours of each other:
metals settle Mon 21:00Z and gas settles Tue ~04-05Z (`pending_payout` → ~0), and "reconcile
ledger to actuals Tue AM" deletes the $130. The moment the mask lifts, any residual offset >$2
— including F1's double-count — becomes an immediate halt.

The map treats the Tuesday reconcile as a bookkeeping chore. It is actually **re-arming the
breaker against a number nobody has audited**, at ~05:00Z, into the dark alert chain.
**Cheapest fix:** before deleting anything, run one pass with pending forced to 0 and read the
logged Δ; and log the ext terms at all (see F4 — they currently appear in no log line).

### F4 — external_cash reads as zero on any failure, silently, and is never logged
```rust
if let Ok(text) = std::fs::read_to_string("data/external_cash.jsonl") {   // reconcile.rs:516
```
Relative path, no `else`. Wrong cwd, a permission change, a rename, a deploy that launches the
binary from another directory ⇒ `(0.0, 0.0)` — **exactly the pre-fix state that caused incident
#3** — and nothing distinguishes it in the logs: the divergence-OK line prints `expected_cash`
but never the ext contribution or the line count.

Same function, the other direction: there is **no cap, no expiry, no sign check, no
reconciliation** on entries. A typo of `-110.07` for `-11.07` hides $99 of genuinely missing
money forever, on the *tight* side of the breaker. This is the honest answer to the "$189 of
tolerance" question — the negative side is only tight relative to a number a human typed and
nobody re-reads. **Cheapest fix:** log `external_cash: n lines, Δ$X, pending $Y` every pass, and
treat a read error (as distinct from NotFound) as alert-worthy. ~4 lines.

### F5 — The breaker structurally cannot see a position that DISAPPEARS
Adoption is one-directional: exchange → local. Grepped the crates — there is **no local →
exchange check** anywhere. If a tracked position is closed by anything other than nestor (the
requoter touching a shared ticker, a manual flatten, the restricted key, an exchange action,
theft), the proceeds land as cash: real > expected by ≤ $1/contract, and `pending_payout` — still
Σ count, because local state still holds the position — covers it **exactly**.

So the real theft/bug that $189 of positive tolerance hides is not sized $189: **liquidate-to-cash
is invisible at any size, permanently**, because the widening term is computed from the very
positions the attack removes. **Cheapest fix:** in §1b, after adopting, assert every
locally-open ticker still appears in the positions read with count ≥ ours; alert on any that
does not. ~10 lines, uses data already fetched — zero extra API calls, zero rate budget.

### F6 — `WORST_CASE_ENTRY_CENTS = 99` is safe for $4 trades and catastrophic for penny ones
`risk.rs:538`. Arm C is **1,000 lots NO @1¢** and the map schedules it for Tuesday night. If the
position row lacks a cost basis — `parse_positions` explicitly filters `market_exposure > 0`
(`kalshi.rs:1045`) and yields `None` — adoption books it at 99¢: **stake $990 on a $93 account**.
`day_spent` $990, `total_at_risk` $990, `pending_payout` +$1,000, and on a settlement loss
`bankroll -= 990` ⇒ negative bankroll, drawdown ≥100%, permanent sticky halt, corrupted
state.json. Note the *correctly*-adopted version is also bad: +1,000 contracts of
`pending_payout` puts positive tolerance an order of magnitude above total equity — the positive
breaker is simply off for the duration. **Cheapest fix:** F1's allowlist; failing that, refuse to
adopt any position whose worst-case stake exceeds `max_portfolio_frac × bankroll`, and alert.

### F7 — The requoter's "±10 per rung" is a NET cap and does not bound gross cash
Derived, not observed — `lip_requoter.py` is VPS-only. Arms A/B rest 10 lots on **both** sides of
each rung (`probe-lip-gas.md` §2), so a two-sided fill nets the rung to zero. Any inventory
check reading net position sees flat and re-quotes a fresh 10/side; the pair collateral (~$0.99
per pair) goes out again each cycle. The only bound left is the 40-order cap:
**40 × 10 × $0.99 ≈ $396 of gross collateral against a $93.09 balance.** The map states the caps
without ever multiplying them against the balance. Terminal state: LIP posts start rejecting,
*nestor's* orders start failing for insufficient funds, large negative divergence, halt.

**This is the single highest-value thing to check on the box** (2 minutes): does the rung
inventory cap read net or gross, and is there a global collateral ceiling? **Cheapest fix:** a
hard `MAX_TOTAL_COLLATERAL` derived from the live balance (≈$35 tonight), checked before every
post.

### F8 — 03:50-03:59Z is unmonitored, and §V gate G4 contradicts §III's "by construction ✅"
The requoter exits 03:50Z; orders are GTC with `expiration_ts` 03:55Z; the market closes 03:59Z.
**§V lists G4 — "expiration_ts GTC + cancel on V2" — as an UNPROVEN capability requiring a probe
before funding, while §III asserts tonight's expiry is handled "by construction ✅".** Those
cannot both be true. If expiration_ts is silently ignored (precisely what G4 exists to test),
orders rest to the close with no requoter watching. A fill there moves cash with nobody to
append to external_cash; the fill-checker is a 10-minute cron so detection lags past the close;
the 60s divergence check halts nestor within a minute.

Then: sticky halt at ~04:00Z → ntfy topic Ryan is unsubscribed from → health_watch's only
halt-alert path is that same dark link → **nestor sits halted through all of Tuesday**, missing
the 12:00Z gas lists and the Tue-night $300 window, noticed only when Ryan happens to look.
This is the 72h calendar item whose failure path pages nobody. **Cheapest fix:** pre-load a
negative external_cash entry sized to the maximum remaining one-sided exposure *before* 03:50Z
(it only shifts the expectation in the direction fills actually move cash), and explicitly
cancel-all-LIP at 03:50Z rather than trusting expiration_ts.

### F9 — The deposit procedure keeps the bot alive and leaves the money unusable
Flow 5's fix (external_cash `+1000` at deposit) is arithmetically correct and holds Δ=0 — but
`state.bankroll` stays $93.09. `flat_usd` $4, `daily_budget_usd` $60, cluster cap $13.96,
portfolio cap $46.50 are unchanged: **the $1k sits idle and nestor trades exactly as it does
today.** The obvious operator fix — bump `state.bankroll` to $1,093 — is worse and undiscussed:
`max_drawdown_frac` and `daily_loss_limit_frac` are fractions of `peak`, and `peak` only
ratchets in `settle()` (`risk.rs:623`). Immediately after a bump,
`daily_limit = 0.35 × 106.03 = $37.11` governs a $1,093 account (3.4% — fires on noise) while
the drawdown check reads 0% against a stale peak. G1-G6 cover the reward unit, the subaccount,
the key and the ntfy chain; **none covers what the deposit does to sizing or to the four risk
constants.** **Cheapest fix:** add **G7** — deposit day re-derives `flat_usd`,
`daily_budget_usd` and `peak` in writing against the new equity, with the §IV five answers
attached.

### F10 — The alert chain may have a second dark link, and cannot see F2 at all
The Mac copy of `health_watch.sh` alerts via `osascript` + a local logfile and contains **no ntfy
call**. R162's cutover was a blanket `/Users`→`/home` patch of 287 scripts — a path rewrite does
not add an ntfy POST, and `osascript` does not exist on Linux (swallowed by `2>/dev/null`).
Either the VPS copy was separately edited (unverifiable from here) or the whole chain terminates
in a logfile nobody reads. Separately, its three checks — process alive, halted flag, bankroll
drop >$10 — **cannot detect F2's silent starvation** even if ntfy worked. **Cheapest fix:** one
grep on the box (`grep -c ntfy ~/kalshi_data/scripts/health_watch.sh`), and add a fourth check:
zero entries in the last N hours while markets were open.

### Minor (noted, not ranked)
- `kalshi.positions()` (`kalshi.rs:499`) sends no cursor and handles no pagination. Fine at 5
  positions; at maker-binary scale the tail of the account silently stops being adopted or
  audited while the balance comparison still covers the whole account.
- Every adopted orphan adds a `market()` call per 60s reconcile pass. The map's rate-budget
  section counts pollers only; it does not count the reconcile fan-out that adoption creates.
- A **voided** market never settles: `settlement_won` returns `None` and the position stays in
  `state.open` forever — permanently inflating `total_at_risk` (portfolio cap) and
  `pending_payout` (positive tolerance). No staleness alarm exists on open positions.

---

## VERIFIED CLEAN (with method)

1. **Startup sweeps do NOT cancel LIP orders.** Read `streak::sweep_orphan_rests`
   (`crates/streak/src/strategy.rs:778`) — filters `SERIES.iter().any(|s| o.ticker.starts_with(s))`;
   `house::cancel_all_house_orders` (`crates/house/src/strategy.rs:207`) — filters
   `is_house_order`; volbook has no sweep at all (grep for `resting_orders` in `crates/volbook`:
   no hits). **So the (d) scenario is benign**: a 2am VPS reboot or a crash-restart does not kill
   LIP presence. The requoter is not under systemd, so presence simply freezes at the last quote —
   a reward-score degradation, not a cash event.
2. **Restart cannot lose or double-apply external_cash.** `read_external_cash` is called inside
   every reconcile pass (`reconcile.rs:320`), not cached at boot — it is a stateless per-pass file
   read (`reconcile.rs:506-535`). Restart semantics are correct.
3. **The deposit entry's sign is right.** `expected_cash = bankroll − total_at_risk + house_cash`
   (`risk.rs:562`) `+ ext_cash` (`reconcile.rs:328`). A deposit raises `real_cash`, so `ext_cash`
   must be `+deposit`. The map's "entry equal to the deposit" is the correct direction.
4. **The map's $189 reproduces exactly from code.** `breaker_threshold` (`reconcile.rs:66-72`):
   positive side = `$2 + Σresting + (pending_payout + ext_pending)`, negative side = `$2 + Σresting`
   with no widening. `pending_payout` = Σ count over open (`risk.rs:578`). $2 + ~57 volbook
   contracts + $130 ≈ $189. The asymmetry is implemented as documented; the negative side is
   genuinely tight (subject to F4/F5).
5. **A LIP settlement loss cannot trip Monday's daily-loss halt.** `begin_day` uses the ET date
   (`reconcile.rs:155-165`); gas closes 03:59Z = 23:59 ET Monday and settles 04-05Z = 00:00-01:00
   ET Tuesday, so `pos.day != state.day` and `settle` excludes it from `day_loss`
   (`risk.rs:600-650`). Day attribution is correct.
6. **Malformed external_cash lines fail safe.** Bad JSON is skipped with an `eprintln`; missing
   fields default to `0.0` rather than poisoning the sum (`reconcile.rs:506-535`). The failure
   mode is F4's *absent* file, not a corrupt line.
7. **A netted-flat rung is invisible to adoption** (a fact, not a bug, but load-bearing for F7):
   `parse_positions` drops `net == 0` rows (`kalshi.rs:1029`), so a fully two-sided-filled rung is
   never adopted and its cash effect appears only as raw divergence.
8. **"Halts within 60s" is accurate.** The settlement task loops on a 60s sleep with panic-catch
   and rate-limit backoff (`nestor_bin/src/main.rs:368-395`). Flow 4's stated reaction time holds.

## NOT VERIFIABLE FROM HERE (F4, F7, F10 are derived, not observed)
`lip_requoter.py`, `lip_probe_runner.py`, `lip_book_sampler.py`, `lip_fill_check.py` are VPS-only
— the Mac has only `lip_screen.py`. Also unread: the VPS `health_watch.sh`, the live
`data/external_cash.jsonl` and `data/state.json`, and the systemd unit's `WorkingDirectory`.
