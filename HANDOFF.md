# HANDOFF — 2026-08-05 ~5:00 PM MT

Read order: [[SENATE STATEMENTS]] → [[LIP STATEMENTS]] → **this file** → [[53 - LIVE STATE]]'s
CURRENT block (which carries the same findings in vault form). Then act.

---

## 1. WHAT THE BUSINESS IS (first principles — the frame for everything below)

A mention market is a binary on a speech act resolving at a scheduled earnings call.
It cannot be hedged. Two structural facts drive everything:

- **YES is a wasting asset through the call.** First-passage/hazard decay: every minute the
  phrase is not said, P(said) drops; when it is said the contract jumps to 1. Measured:
  −4.55c mean move T−2h → close; 377/1196 markets fall >10c to a mean of 1.0c.
- **Retail overpays for YES** (lottery preference). ~3c uniform overprice at T−24h; flat
  mid-priced markets are 7.8c overpriced (n=547).

**Therefore the business is SELLING INSURANCE ON SPEECH ACTS.** We collect the decay and
the premium. **The LIP reward is a SECOND income stream, not the business.**

Confirmed independently by external literature (found 2026-08-05):
- Bartlett & O'Hara, *Adverse Selection in Prediction Markets: Evidence from Kalshi*,
  SSRN 6615739 (Apr 2026), 41.6M trades — MMs earn ~2x per contract in single-name markets
  because retail systematically overbets YES on markets that mostly settle NO.
- Xi, Moallemi, Pai & Wang, arXiv 2607.08199 (Jul 2026) — prediction-market variance =
  Wright-Fisher deadline-convergence term `p(1−p)/τ` + Glosten-Milgrom adverse selection.
  **Predicts variance peaks at p=0.5**, which is exactly our measured toxicity peak.

---

## 2. THE DECIDING NUMBERS (all measured today, with n)

### Exit policy — 1,208 settled mention markets, `~/kalshi_data/mentionstudy/`
| policy | result |
|---|---|
| hold to settlement | **−0.82c/contract** (n=10,981) |
| exit T−2h at mid | −0.30c |
| **exit T−2h by CROSSING** | **−6.30c** |
| every stop-loss (2c…20c) | −2.8c to −3.8c, ALL significantly worse than holding (t −5.9…−6.9) |

Median spread is **1.0c from T−24h to T−3h**, then **4.0c at T−2h, 7.0c at T−1h**. We were
waiting until the spread quadrupled and then paying it.
Break-even reward: **~1c/contract if holding, ~6.5c if crossing.** We earn ~3.1c.

### Markout — three independent measurements, `~/kalshi_data/markoutstudy/`
Fills at the touch of a 1c book are **NOT EV-neutral: ≈ −0.5c/contract.**
Our own 52 maker fills: `mo`+1m −1.10 (t=−4.7), +30m −1.81; EV (`mof`) −0.60 / −1.30.
22,921 synthetic mention fills: EV −0.63c. 8,251 live-LIP-universe fills: EV −0.45c.

**PRICE BAND IS THE SELECTOR** (replicates across both universes):
| band (1c books) | EV/contract |
|---|---|
| **≥90c or ≤10c** | **+0.14 … +0.29c** (at or above zero) |
| 25–75c middle | **−0.83 … −1.35c** |
| 45–55c | −1.10 … −1.74c |
**58% of our fills land in the toxic middle. Only 10% in the benign extremes.**

**Markout is REAL INFORMATION, not transient impact**: corr(mo30, settlement markout)
= 0.455; quintiles perfectly monotone; **no mean reversion** (>100% of the +5m move is
retained at +30m and +2h). So it is a valid selection signal.
**But family-level markout does NOT persist**: cross-time corr = 0.13, and 48 of 50 series
got *more* toxic in the later half. **Never build a family blacklist from markout.**
Structural cuts (spread, price band, print-size regime) replicate; reputations do not.

### Side asymmetry
short-YES **+1.27c**, long-YES **−3.99c** (worst −10.87c in the 40–60c band).
λ (fills per resting contract per day, measured): **yes 0.482, no 1.382** — the NO side
fills 2.9x faster.

---

## 3. THE SEAT CRITERION — derived, and the thing to build next

Marginal reward per extra resting contract per day:  `(pool_per_day/2) · R/(q+R)²`
Marginal fill cost per resting contract per day:     `λ_side · |markout|`

> **Quote a side iff `(pool/2)·R/(q+R)² > λ_side · |markout|`**

Both terms are linear in our size at small share, so **the decision is BINARY per market —
you cannot size your way out of a bad market.** Price band was only ever a *proxy* for
`λ · markout`; this is the underlying quantity.

Applied to the live book (`scratchpad/criterion3.py`, run 2026-08-05 ~16:55):
**23 of 31 seat-sides PASS, 8 fail — and every failure is a NO side in a mid-priced
market** (LYFT-MOBI no@0.74, YTVIEWSW no@0.57, LOYA no@0.65, HIMS-CANA/WEGO no@0.24).
The rule reproduces the price-band finding *without being told about it*, and refines it:
it is not "avoid the middle", it is **"avoid the fast-filling side of the middle."**

WARNING FROM WRITING IT: I got this wrong twice first — (a) wrong pool field
(`period_reward`/1e4/(end−start) is correct, NOT `reward_amount_centicents`), and
(b) the `(pool/2)/R` small-share shortcut, which blows up at R≈1 and claimed one contract
could earn $9/day. **Use the full `R/(q+R)²` marginal.**

---

## 4. STATE AS OF THIS HANDOFF

**Deployed** `seats.py` md5 `9c3872c52625016a3f7e28fb321694eb` = commit `7cc623a`.
Repo `~/Documents/senate/nestor-wt-lipv5`, branch has 7cc623a at HEAD.

**Live env** (the ones that matter):
```
SEATS_EXIT_CROSS_H=999          SEATS_EXIT_EVENT_LEAD_H=-100000   # crossing OFF, both routes
SEATS_K=0.06                    SEATS_K_PER_MARKET=0              # K change DORMANT, see §5
SEATS_LONGDATED_ENABLE=0        SEATS_WS_FILLS=1
SEATS_FAMILY_CAP=6              SEATS_DEPLOY_USD=1150
SEATS_SWAP_S=10800              SEATS_ESCROW_PER_MKT_USD=60
SEATS_DAY_STOP_USD=100          SEATS_TOTAL_STOP_USD=200
```
**TRAP: `SEATS_EXIT_EVENT_LEAD_H=0` does NOT disable event crossing.** The condition is
`now >= ev − lead`, so 0 means "cross once the event passes". It must be large NEGATIVE.
That mistake cost $3.84 + $1.32 fees. **Verify the boolean, never the intent.**

**Live P&L, honest** (seats era = fills since Mon night):
rewards recorded +$34.71 (understates — snapshots only start 01:15 today) · realized
−$26.35 (**$14.52 of it FEES**, all from crossing) · unrealized −$40.71.
**Close-up-shop total ≈ −$31.** We have not made money yet.
Holding everything to settlement: expect **$583 back on $624 basis = −6.5%**; selling now
returns $552, so **holding is worth +$31 vs liquidating.**
Reward run-rate ~$60/day. 26 days to Sept 1.
**−6.5% is well within variance** — per-market P&L sd is 192c, p10 −215c, p90 +164c, 48%
of markets negative even when the strategy works. **n=22 cannot distinguish it from zero.
Running longer is the correct response, not a cleverer model.**

**Fees: makers pay ZERO** (41 maker fills $0.00; 31 taker fills $7.27 = 2.30% of notional).
Every fee we have ever paid came from crossing.

---

## 5. IN FLIGHT / DECIDED / OPEN

**Awaiting an adversarial review before arming** (agent running at handoff):
`9a75ea6` — K calibrated 0.06 → 0.361 (measured, n=18 markets/0.6d, IQR 0.20–0.56) plus
per-market K. Deployed but DORMANT behind the two env flags above. Note: as a *constant*
K cannot change ranking order — but **K_eff varies 0.20–0.56 per market**, so one constant
mis-ranks selection. It also mis-scales every ABSOLUTE use: the accrual floor and the swap
margin (at K=0.06 the advertised "2x better challenger" was really ~12x, so **nothing ever
swapped**). Arming it changes selection — review first.

**Also in flight**: escrow-undercount fix (`orders_escrow(roundtrip=True)` reports $0 for a
long-YES round trip holding real contracts → engine thinks it has budget it doesn't; likely
the source of "$1,432 committed against a $1,150 mandate").

**Ryan's rulings today**: skip the side-skew (small reward, real risk of pushing us where
rivals aren't — which is the LLY trap); no taker-side ideas in this bot (separate taker bot
later); one-sided refusal parked; deploy when told, don't deploy off a question.

**Open / not built**: the seat criterion in §3 · reward-only split (`√(V·R·p)`, +$3.92/day,
must be capped — it optimises toward thin sides) · one-sided refusal revisit (it was shipped
on a CONFOUNDED number: the 0.0156-vs-0.0446 figure measured markets where a side had NO
PRICE, not seats where we chose zero size).

---

## 6. WHAT WAS TESTED AND FOUND WORTHLESS — DO NOT REBUILD

- **Stop-losses.** Worst of 11 policies. A 2c stop fires on 51% of eventual WINNERS
  (giving up 35.9c each) and pays 14.3c of spread to do it. An adverse move and a wide
  book are the same event.
- **Speed / websockets for boxing: worth $0.00.** Box cost never positive at any horizon
  (0 of 52 fills); no decay to capture; in all 6 round trips taker flow at our exit price
  was **0.0 contracts** during the blind window AND for 3h after; **not one case** where
  flow passed our queue and we missed it. (Deployed anyway as a correctness fix — fill
  detection 114s → 0.33s median, so we stop quoting into a side being sold to us. Real,
  but not the money.)
- **Fair-value / transcript base-rate modelling: inapplicable.** We are a LIP bot; we
  cannot choose our price.
- **Ranking on reward÷fill-risk**: would walk us into worthless markets. The fattest pools
  are Class-A (gas/metals/UST/DXY) and this strategy already selects quiet books.
- **Family blacklists from markout**: cross-time corr 0.13. Does not hold.

---

## 7. PROCESS — PAID FOR REPEATEDLY TODAY

**Four of five fixes built today were repairs to things built today.** The pattern every
time: verify a change works in isolation, never ask what it does to the rules already
running. The laggard cull passed every test and destroyed the exit orders of open positions.

**ALWAYS run an adversarial review that hunts INTERACTIONS before deploying.** Today's
caught two changes that would have lost real money: a long-dated cross with no loss cap
(would pay 110% of a position's value to close it) and immunity that made capital a
permanent hostage. Reviews cost an hour and save more.

**Verify the boolean, not the intent** (the EVENT_LEAD_H=0 trap).
**Check the arithmetic twice before showing it** — today I produced a $749 opportunity cost
that was really ~$36 (used `close_time`, which on mention markets is a 2026-12-31
PLACEHOLDER; the real date is the call), and a $9/contract/day reward that was a broken
approximation.
**`day_realized` is NOT P&L** — it is `evict_fill_mark`, fill-at-touch marked at mid, which
books half the spread by construction (~$32 of ~$34). Never quote it as money.

---

## 8. THE NEXT REAL INFORMATION

**Thursday and Friday Aug 6–7 settlements.** ~$300 of basis in LYFT/ABNB/SG/CELH (Thu) and
WEN/DKNG (Fri). **Every series we trade is FIRST-CYCLE with zero settled history**; the
−0.82c hold-to-settlement result comes entirely from MATURE series (AAPL/TSLA/AMZN/META,
which measured about half as toxic: mo30 −0.62 vs −1.21). The data contains exactly one
earnings event per series, so first-cycle-vs-mature **cannot be tested** on it — Thursday
is the first evidence that will ever exist for the markets we actually hold.

If those settle near their marks, the thesis holds and the criterion in §3 is the next
build. If they settle systematically against us, first-cycle markets are a different animal
and the whole position side needs re-founding.

---

## 9. FILES (all on the VPS)
`~/kalshi_data/mentionstudy/` 1,208 settled mention markets, exit-policy + calibration ·
`~/kalshi_data/markoutstudy/` markout, three universes, the price-band result ·
`~/kalshi_data/boxstudy/` proof that speed is worth $0 · `~/kalshi_data/splitstudy/`
the √(V·R·p) split, K_eff, the reward-model validation failure (ρ=−0.245) ·
`~/kalshi_data/screen/` 741-program screen, TOP30.csv · `~/kalshi_data/exitstudy/` ·
`~/kalshi_data/virgil/data/est_history.jsonl` per-program accrual every 10 min (cron).
Local scratchpad: `criterion3.py` (the seat criterion), `holdev.py`, `seatsera.py`,
`liquidate.py`, `tiedup.py`, `hostages.py`.
