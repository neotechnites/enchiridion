# build-polylag.md — Poly→Kalshi POLITICS lag watcher (SOROS S2 spec)

Status: BUILD TICKET (capture daemon). Design-only; no execution here.
Author: SOROS burst-2, 2026-07-24. Reuses `xvenue_watch.py` patterns wholesale.

## Thesis (one sentence)
Political/macro news reprices Polymarket's DEEP, attention-heavy book first; the
THIN Kalshi political ladder follows minutes later — so a Poly move is a leading
signal for the same-direction Kalshi close. Fish = inattentive Kalshi political
quoters. Reflexive, NOT latency-racing: the edge lives at MINUTES, not ms.

## Why this is a NEW door (not the dead ones)
- NOT sports xvenue (arbed at every size, seam closed — note 13). Politics xvenue is UNTESTED.
- NOT crypto xvenue (that's BASIS: BRTI≠oracle, 60s-avg≠point — settlement risk, not lag).
- NOT within-Poly whale-copy (dead: converges toward the informed whale).
- NOT intra-venue spike-fade (S3 DEAD this burst — bid-ask bounce, uncapturable).
  ← THIS is the distinction that matters: S3 proved a Kalshi thin-ladder move
  mean-reverts on the MARK but a taker captures nothing. S2 is different physics:
  the signal is a DIFFERENT venue's price (Poly), and the Kalshi move we ride is
  the gap CLOSING toward Poly (continuation), not a same-venue snapback.

## The one unknown that decides buildability (check FIRST, cheap)
Do matched Kalshi↔Poly-US POLITICAL markets even exist with two-sided books?
Kill-fast probe (≤10 min, no daemon): enumerate Poly gamma political markets
(`/markets?closed=false&tag_id=<politics>` — note: gamma `tag=` filter is broken,
use slug queries e.g. `slug=` for known events) and Kalshi political series, match
by event, count pairs where BOTH have OI>500 and a two-sided book. If <5 matched
live pairs → door is structurally thin, DECAY-BENCH the spec. If ≥5 → build daemon.

## Matched-market candidates (verify existence in the probe)
| event class | Kalshi | Poly-US |
|---|---|---|
| POTUS approval band | KXAPRPOTUS (point ladder) | 538/approval market if listed |
| Fed decision | KXFED / KXFEDDECISION | Fed-rate markets |
| election margins | KXMIDTERMMOV, KXHOUSERACE, KXSENATE | 2026 race markets |
| nominations/appointments | KXNOM* | nominee markets |
| shutdown / debt / recession | KXSHUTDOWN, KXRECESSION | same-named binaries |
Politics taker fee: Kalshi 0.04·p(1−p); Poly-US 0.04·p(1−p); Poly maker FREE.

## Daemon design (`polylag_watch.py`, machine #7 candidate)
Reuse from `xvenue_watch.py` verbatim: `curl()` (subprocess, SSL-broken workaround,
3 retries, JSON-key validity check), `kfee`/`pfee` scaled to 0.04, the paper-fill
+ resolution scaffold, supervisor heartbeat pattern.

CADENCE: poll every 30–60s (minutes-scale edge; 8s is overkill and rate-wasteful).
For each matched pair each tick, record a row:
```
{ts, event, k_ticker, k_bid, k_ask, k_mid,
 poly_token, poly_bid, poly_ask, poly_mid, poly_depth_usd,
 gap = poly_mid - k_mid}          # signed, Poly-leads convention
```
SIGNAL (log an episode) when |gap| ≥ THETA (start 4¢, the xvenue floor) AND Poly
is the deeper book (poly_depth_usd > k_depth) AND Kalshi ladder is thin. Direction
= sign(gap). Then track Kalshi's mid over the next LAG window (start 15 min):
paper-enter Kalshi at the touchable ask in the gap-closing direction (fresh print
+1¢, fee in), mark the fill, and record how much of the gap Kalshi closes.

RESOLUTION / verdict fields per episode:
- `gap_closed_frac` = (k_mid_after − k_mid_0)/(poly_mid_0 − k_mid_0) over LAG.
- paper P&L net both fees, exit-on-convergence vs hold-to-settle.
- PLACEBO arm: random no-gap windows on the same tickers — Kalshi drift must be ~0.

## Analysis (what promotes it)
- Lead-lag xcorr Poly↔Kalshi per event (confirm Poly LEADS — if Kalshi leads, dead,
  same as sports where Kalshi was price discovery).
- Edge = mean gap_closed_frac × mean gap, net fees, vs placebo ≈ 0.
- Bar: n≥60 episodes, positive across ≥2 event classes (not one correlated event —
  cluster by event/day like S3 required), honest era or event split.
- Trade Kalshi ONLY (Poly = signal, charter venue rule).

## Traps to pre-empt (learned this burst)
- CORRELATED SAMPLE: one news event moves many rungs/pairs at once → cluster by
  event, count events not rungs (S3 t-stats were 3× inflated before day-clustering).
- STALE-PRINT: use book bid/ask, not last-trade (S3's whole edge was a last-trade
  bounce artifact — a taker entering 2 min later captured nothing).
- Direction attribution: the naive "buy cheaper venue" is weak; the edge is riding
  the LAGGING venue toward the LEADING one (xvenue lesson, note 13: naive +2.3¢ vs
  attributed +8.3¢).

## Cost / disk
~5–20 matched pairs × 1 poll/45s ≈ 10–40k rows/day; compact to episodes only
(never store every tick — disk near full). One heartbeat line/poll to supervisor.log.
