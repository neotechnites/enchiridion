# VPS Migration Runbook (drafted 2026-07-25; tier numbers pending verify-cloud-tiers.md)

## The decision (derived)
Requirements: ~1 vCPU / 1-2GB RAM / ~50GB disk / 24-7 uptime / US-EAST (latency to Kalshi is money:
home order round-trip ~170-300ms, Virginia ~20-50ms → directly raises retry-fill probability).
- **Primary: Oracle Cloud Always Free** — VERIFIED 2026-07-25 (verify-cloud-tiers.md): still
  free FOREVER but the allowance was CUT Jun 15 2026 to **2 OCPU / 12GB RAM / 200GB** — still
  5-10× our need. The PAYG conversion is MANDATORY, not optional: idle reclamation triggers when
  7-day p95 CPU/net/mem are ALL <20%, and our bot is light enough to look idle; PAYG exempts
  while the bill stays $0. Ashburn A1 = frequent "out of host capacity" — obtainable by retrying
  (script the launch attempt if needed). Home region is PERMANENT: **us-ashburn-1**.
- **Fallback CHANGED (verified): Hetzner DOUBLED prices Jun 2026** — CPX11 Ashburn is now
  ~$17.49/mo, no longer the cheap fallback. New fallback = **AWS credits**: a new account gets
  $100 (+up to $100 earned) expiring at 6 months → t4g.micro + 50GB ≈ $10/mo burns credits ≈
  ~6 months effectively free in us-east-1, THEN decide (by which point nestor funds it or
  Oracle capacity has been won on retry).
- **Kalshi hosting (verified):** api.elections.kalshi.com is CloudFront-fronted AWS; origin
  region unpublished (third-party claims us-east-2/Ohio, UNCONFIRMED). US-East VPS is correct
  either way (Ashburn↔Ohio ~10ms).

## Answers to Ryan's questions
- "How quickly do I hit Oracle free limits?" — **for our workload, effectively never**: we need
  <5% of the A1 compute allowance; captures ~0.5GB/day raw, gzip 10-20× → the 200GB disk holds
  YEARS. The realistic risks are capacity-at-signup and idle-reclamation, not usage limits —
  both addressed by the PAYG conversion.
- "Does AWS have a good free tier?" — not anymore for this: new accounts get limited-time
  credits (verifier confirming exact terms), then ~$8-10/mo for what Hetzner sells at ~$5 and
  Oracle gives free.

## Runbook (~1-2h hands-on)
**Phase 0 — Ryan (10 min):** create the account (Oracle: sign-up, home region us-ashburn-1,
then Billing → upgrade to Pay As You Go). Create an ARM (Ampere A1) instance: Ubuntu 24.04,
2 OCPU / 8GB (leaves free headroom), 100GB boot volume. Add your ssh public key. If A1 is out
of capacity after a few tries at different ADs: fall back to Hetzner Ashburn.
**Phase 1 — bootstrap (Claude-guided, ~20 min):**
  - non-root user + ssh hardening; ufw: allow ssh in, everything else outbound-only (nestor
    needs NO inbound ports).
  - apt: git, python3, build-essential, pkg-config, libssl-dev; install rustup (native ARM
    build works; no cross-compile needed).
**Phase 2 — code + secrets (~15 min):**
  - git clone nestor + athena (GitHub deploy key or Ryan's key).
  - scp secrets: nestor/.env, nestor/secrets/prod.pem (chmod 600) — NEVER via git.
  - mkdir ~/kalshi_data; port scripts (remove macOS-isms: caffeinate wrapper, osascript alerts).
**Phase 3 — services (systemd replaces launchd, ~20 min):**
  - nestor.service (ExecStart=nestor run, Restart=on-failure, WorkingDirectory=nestor).
  - athena-supervisor.timer (every 5 min → nestor_supervisor.sh, linux-adapted).
  - healthwatch.timer (10 min → health_watch.sh; alerts → ntfy.sh push topic = FREE phone
    notifications, replaces macOS banners).
  - aireview.timer (2×/day; needs `claude` CLI installed + authenticated once on the box).
**Phase 4 — cutover WITH state (~10 min, the ordering that matters):**
  1. Stop Mac nestor (kill), stop Mac daemons (launchctl unload com.nestor.machines).
  2. scp nestor/data/* (state.json, logs) + ~/kalshi_data/*.jsonl* to the VPS.
  3. Start VPS services; verify logs: clock-skew OK, divergence <$2, three strategies scheduled,
     first reconcile clean.
  4. Mac becomes dead-standby (keep the repo clones; nothing runs).
**Phase 5 — verify for 24h:** watch first VPS-placed order round-trip in the participation log
(expect ts_ack−ts_submit to drop from ~170-300ms to ~20-60ms — the latency dividend, measured).

## Upgrade path (when nestor pays for it)
Oracle free → AWS us-east-1 (Kalshi co-location, minimum latency) when fill-race losses at
scale exceed ~$20/mo; add a second cheap box (Hetzner) as capture-redundancy when data loss
would hurt more than $5/mo.
