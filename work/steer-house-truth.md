# STEER: House probe measurement-truth checks (demo) — before Monday's first quotes

> The probe's PRODUCT is a promote/kill verdict on maker fills. Before it takes its first
> live fill, verify the measurement instrument tells the truth. Streak lessons applied:
> fee truth comes from the exchange, not our formula (R106); Kalshi read-indexes lag writes
> by seconds-to-tens-of-seconds (settled filter 36s, resting-orders list, REST books in
> bursts) — measure the lag on the index we depend on, don't assume it.

## The three questions (all answerable on DEMO, size 1, fake money)
1. **What does a MAKER fill look like in `/portfolio/fills`?** Place a resting quote on a
   demo market where the demo MM will cross it (the R99/R149 pattern: KXHIGHNY-style demo
   books carry resting MM quotes; a bid 1-2¢ above best-ask-complement gets taken). When it
   fills: dump the COMPLETE fills payload verbatim into the ledger. Specifically: is there a
   fee field (`fee`, `average_fee_paid`, anything)? Is there an `is_taker` / maker-taker
   marker? What are the exact field names/units (expect *_dollars/_fp strings)?
2. **Fills-index latency:** from the moment the fill must have happened (order placed
   crossing a resting quote fills immediately — or for a resting fill, poll tight), how many
   seconds until it appears in `/portfolio/fills`? Poll at 1s. Report the distribution over
   ≥3 fills. The house loop polls at 3s and pulls the surviving leg on fill detection —
   detection lag adds directly to two-sided pickoff exposure.
3. **Maker fee schedule truth:** what fee does the exchange ACTUALLY charge on a maker fill
   (from the payload, or balance delta if no field)? Compare vs taker formula
   7·P·(1−P)/100¢ and vs the "maker 25% / often exempt" prior.

## Context you need
- Demo key id: ~/Documents/senate/enchiridion/SECRETS.local.md; PEM:
  ~/Documents/senate/nestor/secrets/Demo.txt. Base URL via KALSHI_API_BASE (demo host —
  see nestor README/engine config). Signing: RSA-PSS, path-only (query strings NOT in the
  signed path — a signing bug was fixed for exactly this).
- V2 order shape: resting = time_in_force good_till_canceled + future expiration_ts +
  taker_at_cross; POST /portfolio/events/orders; duplicate coid → 409. Set expiration_ts
  ~120s so nothing outlives the probe; cancel everything you placed before exiting.
- Known demo quirk (R99, may have changed): /portfolio/fills returned EMPTY after taker
  fills on demo. If it still does for maker fills, that IS a finding (question 2 answer =
  "index unusable on demo") — then answer question 1/3 from the order-response + positions +
  balance deltas instead, and say the prod lag remains unmeasured until the first live fill.
- Write scripts under ~/kalshi_data/scripts/ (e.g. probe_maker_fill_truth.py). Do NOT touch
  the nestor repo, do NOT touch prod keys, do NOT touch live state.

## Report → work/verify-house-truth.md
Verbatim payloads (trimmed to the fill entries) → the three answers with numbers → what the
house crate should change (fee booking source; whether 3s detection is fast enough) → what
stays unknown until prod. Brief.
