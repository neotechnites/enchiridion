# 22 - Bootstrap Tests (utilization flywheel)

> The measured record of the Senate's core claim: **stock Claude + these files → Senate member, no Ryan intervention** (charter, bootstrap doctrine). Protocol: cold worker gets ONLY the enchiridion path, spins up, answers state questions + doctrine probes it can only pass if the files teach. Every gap found becomes a file fix. Append each test here.

## Test 1 — 2026-07-23 (Opus/high, ~46k tokens, ~88s, 8 tool calls)
**Design:** zero context beyond the directory path. Probes: (a) conditional-verdict trap (62% trending / 49% flat / 51% unconditional), (b) alarm-bell odds reporting (30%), (c) global-Poly venue trap, (d) the stale-lock trap that fooled the real implementor.

**Result: PASS — the files produced a member, not an assistant.**
- Identity/goal/role: correct and in Ryan's terms (one goal, derivation chain, hunger-with-the-head, moneyball verbatim).
- Live state: slate, watchlist, machines, lock decay + BOTH operational lessons (kill-scan works; verdicts are dated) — all correct.
- Probe (a): CONDITIONAL, gate-hunt, gate must clear the overfit bar, explicit refusal to slice-mine. Textbook.
- Probe (b) verbatim output: *"The odds are 30%. 30% is not acceptable — the levers that raise it are [X, Y, Z]; may I pull them?"* The alarm-bell reflex transmitted.
- Probe (c): refused the venue, converted the signal to a Kalshi/US-Poly play. Correct.
- Probe (d): refused the 99.3% bait, cited the dated-verdict rule and the kill-scan. The exact real-world failure, now file-proofed.
- Discipline: stayed in scope, read only 7 files, cheap.

**Gaps it found → fixes applied same day:**
1. Banner/precedence overhead; three competing read-orders (00 vs 15 vs 18); entry point only reachable via banner-chain. → **Root = live notes only; all superseded notes moved to `archive/`** (wikilinks unaffected — name-based); read order now lives ONLY in note 18; note 15 defers to it; README states the root/archive rule.
2. Numbering collisions (08×3, 09×3) making links ambiguous. → collisions now all in archive/; live root has unique numbers.
3. Goal stated three conflicting ways across generations (10%/day → 0.5-1.5% → staged). → conflicting versions archived; staged goal in 18/15/21 is the only live statement.
4. Lock labeled three ways in three places. → all stale-lock notes archived with their DEAD banners intact.
5. Correctly could not verify actual key provisioning (SECRETS.local.md untracked) — noted as intended behavior, not a gap.

**Next-test criteria (raise the bar each time):** a fresh worker should ALSO be tested on (i) executing a small task end-to-end from the Batch Playbook alone, (ii) the always-update doctrine — does it commit what it learns unprompted, (iii) time-to-spin-up (files-read count and tokens should FALL as structure improves).
