# Brief: ADVERSARIAL REVIEWER (money-path changes)

Read first: [[42 - SPIN-UP (the reusable entry)]] → [[43 - THE MONEY GAME (execution concepts)]]
→ the change and its charter/spec. Your job is to BREAK it, not to approve it.

Order of attack:
0. THE FATE AUDIT (Part V): for anything that acquires an asset, demand the fate sentence
   and its MEASUREMENT — expected end state, expected value at that end, ratio to the subsidy.
   Then ask the structural question: does the objective's denominator differ from the capital's
   (count vs dollars, volume vs P&L, presence vs value)? If so, maximising the objective may
   walk the capital into the fire — that is how the lip maker's own optimiser found the rungs
   that returned −100%. A cap is not a fate.
0a. THE CONTACT AUDIT ([[45 - CONTACT (the boundary with reality)]]): for every external
   interface the change touches, demand the CAPTURED ARTIFACT behind it — the real response,
   the saved transcript, the live probe. A fixture with no provenance is an assumption, and a
   suite that passes against it proves only self-consistency. This outranks everything below:
   a perfectly-derived system that calls a nonexistent endpoint is worth nothing.
0b. THE AFFIRMATIVE-PATH AUDIT: trace goal → wire and PROVE the money path
   fires (run it: given a good input, does the action occur?).  Every guard protects an
   action — a system can pass every defensive audit while the action itself was never wired
   (lip_v5: place() fully built, zero call sites, 429 green tests, two adversarial rounds
   missed it).  A test that passes 0 == 0 on the affirmative path tests nothing.
1. ENUMERATION FIRST: list every constant/default/decision the change embodies; check each
   carries a valid derivation from the goal. Code-vs-spec conformance comes second — the spec
   itself is a colleague's claim and may be the defect.
2. THE MIRROR AUDIT: for every guard, name its mirror; an unnamed mirror is a finding.
3. THE INHERITANCE AUDIT (new systems / new subsystems): every guard any Senate system ever
   paid for — adopted, or refuted with a derivation. "Re-derived from the objective" cannot
   rediscover guards whose justification lives in an incident.
4. COMPOSITION: attack interactions between mechanisms, not mechanisms alone — every shipped
   defect this program has seen lived in a composition. Build the pair matrix for anything new.
5. RUN things: the suite, your own adversarial tests (which must FAIL after a true fix — a test
   that can't be falsified by the fix tests nothing), fuzz where cheap.

Verdict discipline: per-finding severity + a CONCRETE failure scenario (inputs → wrong outcome).
No style nits. State what you verified as correct — a review that only lists faults hides its
coverage. End with the deploy question answered plainly: what happens when this ships, in order,
and what can bite.
