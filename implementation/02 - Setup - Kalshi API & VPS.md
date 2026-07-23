# 02 — Setup: Kalshi API & VPS

> ✅ **Still valid (2026-07-23).** Auth, signing, order placement, and VPS steps are unchanged and needed — but apply them to the **streak ≤44¢ build (build #1)**, not the weather sleeve. See [[18 - LIVE STATE (2026-07-23)]].

> Everything needed to get the [[01 - Weather Sleeve Spec]] running live on a server. Do this before the go-live checklist.

## Kalshi API auth
Kalshi uses **API key + RSA private key request signing** (not a simple bearer token).
1. In the Kalshi account: **Settings → API Keys → Create**. This gives an **API Key ID** (a UUID) and a downloaded **RSA private key** (`.pem`). The private key is shown ONCE — save it immediately.
2. Every request is signed: build the string `timestamp(ms) + METHOD + path` (path = the part after the host, e.g. `/trade-api/v2/portfolio/orders`), sign it with the RSA private key (PSS padding, SHA-256), base64 the signature.
3. Send headers on every call:
   - `KALSHI-ACCESS-KEY: <API Key ID>`
   - `KALSHI-ACCESS-SIGNATURE: <base64 signature>`
   - `KALSHI-ACCESS-TIMESTAMP: <same ms timestamp>`
4. Base URL: `https://api.elections.kalshi.com/trade-api/v2`. (Public market data — `/markets`, `/events`, `/markets/trades` — needs no auth; **placing orders does**.)
5. Order placement: `POST /portfolio/orders` with `{ticker, action:"buy", side:"yes", type:"limit", count, yes_price (cents), client_order_id}`. Use **limit** orders at/above the ask for control (avoid runaway market fills). Check positions/fills via `/portfolio/positions`, `/portfolio/fills`.

**Funding:** account must have USD balance for live trades (even tiny). Fund before go-live.

## Stack
- **Python 3** (matches all research scripts).
- HTTP: `requests` (the research machine's `urllib` SSL is broken — that's a local-machine quirk; on a fresh VPS use `requests` normally, no curl workaround needed).
- Signing: `cryptography` library (RSA-PSS/SHA-256).
- Scheduling: system `cron` (weather is once-daily) — the lock sleeve later needs a long-running poller (`systemd` service).
- Storage: start with a local SQLite file + JSONL trade log; no DB server needed at this scale.
- Config + secrets: `.env` (API key ID, path to `.pem`) loaded via `python-dotenv`; **never commit the `.pem` or `.env`**.

## VPS
- Any small always-on Linux box (1 vCPU / 1GB is plenty for weather; the lock poller later wants low latency to Kalshi but is still light). Providers: Fly.io, Hetzner, DigitalOcean, AWS Lightsail.
- **Timezone:** set the box to UTC; compute 9am ET in code (handle EST/EDT via a tz library) so cron isn't fooled by DST.
- Provisioning: Python 3.11+, venv, the repo, `.env` + `.pem` (scp'd, `chmod 600`), cron entry, logging to file + a simple alert (email/Slack/Telegram webhook) on errors or trades.
- Keep the `.pem` only on the VPS (and one offline backup). Rotate the key if it's ever exposed.

## Secrets & safety
- `.pem` and `.env` live only on the VPS, `chmod 600`, outside any git repo.
- Hard-code conservative caps in code (max stake/trade, max trades/day, max daily loss) so a bug can't blow the account.
- Kill-switch: on any auth failure, unexpected fill price, or daily-loss breach → stop placing orders and alert.

## Build sequence (weather-first, live-tiny)
1. Provision VPS, install stack.
2. Generate Kalshi API keys, fund account, get auth signing working — verify with a read call (`/portfolio/balance`).
3. Implement + test order round-trip with **one $1 trade** on any liquid market: place limit → confirm fill → let it settle → reconcile P&L in the log.
4. Confirm each weather city's exact series ticker + settlement station.
5. Wire the daily weather job ([[01 - Weather Sleeve Spec]]), run in **log-only dry-run for 1–2 days** to eyeball the picks, then flip to live at $10/trade.
6. Watch the weekly log; scale size only once fills + edge confirm.

## Open items to confirm at build time
- Exact Kalshi order endpoint field names / min order size / price tick (verify against current API docs).
- Whether live Open-Meteo `forecast` endpoint serves the morning 00Z run at 9am ET.
- Kalshi rate limits (fine for weather; matters for the lock poller later).

Related: [[00 - Implementation Overview]] · [[01 - Weather Sleeve Spec]]
