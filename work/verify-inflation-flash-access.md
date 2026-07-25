# verify-inflation-flash-access — closing the R126 prelim→final source-access gate

Date: 2026-07-25. Task: determine which Kalshi inflation flash/prelim markets exist, their official
prelim & final data sources (URL / machine-readability / free-vs-walled / timing vs close), and whether
the prelim is obtainable BEFORE the book prices it. Verdict target: HARVESTABLE / PARTIAL / WALLED.

Prior context: R126 (enchiridion) + `work/verify-prelim-final.md` — |final − prelim| ≤ 0.1pp holds >90%
for HICP/CPI flash→final; sources were "partly walled." This ledger closes that gate.

## (1) The Kalshi markets of this type (public API, Economics catalog = 622 series)
Pulled `GET /trade-api/v2/series?category=Economics` (520 KB). Two families exist:

**Family A — settle ON the preliminary/flash value (source = Trading Economics mirror):**
| series | title | agency behind the flash | freq |
|--------|-------|------------------------|------|
| KXFRCPIPREL | France Inflation Rate YoY Prel | INSEE | monthly |
| KXDECPIPREL | Germany Inflation Rate YoY Prel | Destatis | monthly |
| KXITCPIPREL | Italy Inflation Rate YoY Prel | ISTAT | monthly |
| KXEZCPIYOYF | Euro Area Inflation Rate YoY Flash | Eurostat | monthly |
| KXUSMICHCSP | US Michigan Consumer Sentiment Prel | UMich | monthly |
| (GDP flash cousins) KXDEGDPYOYF, KXEZGDPYOYF, KXFRGDPQOQP/YOYP, KXESGDPQOQF, KXITGDPQOQA/YOYA | GDP flash/prel/adv | Eurostat/national | quarterly |

**Family B — settle on the FINAL / first non-preliminary release:**
| series | title | settlement source (from API) | freq |
|--------|-------|------------------------------|------|
| KXHICP | Euro Area Inflation (HICP YoY) | **Eurostat** (`ec.europa.eu/eurostat`) | monthly |
| KXCPI / KXCPIYOY / KXCPICORE… | US CPI | BLS (first release ≈ final) | monthly |

All Family-A/GDP markets carry the generic contract template **ECONSTATTE.pdf**; KXHICP carries **HICP.pdf**.
Note: at pull time NO events/markets were live for any of these (listed just-in-time each month; KXHICP had
one settled event KXHICP-25JUN). Series definitions + contract terms are the load-bearing evidence.

## (2) Contract mechanics that decide everything (ECONSTATTE.pdf, extracted verbatim)
- **Last Trading Date/Time (lines 105-112):** "The Last Trading Date … will be the day of expected release
  of <econ stat> … The Last Trading Time will be **one (1) minute prior to the expected release**."
- **Resolution basis (lines 74-83):** "only the **first non-preliminary release** … will be used to resolve
  the market, and subsequent revisions will not affect resolution … the exact statistic defined in the
  Contract title governs." (So a "Prel"-titled market settles on the prelim; an untitled/final market like
  KXHICP settles on the first non-preliminary = the mid-month final.)
- Expiration time 10:00 AM ET; settles $1.00; source hierarchy = agency first, Trading Economics fallback.

## (3) Official sources — URL / machine-readability / free-walled / timing (tested in-env)
| source | endpoint | MR | free? | in-env reach | flash timing | final timing |
|--------|----------|----|----|--------------|--------------|--------------|
| **Eurostat HICP** | `ec.europa.eu/eurostat/api/dissemination/statistics/1.0/data/prc_hicp_manr?format=JSON&geo=EA&coicop=CP00` | **JSON, keyless** | **FREE** | **200 OK** (pulled EA CP00 2025-09…12 = 2.2/2.1/2.1/2.0) | flash ~last day of ref month / 1st, ~11:00 CET | **final ~17th of next month, 11:00 CET** |
| Eurostat euro-indicator releases | `ec.europa.eu/eurostat/web/products-euro-indicators` | HTML | FREE | 200 | e.g. Jun-26 flash 01/07, final 17/07 | confirmed via calendar |
| INSEE (France) BDM | `api.insee.fr` | JSON API | free w/ token | **000 blocked** | provisoire ~last working day ~07:45 CET | définitif ~15th next month |
| Destatis (Germany) | `www-genesis.destatis.de` (GENESIS API) | JSON/CSV | free w/ token | **307 redirect** | prelim ~28th-30th, 08:00 CET | final ~2 wks later |
| ISTAT (Italy) | `esploradati.istat.it` (SDMX) | XML/JSON | free | not tested | prelim ~end of month | final ~mid next month |
| BLS (US CPI) | `api.bls.gov/publicAPI/v2/…/CUUR0000SA0` | JSON | free (v2 key) | **200 OK** | n/a — first CPI release ≈ final | seasonal revision only |
| Trading Economics (named settle mirror) | `tradingeconomics.com/<ctry>/inflation-cpi` | HTML / paid API | **WALLED (paid API)** | scrape-only | mirrors agency | mirrors agency |

Flash→final gap for euro-area HICP = **~2.5 weeks**; documented |final − flash| ≤ 0.1pp in >90% of months
(R126 / verify-prelim-final.md; not re-measured here — this task is the access gate, not the CDF).

## The gate, resolved
The "partly walled" flag was about **which endpoint physically fixes the settle value**. Result:
- **Euro-area aggregate (Eurostat) is fully OPEN** — free, keyless JSON, confirmed 200, updated at release.
  For any Eurostat-settled market the prelim/flash value is trivially machine-harvestable.
- The **national prelim agencies (INSEE/Destatis/ISTAT)** are the walled/blocked ones (token or scrape),
  AND Trading Economics (the named settle mirror) is paywalled — but this is **moot** (see below).

## Why source access is NOT the binding constraint — the two real killers
1. **Family A gives zero front-run window.** The *PREL/*FLASH markets settle ON the prelim, but Last Trading
   = **1 minute BEFORE** that prelim is released. The settle value is not public until after the book is
   frozen. No amount of source access lets you front-run it — you'd be pure-forecasting the flash. WALLED by
   microstructure regardless of endpoint.
2. **Family B (KXHICP) has an OPEN source and a real ~17-day window** where the flash is public and the
   final-settling market still trades — this IS the R126 lock, and Eurostat access is confirmed. BUT the
   flash is public to the **entire order book**, so the residual is an **efficiency** question (does the book
   misprice a ≤0.1pp-locked outcome?), not a source-access question. Access is solved; edge is not proven.

## Fetch procedure (if pursuing the KXHICP / Eurostat-settled lock)
```
GET https://ec.europa.eu/eurostat/api/dissemination/statistics/1.0/data/prc_hicp_manr
    ?format=JSON&geo=EA&coicop=CP00&lastTimePeriod=2      # keyless, free, ~instant
# read latest period value = flash YoY the moment it posts (~11:00 CET end-of-month)
# KXHICP-<mon> stays open until ~1 min before the mid-month FINAL (~17th, 11:00 CET / 05:00 ET)
# if a rung boundary is ≥0.2pp from the flash, the ≤0.1pp revision cannot flip it → lock candidate
```
For country boundaries you'd need INSEE/Destatis/ISTAT tokens (free but the in-env network blocks them) —
but those feed Family-A markets that have no window, so not worth wiring.

## VERDICT: PARTIAL
Source access is CLOSED as a blocker for the euro-area aggregate (Eurostat = free machine-readable JSON,
confirmed), and the ≤0.1pp flash→final lock is real for KXHICP with a ~17-day open window. It is NOT
harvestable end-to-end because (a) the national *PREL/*FLASH markets last-trade 1 min before their own
settle value posts — no front-run; (b) KXHICP's flash is public to the whole book, so the remaining edge is
efficiency, not access; (c) national prelim sources (INSEE/Destatis/ISTAT/Trading Economics) are
token/paywalled but moot given (a). Next test is a book-efficiency probe on a live KXHICP event, not more
source plumbing.
