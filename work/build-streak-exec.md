# BUILD: Fitted execution policy for streak (rest-at-40 + taker backstop at 46)

> **You are implementing an INTENT; the spec is evidence of intent, not truth. Enumerate the
> design decisions your code embodies — every constant, limit, cadence, size, timeout, retry
> count, default. Derive each from the goal (maximize E[EV captured] per signal), or mark it
> UNDERIVED and flag it upward — never silently ship it. Where spec and first principles
> diverge, STOP and surface — do not faithfully implement a mistake.** (enchiridion note 23 §II)

## What this replaces
The current live streak entry: fire iff observed ask ≤44, IOC limit 46, 4 attempts × 2s.
Superseded by the fitted Cont-Kukanov policy in `work/verify-streak-execution.md` (READ IT IN
FULL — the derivation, the fill tables, and §5 caveats are all load-bearing), as amended by
the Fable review rulings recorded in note 39:
- **Rest-at-40: accept as-is** (no new population, strictly better).
- **Backstop ships at 46, NOT 48.** The 45-48 window population is NEW (the 44-gate never
  traded it) and the researcher's own flagged risk (conditional win rate in swept/never-dipped
  windows, §5 first bullet) lives exactly there. 46 keeps the ~2.5×-fee cushion at the
  conservative 52% win rate. The ceiling is a DIAL that walks to 48 only on live evidence
  (fills at 45-46 proving the conditional win rate) — make it a config constant, don't
  hardcode it into logic.
- **Maker-entry fills get tagged and tracked separately** (entry-path field in the
  participation record) — the §5 adverse-selection risk is measured, not assumed.

## The policy (from the ledger, with rulings applied)
1. When derive-fourth is decisive at **T0−5-10s**: post a **resting limit BUY, full size, at
   40¢** on the reversal side. Resting = `good_till_canceled` + future `expiration_ts` +
   `taker_at_cross` (the demo-proven combo from the house-probe build, R149; V2 REQUIRES
   time_in_force). Set expiration_ts ≈ end of the entry window so a dead process leaves
   nothing meaningful (enforcement is lazy ~2-3min — accepted at this size, same as house).
2. **Cancel-on-flip**: if the official result contradicts the derivation, or the 4-streak
   fails to confirm at T0, cancel immediately (cancel response is truth; the resting-orders
   list is eventually-consistent — never poll it for truth).
3. **Backstop**: if unfilled by **~T0+45s** (still inside the entry window, ttc guard intact),
   cancel the resting order, then IOC marketable-limit at **46¢**. Ask >46 all window = the
   correct no-trade.
4. **Late-signal path** (derivation not decisive pre-T0, ~20%): no maker leg; taker-only at
   ≤46 within the window (current retry machinery is fine here).
5. Paper mode: keep observed-ask pricing for the taker path (honest), and model the maker leg
   explicitly so paper stays comparable.

## Constraints and facts you must honor
- Sizing: flat $4 per entry until 8 clean fills under this policy (R148). At 40¢ that's 10
  contracts; derive and document what the backstop size does (same contracts vs re-derived
  at 46 to hold $4 — derive it, don't default).
- Signal gate: the old "observed ask ≤44" selectivity gate is SUPERSEDED by this policy's
  price levels themselves (willingness-to-pay is expressed by the 40 rest and 46 ceiling).
  If you believe any residual gate should remain, derive it and surface it — do not silently
  keep or drop it.
- Maker fills pay maker fees (~¼ taker, often exempt) — fee accounting must use the actual
  average_fee_paid from the fill response (already the pattern).
- Distinct coids per attempt (-r{n} suffix pattern, 409-safe). Duplicate coid → 409.
- The ws book (STREAK_WS flag, currently shadow) is NOT a dependency — REST is the floor. If
  ws makes cancel-on-flip or the T0 confirm faster, wire it behind the existing flag only.
- One state-writer lock: use NESTOR_STATE_PATH override for any side-run testing.
- Demo-first: prove the rest→cancel→IOC sequence end-to-end on demo before the build is
  called done (demo key id in enchiridion/SECRETS.local.md, PEM in nestor/secrets/Demo.txt).
  Demo probes have caught 4 API truths so far; assume the docs are wrong until demo agrees.
- Tests + clippy clean. Commit on a branch (`streak-exec-policy`); do NOT merge — Fable
  reviews and merges.
- Participation record: every entry logs entry_path (maker_rest | taker_backstop |
  taker_late), attempt prices, and per-retry book snapshots stay (diagnostic, R154).

## Report format
Enumerated decision list (DERIVED / JUDGMENT / UNDERIVED, per note 23 §II) → what demo proved
(including any new API truths) → test/clippy status → branch + commit → open risks. Brief.
