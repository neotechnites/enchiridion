# Build ticket — B4 cross-family settlement-calibration harvester (`settle_harvest.py`)

> BEZOS day-1 pipe (note 32 §5). Kalshi retains trades ~10wk, books unrecorded → a daily corpus of (family, strike, T-Nh price, outcome) across ALL liquid settled markets is proprietary by construction and compounds into calibration edges everywhere (powers B1/B9 2yr confirm, THORP/BENTER feature-models, every certainty/wing edge). Generalizes the proven `cwing_pull.py` from 5 commodity series to the whole exchange. Build cost ~2h; zero new infra (post-hoc reconstruction, no pre-close daemon needed).

## Core insight (why it's cheap)
`cwing_pull.py` already reconstructs pre-settle prices POST-HOC from the trades endpoint (`/markets/trades?ticker=..&max_ts=..&limit=1` = last print before a timestamp). Trades retained ~10wk → running DAILY is well inside the window; no daemon must be alive before close. So the harvester is a plain daily cron, not a live capturer.

## Endpoints (all keyless, curl only — urllib SSL broken; ≤8 req/s; a page is valid only if parsed JSON has the expected key, 429 bodies parse as JSON)
- **Settled-market list:** `GET /markets?status=settled&limit=1000&cursor=<c>` — paginate, keep rows whose `close_time` ∈ [now−48h, now−1h] (48h window absorbs a missed run; dedupe against corpus on `ticker`). Each row already carries `result`, `event_ticker`, `series_ticker`, `close_time`, `volume_fp`, `open_interest_fp`, `floor_strike`/`cap_strike`. **Use `*_fp` / `*_dollars` fields — plain `volume`/`open_interest`/`yes_ask` read None for every market (Mesh API quirk).**
- **Pre-settle price:** `GET /markets/trades?ticker=<tk>&max_ts=<int>&limit=1` → `yes_price_dollars` of last print before the horizon ts; store `age = horizon_ts − created_time` so stale prints are filterable downstream.

## Liquidity filter (bounds cost)
Keep a settled market only if `volume_fp ≥ 50` OR `open_interest_fp ≥ 100`. Reason: illiquid rungs have no tradeable price and no calibration value. This is the single knob that keeps rows/day and curl-count sane.

## Horizons pulled (economy tiering, mirror cwing)
- Every kept market: pull T−3h and T−24h (the two decision horizons the volbook/certainty plays use).
- ONLY if T−3h mid ∈ wing band (0.02–0.30 or 0.70–0.98): additionally pull T−1h and T−6h (the term-structure of the wing premium — feeds B12). Interior markets skip the extra pulls.

## Row schema (one JSON line per settled market, append to `settle_obs_<YYYYMM>.jsonl`)
```
{ "series","event","tk","family",           # family = coarse tag (see map below)
  "fs","cs",                                  # floor/cap strike (either may be null)
  "result",                                   # yes|no  (skip if not in {yes,no})
  "vol","oi",                                 # volume_fp, open_interest_fp
  "close_ts","date",                          # close epoch + settle-date str
  "y3","age3","y24","age24",                  # mid (dollars 0-1) + print age (s) at each horizon
  "y1","age1","y6","age6" }                   # present only for wing-band markets
```
Family tag from series prefix (extend freely): KX{GOLD,SILVER,COPPER,PLAT}* → metal; KX{WTI,BRENT,NATGAS,RBOB,HEATOIL}* → energy; KX{INX,NASDAQ100,SPX,DJIA}* → index; KX{BTC,ETH,SOL,...}* → crypto; KXHIGH/KXTEMP* → weather; KX{CPI,NFP,GDP,PCE,JOBLESS,FED,RATE}* → econ; KX{...COUNT,...MENTION,...VOTE}* → count; sports slugs → sports; else → other.

## Cadence
Daily cron, 09:00 UTC (after prior-day US settles are final). Wake-catchup: 48h list window + dedupe-on-ticker makes a skipped run self-healing. Supervisor-managed like the other 6 machines (`nestor_supervisor.sh`, AbandonProcessGroup=true).

## Est. volume / cost
Exchange settles ~2–5k markets/day; liquidity filter (~vol≥50) keeps est. **400–1200 rows/day**. Curl budget: 1 list-paginate (~5–10 calls) + 2 trade-pulls/row + ~2 extra for the wing-band subset (~15% of rows) ≈ **1000–2800 curls/day** → at 8 req/s and 0.13s spacing ≈ **3–8 min/day**. Disk: ~1–2 KB/row → **~0.5–1 MB/day, ~15–30 MB/month** (monthly-rotated files; compact, never store raw ticks per disk rule).

## Reuse / build steps
1. Copy `cwing_pull.py` `get()`, `iso/isots`, `last_print_before()` verbatim (proven throttle + valid-page logic).
2. Replace the fixed 5-series loop with the settled-market paginator + liquidity filter + family tagger.
3. Add the 4-horizon tiered pull (T−3h/T−24h always; T−1h/T−6h wing-band only).
4. Append-with-dedupe to `settle_obs_<YYYYMM>.jsonl` (load existing tickers into a set at start).
5. `settle_analyze.py`: per-family per-DOW implied-vs-realized + fade EV (generalize `bezos_famattr.py`), and a certainty-carry table (far-dated YES≥90¢ realized settle rate) for BUFFETT/B8.

## What it unblocks (the compounding)
- B1/B9 2-yr confirm without waiting 2 yr — reconstructs history back ~10wk NOW, then accrues forward forever.
- THORP/BENTER "informational, not structural" feature-models (note 33 thin-liquid frontier) get their training labels.
- BUFFETT certainty / B8 carry: realized settle rate by price bucket, cross-family.
- B12 wing-premium term structure (T−1/3/6h decay) for entry-timing.
- Every future wing/certainty idea has an immediate cheap on-disk kill instead of a capture wait.
