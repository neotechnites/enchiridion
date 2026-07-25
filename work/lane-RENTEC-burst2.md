# Lane RENTEC — burst 2 (2026-07-24)

Archetype: RENTEC (weak-signal stack on ONE family, 15m crypto native).
**Steer (burst 2):** "Your PROMISING (R1) is window-limited (one day). Gunzip the kbt archive files and
EXTEND across every captured day; report widened n + power analysis. If it survives widening, top
promotion candidate of the night."

## Result in one line
**It did NOT survive. Widening + de-contamination proved burst-1's R1 flagship was a KBT source-frozen
book artifact. Structurally DEAD. Burst-1's PROMISING is RETRACTED.** Same artifact also kills a new
leader-persistence idea that first looked like +31c free money. Net: the entire near-close 15m-crypto
price-TAKING lane on KBT data is untradeable until a live-book source exists.

## The data I gained
Gunzipped `kbt_books_*.jsonl.gz` = **Jul 21-23** (887 mkts); uncompressed `.jsonl` = **Jul 24** (burst-1's
window, 242 mkts). Deduped by ticker → **1,112 markets across 4 days**, 5 coins. This gave a genuine
per-day block holdout (freeze Jul24 = burst-1's era, test Jul21-23 unseen).

---
## R1-EXTENDED — depth-imbalance → direction, widened to 4 days  → **DEAD (structural artifact)**
**Fish (claimed):** informed money stacks resting bid depth on the side it expects; book prices off coin
position alone. **Kill that fired:** burst-1 never filtered KBT source-frozen books (Mesh: ~48% of KBT
markets carry a stale orderbook field — coin_price updates per snap, book does not).
- **Naive replication (no book-validity filter), all 4 days:** n=526, win 56.7%, EV **+5.4c** — already
  shrunk from burst-1's Jul24-only +12.7c. Per-day 3/4 positive, Jul21 −2.5c (n=52 noise).
- **DE-CONTAMINATED (require positive spread + both quotes in (0,1) = a transactable book):** n=414, win
  **48.3%, EV −12.5c (−4.9σ).** avg entry flips 0.323→**0.592** — proof the "edge" rows were crossed/stale
  books quoted at fictional sub-bid prices (as low as 0.01 in a near-50 market).
- **Every slice negative on valid books:** per-day −13.8/−9.5/−16.0/−12.5c (Jul24 IN = OOS = −12.5c
  identically); per-coin btc−8.7 eth−3.9 sol−7.9 xrp−12.3 doge−28.7; spread buckets tight−3.2 / mid−6.7 /
  wide−17.9. Buy-cheap-side −11.1c. **No gate rescues it — uniform structural loss = paying spread+fee on
  a coinflip.**
- **The "R4 tight-spread +34c/9σ" cell was CROSSED books** (negative spread passed a `>0.03` filter);
  de-contaminated it's −3.2c. Dead.
Files: `/tmp/rentec_extend.py`, `/tmp/rentec_clean.py`, `/tmp/rentec_debug.py`.
(Note: `/tmp/rentec_tight.py` had a Python late-binding closure bug — discarded.)

## R9 (new) — physical-leader persistence (collide with lock-edge: 60s-avg settle → late leads stick)
→ **DEAD (structural artifact — stale-book lookahead)**
**Rule:** at T-60s buy the side the coin is already on (coin vs strike). **Looked spectacular:** n=452, win
**93.4%, EV +31.3c**; margin>12bp = 99.6% win, +36c; per-day & per-coin ALL +34-48c.
**The tell:** big-margin leaders priced at avg entry **0.618** — a coin 15-49bp from strike with 60s left
should be ~0.90+ on a fresh book. **Freshness test = total kill:** 224/225 edge markets have **FROZEN
books** (leader ask byte-identical T-60→T-10, e.g. 0.52→0.52, churn=1 distinct quote in the whole final
minute; 0/224 reprice ≥5c). The single ACTIVE book was already priced 0.998 (EV +0.2c). Across ALL 452
near-close markets only **1** has a live book. → you are "buying" at the stale OPENING quote with CLOSING
coin info = lookahead-via-stale-data. Not fillable. Files: `/tmp/rentec_leader.py`, `/tmp/rentec_stale.py`.

## Gate-hunt (mandatory before DEAD) — active-book & mid-window regimes → **also DEAD**
- **Active-book gate:** require churn>1 (live quotes) at entry. Leader-EV on active books only: n collapses
  to 1-6 markets at every lead, all avg entry 0.68-0.998 (already correctly priced), EV +0.2 to +3.5c.
- **Mid-window gate (enter earlier, more time = livelier book):** active-frac ~0.48 measured over ALL
  markets, but the intersection (active book ∩ near-50 tradeable) is ~empty — active books are the
  far-from-strike/already-resolved ones churning 0.95-1.00. No fillable near-50 mispricing at ANY lead
  (T-420→T-60). File: `/tmp/rentec_midwindow.py`.
- **Verdict:** the frozen ~52% carry 100% of the apparent edge and are non-transactable; the live ~48% are
  fairly priced. There is no regime where a fillable KBT crypto book misprices the settle. Structural.

## Power analysis (as the steer asked)
Moot for R1 (edge is negative once de-contaminated). For the record on the naive pooled series: sd of
per-trade net = 58.9c, so a real 3σ verdict needs ~1,075 obs ≈ **8.2 capture-days** all-coins pooled, or
**~21 days BTC-alone** at current accrual (132 near-50 tradeable rows/day, 29 BTC). But accrual is the wrong
fix — **the binding constraint is data QUALITY (source-frozen books), not quantity.** More days of frozen
books = more fake edge, not a verdict.

---
## Forward RENTEC doors (frozen-book-aware — route AROUND KBT's stale book field)
These are the honest next ideas; all blocked on the same capture gap, so they're specced not run.
- **R10 — coin-trajectory settle model on the FRESH channel.** KBT updates `coin_price` per snap even when
  the book freezes. Build the settle prob purely from coin path (position vs strike, last-N-sec drift,
  realized vol) — that's REAL. Trade only where a LIVE (churn>1) book offers a gap vs the model. Fish:
  MMs set the book early then pull, leaving the frozen quote; a fresh-quoting taker captures. Kill:
  intersect model-prob≥0.85 with active-book ask≤0.75, n≥60 — needs a live-quote capture (KBT free tier
  can't supply it). Verdict: **CONDITIONAL-research (capture-gated).**
- **R11 — hourly crypto (KXBTCH) not 15m.** Hourly books stay two-sided/liquid far longer (unified volbook
  already trades hourly WTI) → fillable. Re-run R9/R10 there. Kill: active-frac test on an hourly-crypto
  book capture. Verdict: **CONDITIONAL-research (capture-gated).**
- **R12 — cross-coin breadth as DIRECTION (not sizing).** ≥4/5 coins same side of strike at T-60 = real
  macro move; trade only the single most-liquid ACTIVE book (BTC) to dodge both frozen-book poison and
  correlated-sample double-count. Kill: on active BTC books, does ≥4/5-agreement beat BTC's own margin?
  Verdict: **CONDITIONAL-research (capture-gated).**
- **R13 (doctrine, not a trade) — ACTIVE-BOOK FILTER is mandatory** for the whole RENTEC/microstructure
  lane. Any KBT price-taking backtest MUST pre-filter churn>1 at entry or it manufactures fake edges. This
  retro-indicts burst-1 R1/R4/R5/R6 (all conditioned on the frozen set). → add to the Mesh.

## CAPTURE DEMAND (the highest-value output — note 32 §5, demand-led)
`capture_kbt.py` stores KBT's source-frozen orderbook field. **Fix:** stamp each snapshot with a
book-liveness flag (either a quote-update ts, or compute churn-since-last-change at capture time) so frozen
books are excludable — OR capture the LIVE Kalshi orderbook (`/trade-api/v2/markets/{t}/orderbook`) on a
short cadence for a handful of BTC/ETH 15m + hourly markets. Without it the entire near-close crypto book
lane is untestable for taking strategies, and every "book" edge found on KBT is a stale-quote mirage.

---
### Burst summary
- **1 flagship RETRACTED:** burst-1 R1 (+12.7c PROMISING) → **DEAD (KBT source-frozen book artifact)**,
  proven by widening to 4 days + a transactable-book filter (−12.5c uniform) + a freshness test (100% of
  edge markets frozen, 0/224 reprice).
- **1 new idea born and killed same burst:** R9 leader-persistence (looked +31c) → DEAD (stale-book
  lookahead). Good kill — the tell was avg entry 0.618 on a would-be 0.90 book.
- **2 gate-hunts run (active-book, mid-window)** → both dead; the live-book subset is fairly priced.
- **4 forward doors specced** (R10-R13), all capture-gated on the ONE demand that unblocks the lane.
- **Scouts:** none (finding was decisive on-disk; the unblock is a nestor capture-script change, not a
  research question — economy).
- **Corrects the night's record:** the steer flagged R1 as the top promotion candidate *if* it widened.
  It didn't. Reporting that honestly, with numbers, is the deliverable.
