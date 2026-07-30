# 53 - LIVE STATE — 2026-07-29 ~23:00 MT, note-52 build done

> Read [[42 - SPIN-UP]] → [[52 - THE LIP STRATEGY (settled with Ryan, 2026-07-29 night)]] →
> this.  Supersedes [[50]]'s state half.  The strategy is note 52; this is where the build
> stands against it.

## STATE
- **v5 STOPPED, NOT DEPLOYED.**  Branch `lip-v5-build` at `0f576a1`, pushed.  **683 tests
  green.**  Arming requires Ryan's hand-written `v5_go.json` (G3) — deliberately not mine.
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
