# Cloud tier verification — always-on Kalshi bot (verified 2026-07-25)

Need: 1 vCPU, 1-2GB RAM, ~50GB disk, 24/7, US-East (low latency to Kalshi).

## 1. Oracle Cloud Always Free (Ampere A1)
- **A1 allowance CUT June 15, 2026**: was 4 OCPU / 24GB RAM, now **2 OCPU / 12GB RAM** (still ample for this bot). Block storage: 200GB total Always Free (2 volumes). Change was made silently — docs updated, no announcement.
  - Source: https://www.infoq.com/news/2026/07/oracle-cloud-free-tier-limits/ , https://docs.oracle.com/en-us/iaas/Content/FreeTier/resourceref.htm
- **Free forever**: Yes — "Always Free" has no time limit (distinct from the 30-day $300 trial). Allowance was reduced but remains $0.
- **Idle reclamation (A1 only)**: instance reclaimed if over a 7-day window the 95th-percentile CPU <20% AND network <20% AND memory <20%. A lightly-loaded 24/7 bot CAN trip this.
  - Source: https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm
- **PAYG upgrade**: Idle reclamation applies only to *Always Free* instances; upgrading the tenancy to Pay-As-You-Go exempts instances from idle reclamation while staying $0 as long as usage stays within the Always-Free resource limits. This is the standard "upgrade to PAYG, stay free" pattern. CAVEAT: the new 2026 A1 limit's scope is disputed — Oracle docs say the cut applies to "all tenancies," several support agents told users (Jun 2026) it hits free-tier only. **UNRESOLVED — flag.**
- **Ashburn (us-ashburn-1) A1 capacity**: frequently returns "Out of host capacity" for free-tier creates; obtainable via retry/persistence (or automation scripts). PAYG accounts generally get better/priority capacity. Obtainable but NOT instant/guaranteed.
  - Source: https://medium.com/@imvinojanv/setup-always-free-vps-with-4-ocpu-24gb-ram-and-200gb-storage-the-ultimate-oracle-cloud-guide-bed5cbf73d34

## 2. AWS Free Tier (new credits-based plan, since 2025-07-15)
- **New account gets**: $100 credits at signup + up to $100 more earned via service usage = **$200 max**. Expires **6 months after signup OR when credits depleted, whichever first**.
  - Source: https://aws.amazon.com/about-aws/whats-new/2025/07/aws-free-tier-credits-month-free-plan/
- **After expiry**: Free-plan account auto-closes (90-day grace, then resources erased) unless you click-upgrade to the Paid plan (standard PAYG pricing). The old 12-month 750-hr/mo free t2/t3.micro is GONE for new accounts. 30+ "always-free" services remain, but **no always-free EC2**.
  - Source: https://docs.aws.amazon.com/en_us/awsaccountbilling/latest/aboutv2/free-tier.html
- **Cheapest always-on after credits (us-east-1)**:
  - t4g.micro (ARM Graviton, 2 vCPU/1GB) on-demand **$0.0084/hr ≈ $6.13/mo**
  - t3.micro (2 vCPU/1GB) on-demand ≈ **$7.59/mo**
  - Spot ~$0.0018-0.002/hr (~$1.30-1.50/mo) but **interruptible — unsuitable for a 24/7 bot**.
  - Plus ~50GB gp3 EBS ≈ 50 × $0.08 = **~$4/mo storage**. Realistic all-in: **~$10/mo (t4g.micro on-demand + disk)**.
  - Source: https://www.economize.cloud/resources/aws/pricing/ec2/t4g.micro/ , https://aws.amazon.com/ec2/pricing/on-demand/

## 3. Hetzner US (Ashburn)
- **June 15, 2026 price hike**: CPX/CCX lines rose +113-176% (CPX22 €7.99→€19.49). CX/CAX rose ~30%.
  - Source: https://docs.hetzner.com/general/infrastructure-and-availability/price-adjustment/ , https://wz-it.com/en/blog/hetzner-price-increase-june-2026-cpx-ccx-alternatives/
- **CPX11 (US Ashburn)**: still exists; 2 vCPU (AMD shared), 2GB RAM, 40GB NVMe, 20TB traffic. New price **≈ $17.49/mo** (was ~$5.85/mo pre-hike). Meets the spec except disk is 40GB not 50GB.
- **Cheaper ARM/Intel lines (CAX11 ~$6.99, CX23 ~$6.49)**: historically Hetzner US locations (Ashburn VA / Hillsboro OR) offer **only CPX + CCX**, not CX/CAX. Sources conflict on whether CX23/CAX11 are now orderable in US. **FLAG — if US-available, CAX11 (ARM, 2vCPU/4GB/40GB) at ~$6.99/mo is the cheaper pick; otherwise CPX11 at ~$17.49 is the US floor.**
  - Source: https://costgoat.com/pricing/hetzner
- **US stock**: Ashburn VA is a live region; generally in stock.

## 4. Kalshi API hosting
- **DNS**: `api.elections.kalshi.com` → 143.204.142.74/.85/.94/.104. whois: **Amazon Technologies Inc (AT-88-Z)** — this is an **Amazon CloudFront** range. So the public API is **CloudFront-fronted (AWS), NOT Cloudflare.**
- **Confirmed AWS**: Kalshi docs offer **AWS PrivateLink** for `external-api.kalshi.com` (traffic stays on AWS backbone) — confirms Kalshi runs on AWS.
  - Source: https://docs.kalshi.com/getting_started/api_environments
- **Region NOT officially published.** Third-party claim (quantvps) says order-matching in **us-east-2 (Ohio)**; another source implies Chicago proximity. **UNCONFIRMED — flag.** For latency, CloudFront terminates at the nearest edge so raw ping is low from many locations; true origin/PrivateLink region is us-east (Virginia/Ohio) but not verified to a specific region. US-East VPS placement is the right call.
- Note: recommended production host is now `external-api.kalshi.com`; `api.elections.kalshi.com` is legacy but still supported.
