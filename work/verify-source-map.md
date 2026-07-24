# verify-source-map (IC6) — settle-source → public-endpoint map for the 80 single-source live series

Date: 2026-07-24. Source: `~/kalshi_data/ic_count_triad_out.json`, filtered to `obsc=="obscure-single"` AND live (close > now) → **78 unique series** (the "80"). This is the info-channel capture menu: what physically fixes the settle value, where it's published, how machine-readable, and its latency vs the Kalshi market close. Edge exists wherever **truth is fixed well before the book prices it** and the channel is watchable.

Rating key — MR: machine-readable (Y / partial / N=human-watch). LAT: source-availability vs market close (the capture window). EDGE: capturable info-channel gap.

## A. MENTION-on-earnings-call — 46 series (KXEARNINGSMENTION*: AAPL, MSFT, AMZN, META, … all one_off)
- **Settle source:** does the speaker utter word W on company X's quarterly earnings CALL. Truth fixed at call-end (~60 min).
- **Endpoint:** live audio webcast (company IR page / Nasdaq); machine transcript posts **hours later** (Motley Fool / Seeking Alpha / company 8-K exhibit / AlphaSense). MR: **N live → partial delayed** (audio is human-listen; transcript machine-readable but lagged).
- **LAT:** truth at call-end; market stays open until close **days later** (KXFED-style ~T+1). Book is stale for the whole gap.
- **EDGE: HIGH, but labor-bound.** A live listener has 0/1 word-truth at call-end; the book won't reprice until the transcript drops (hours) and often stays mispriced until close. Same mechanism as KXFEDMENTION. Constraint: one human can cover ~1 call/hour during earnings season; books are thin (OI $47–$14k). Capture = live-listen + snapshot book (build a general `earnings_mention_capture.py` cloned from `fedmention_capture.py`).

## B. MENTION-on-single-broadcast — 6 series (non-earnings)
| series | event / source | MR | LAT | EDGE |
|--------|----------------|----|-----|------|
| KXFEDMENTION | Powell FOMC presser → transcript federalreserve.gov | N live / Y transcript (hours) | truth ~19:30Z, close +18.5h | **HIGH** — capture live (see `fedmention_capture.py`, armed Jul 29) |
| KXTRUMPMENTION / KXMAMDANIMENTION | speech/rally → WH transcript (whitehouse.gov), C-SPAN, Rev.com | N live / Y transcript (hours–day) | speech fixed, close days later | HIGH — live-listen, but efficient-rated crowd on Trump |
| KXMRBEASTMENTION | a YouTube video → the video itself | N (watch), auto-caption partial | video drop = truth, close after | MED — anyone can rewatch; edge only in first minutes |
| KXMLBMENTION / KXWNBAMENTION | broadcast/game call → league feed / box | N watch | game-time truth, close after | MED — human-watch |

## C. ELECTIONS vote-counts — 8 series
KXVOTEPRIMARY (9 evt), KXHOUSEPOPVOTEMARGIN, KXMIDTERMVOTETURN (163 evt), KXVOTEPERCENTBINFACE, POPVOTEMOV, KXUKBYELECTIONCOUNT, KXRGOVCOUNT, KXGENERICBALLOTVOTEHUB.
- **Settle source:** certified vote totals / turnout / margin.
- **Endpoint:** state Secretary-of-State results portals (mostly HTML, some CSV/JSON county feeds), AP Elections API (paywalled), UK: returning-officer + parliament.uk. MR: **partial** (SoS pages vary; a few states JSON, most scrape).
- **LAT:** results trickle over election night → certification days later; markets close on certification. **Long, staged latency** — the count is knowable from precinct feeds well before the market's certification-close.
- **EDGE: MED–HIGH on the staged ones** (KXMIDTERMVOTETURN has 163 live events, tiny OI). Edge = read precinct/county feeds faster than the book updates. Labor/scrape-heavy; per-state endpoints differ. KXUKBYELECTIONCOUNT: single clean UK source, fast declaration.

## D. SENATE/CONFIRMATION roll-calls — Politics counts
KXVOTEBLANCHE, KXVOTELAKE, KXVOTECLARITY, KXSVOTECLARITY, KXBLANCHECOUNT, KXCLAYTONCOUNT, KXJUDGECOUNT (monthly).
- **Settle source:** Senate confirmation / cloture roll-call vote tally.
- **Endpoint:** **senate.gov/legislative/LIS/roll_call_votes → XML** (machine-readable, official) and congress.gov. Nominations also in the **Federal Register** (federalregister.gov/api/v1 — clean JSON). MR: **Y** (Senate roll-call XML is the gold case; FR API is JSON).
- **LAT:** roll-call XML posts within minutes of the vote; FR next business day. Market closes after. **Short, clean latency.**
- **EDGE: HIGH-quality channel, low labor** — the senate.gov XML is the single best machine-readable settle source in the whole set. If any of these books stay open post-vote (check `stayopen`), the XML gives instant truth. Build a `senate_rollcall_watch.py` poller.

## E. FED / ECON counts
- **KXRATECUTCOUNT** (annual, OI $4.3M — largest in set): count of Fed rate cuts in 2026. Source: FOMC statements, federalreserve.gov (text). MR: partial (text, parse the target-range line). LAT: instant at 18:00Z on each of 8 meeting days; market annual. EDGE: LOW as info-channel (release is public & instant, crowd watches) — this is a *forecasting* market, not a latency seam.

## F. SPACE / LAUNCH counts — 3 series
KXSPACEXCOUNT (OI $505k), KXLAUNCHCOUNTM, KXRKLBCOUNT.
- **Source:** successful orbital launch counts. **Endpoint:** SpaceX/RocketLab webcasts + **FAA launch logs**, spaceflightnow, Wikipedia launch tables, r/spacex live threads. MR: **partial** (launch DBs semi-structured; some JSON APIs like thespacedevs/Launch Library 2 — machine-readable). LAT: launch success known live; count-markets close monthly/annual. EDGE: MED — Launch Library 2 API gives near-real-time machine truth ahead of a monthly-close book.

## G. MISC counts
| series | source / endpoint | MR | EDGE |
|--------|-------------------|----|------|
| KXEMMYCOUNT (Entertainment) | Emmy telecast → emmys.com official | N live / Y after | MED — watch the show |
| KXFTACOUNTRIES / KXTRUMPCOUNTRIES / KXISRNORMCOUNT | trade-deal / normalization counts → State Dept, Federal Register, news | partial | LOW–MED, event-sparse |
| KXMORATORIUMCOUNT | policy/eviction count → agency notices / Federal Register JSON | Y (FR API) | MED |

## Ranked capture menu (best info-channel seams)
1. **Senate roll-call XML (Group D)** — cleanest machine-readable official source, minute-latency; build `senate_rollcall_watch.py`. Federal Register API (JSON) is its sibling for nominations/notices.
2. **KXFEDMENTION (B)** — armed tonight via `fedmention_capture.py`; live-listen presser vs 18.5h stale book.
3. **Earnings-call mentions (A, 46 series)** — highest *count* of seams, all the same live-listen mechanism; clone the fedmention daemon into a generic earnings-call capture. Labor-bound, thin OI.
4. **Launch Library 2 API (F)** — machine-readable launch truth ahead of monthly-close count books.
5. **Election precinct feeds (C)** — real but scrape-heavy, per-state; staged certification latency.

## Machine-readability summary
- **Fully machine-readable official (Y):** Senate roll-call XML, Federal Register API (JSON), Launch Library 2 API. → best automate-able.
- **Partial (delayed transcript / scrape):** earnings transcripts, WH/Fed transcripts, SoS election pages, FOMC statement text.
- **Human-watch (N):** all live presser/call/broadcast/award audio — this is where the *listener edge* lives (truth before transcript).

Method note: 78 series pulled + grouped via `python3` over `ic_count_triad_out.json` (obscure-single ∧ live). Endpoint/latency ratings are analytic (channel knowledge), not fresh-fetched.
