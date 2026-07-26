# VERIFY: Optimal execution policy for the 4-streak reversal signal (2026-07-26)

Goal: derive the order-placement policy that maximizes **E[EV captured] per signal** for the
reversal edge (win 52% conservative / 54.7% recent), vs the current policy (IOC@46, 3 retries,
fire iff observed ask ≤44). EV(p)=w·100 − p − fee, **fee(p)=7·p·(100−p)/10000 cents** (=1.74c @46c;
verified vs Kalshi 0.07·P(1−P)). Breakeven: **50.3c @52%, 53.0c @54.7%**. Any fill ≤~50c is +EV @52%.

---

## 1. What the literature prescribes for this structure

**Cont & Kukanov, "Optimal order placement in limit order markets"** (Quant. Finance 17(1):21-39, 2017;
arXiv:1210.1625). Single-exchange closed form (their Prop. 3) is the exact frame: choose limit size L
and market size M to minimize cost `v = (h+f)M − Σ(h+r)·[fills] + θ(orders) + λu(shortfall) + λo(excess)`,
where h=half-spread, f=taker fee, r=maker rebate, θ=impact, **λu=underfill penalty, λo=overfill**,
Q=queue ahead, ξ=queue outflow, F(x)=P(ξ≤x). Result:
- **(i) λu ≤ λu_low → (M*,L*)=(0,S): post everything as a limit, take nothing.**
- (ii) λu ≥ λu_high → (S,0): take everything.
- (iii) between: **L* = F⁻¹( (2h+f+r)/(λu+h+r+θ) ) − Q**, M* = S − L*.

Reading for us: the optimal limit size rises as **taking is expensive (h,f large)** and falls as the
**underfill penalty λu** rises. Their second lever ("place several orders down the book, collect fills,
cancel the rest — non-execution risk falls because limit fills aren't perfectly correlated") = a **maker
ladder**. Their "M* scales with target S, L* is bounded" note means: because **our target is tiny (8-12)
it fits entirely inside the limit bound → theory says post it as a limit and take only as a backstop.**

Where our problem *differs* and bends the answer: (a) **not trading is allowed** — a miss costs only the
forgone EV (λu ≈ +4-13c of EV, small vs the 100c notional), which sits us near regime (i), i.e. **rest-heavy**;
(b) a **hard 60s deadline** and (c) **adverse selection / signal decay** — the "competing same-side buyer
sweeps 45→48" is exactly the maker's toxic-fill risk. The latency-execution literature (arXiv:2504.00846;
Moallemi-style) says immediacy value rises with information decay → **tilt toward taking as the deadline
nears**. Net prescription: **rest cheap for the bulk of capture; backstop-take before the deadline up to a
+EV ceiling.** That is what the data below independently produces.

Sources: [Cont&Kukanov arXiv:1210.1625](https://arxiv.org/abs/1210.1625) ·
[latency/execution arXiv:2504.00846](https://arxiv.org/pdf/2504.00846) ·
[Springer crypto exec 10.1007/s42521-023-00103-y](https://link.springer.com/article/10.1007/s42521-023-00103-y).

## 2. Our data — reversal-side ask path (first 60s after T0)

Tapes: `~/kalshi_data/kbt_books_btc.jsonl`, `kbt_books_eth.jsonl`, 174 windows/coin, **2026-07-24→26 (2.3 days)**,
~100ms snaps. Schema decoded: snap=`[ts, yes_bid$, no_bid$, coin, yes_bid_sz, no_bid_sz, …]`;
**reversal ask = 100 − (streak-side best bid)** (buy-no ⇒ 100−yes_bid backed by yes_bid_sz; buy-yes ⇒
100−no_bid backed by no_bid_sz). **R13 stale-field filter applied**: dropped byte-dup/frozen snaps
(39% of first-60s snaps) and required backing size>0 (yes_bid+no_bid>1 confirms a frozen side).

Streak reconstruction: contiguous clean 4-streaks are rare in 2.3 days (**n=2** BTC, 0 ETH). So the
**primary microstructure set = prev-1 followers (n=143, reversal = opposite of immediate prior result)**;
a **relaxed 4-streak set (n=20, gaps ignored)** validates shape. Distributions (pooled BTC+ETH):

| metric | prev1 (n=143) | 4-streak relaxed (n=20) |
|---|---|---|
| open ask (med / p25-p75) | 53 / 49-57 | 53 / 51-56 |
| **min ask in 60s** (med / p25 / min) | **47 / 41 / 11** | **44 / 41 / 32** |
| time-to-min (med) | 12s | **4.8s** |
| post-min upward sweep ≥3c / ≥5c | 38% / 24% | **55% / 45%** |

**Key facts:** the reversal side does **not** reliably open low — it opens **median 53c** (>breakeven).
It **dips** during the window (median min 47c prev1, 44c after a real 4-streak — momentum piling into the
streak side makes reversal cheaper), then **sweeps back up** (case-b sweep is real and *more frequent after
a 4-streak*, 55% ≥3c, and the dip bottoms **early, ~5s**). So the cheap fills are transient — you must be
**resting before T0** to catch them, and decide fast.

**Resting-bid fill probability P(reversal ask trades ≤ L in 60s)** — prev1 (conservative; 4-streak is deeper):

| L | 32 | 36 | 38 | **40** | 42 | 44 | 46 | **48** | 50 | 52 |
|---|---|---|---|---|---|---|---|---|---|---|
| P(min≤L) | 10% | 16% | 21% | **24%** | 30% | 40% | 45% | **61%** | 72% | 80% |

## 3. Policy evaluation — E[EV captured] per signal (unfilled = 0)

Fill model (flagged): resting bid @L fills at price L iff min_ask≤L; taker fills at the ask if ≤ ceiling.
Current policy credited generously (fills at 44 whenever min_ask≤44 — assumes it catches the touch).

| policy | @52% prev1 | @52% 4-streak | @54.7% prev1 | @54.7% 4-streak |
|---|---|---|---|---|
| **CURRENT** (take iff ask≤44) | 2.50c (fill 40%) | 3.45c (fill 55%) | 3.58c | 4.94c |
| take@open ≤50 | 1.17c | 0.95c | 2.06c | 1.49c |
| rest@40 only | 2.45c (fill 24%) | 2.58c | 3.10c | 3.26c |
| **PROPOSED: rest@40 + backstop take≤48** | **3.55c** | **4.72c** | **4.72c** | **6.20c** |
| maker ladder[44,38,32]+take≤48 | 3.64c | 3.98c | 4.83c | 5.33c |

**Improvement of proposed vs current: +42% (prev1) / +37% (4-streak) @52%; +32% / +26% @54.7%** —
absolute **+1.0 to +1.3c per contract per signal**. The gain has two sources: (a) cheaper *maker* price
(fill at 40, EV +10.3c, on the ~24-25% of dips that reach it) and, larger, (b) the **backstop taker captures
the +EV windows current skips entirely** — current fires only when ask≤44, but ~21% of signals sit at
44<ask≤48, which is +EV (2.2-5.0c) and currently thrown away. Taking is better *later* (t≈45-55s: taker fill
rate rises 15%→21% as the ask mean-reverts down within the window) — but must stay inside the 60s window.
The maker ladder helps the shallow-dip prev1 regime (+0.1c) but *hurts* the deep-dip 4-streak regime
(its 44-leg fills at 44 when a single 40-bid would fill at 40) → **single maker level is the robust choice.**

## 4. PRESCRIBED POLICY

1. **Side:** reversal = opposite of the 4-streak direction; quote = 100 − streak-side best bid (WS feed, <1s).
2. **T0−5 to −10s** (signal known early ~80%): post a **resting GTD limit BUY, full size (8-12), at L=40c**
   on the reversal side. Cancellable ~200ms. Fills ~24% (prev1) / ~25% (4-streak) at 40 → EV **+10.3c@52% /
   +13.0c@54.7%**. (Late-signal ~20%: skip the maker leg, taker-only per step 4.)
3. **Hold it resting** through the window; the dip bottoms ~5s and sweeps up — do **not** chase, do **not**
   take at open (median 53 ≥ breakeven).
4. **Backstop:** if unfilled by **~T0+45s** (still inside the 60s entry window), send **IOC marketable-limit
   at ceiling C=48c**; fills if ask≤48 (~+20% of signals). **Ceiling = 48 hard** (safe under the conservative
   52% winrate; breakeven 50.3). Only if you fully trust the 54.7% recent rate may C rise to 52 (breakeven 53).
5. **Miss** the ~55% of signals whose only price is >48 — that is *correct*: taking >breakeven is −EV.
6. Optional maker ladder (half@42, half@36) only if you expect the shallow-dip regime; single-level is robust.

**Expected capture vs current: 2.50c→3.55c (prev1) and 3.45c→4.72c (4-streak) per contract @52%;
3.58c→4.72c and 4.94c→6.20c @54.7%. ≈ +30-40% more edge captured, +1.0-1.3c/contract/signal.**

## 5. Assumptions & caveats (flagged)

- **Winrate exogenous** (given 52-54.7%); NOT re-estimated here. EV uses fixed w. **Conditional winrate in
  never-dipped / swept-up windows may be lower** (the maker fills at 40 precisely when price fell = mild
  adverse selection; the taker at 48 fires when the dip failed = momentum persisted). Untested — if the
  conditional edge in those windows is materially <52%, shade C down. This is the single biggest risk.
- **Fill model** assumes a resting bid at L fills at L whenever the reversal ask trades ≤L, ignoring
  **queue position**. Tiny 8-12 size + thin book + a sweep that reaches L generally clears small queues, but
  fills at *exactly* L with size ahead may not occur → maker fill-rate (24%) is a mild over-estimate.
- **Taker backstop** modeled at the ~45s ask; real IOC has 200-400ms RTT and can miss a moving quote during
  a sweep → small unmodeled haircut on the taker leg.
- **Sample: 2.3 days, 174 windows/coin, regime-narrow.** Clean contiguous 4-streaks n=2 → primary numbers
  from prev1 (n=143); relaxed-4-streak (n=20) confirms shape and is *cheaper/faster* than prev1, so prev1
  fill-probs are **conservative** for true 4-streaks (real gain likely ≥ the prev1 figures).
- **Signal timing:** assumes the 5-30s early tell (~80%) suffices to rest pre-T0; the ~20% late case
  degrades to taker-only ≤48.
- Market impact of our order negligible (given). Money-impact from reconstruction only: at ~10 contracts,
  +$0.10-0.13 per signal; daily scale small (tiny size) — the win is fractional edge capture, not size.
- Script: `/tmp/streak_exec.py` (streams both tapes, churn-filtered, reproducible).
