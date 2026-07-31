# 49 - MEASUREMENT DISCIPLINE — the anti-oscillation rules (2026-07-29)

> Written after a session in which a fully-spun-up Claude produced **eight consecutive confident
> conclusions, each overturning the last**, and burned Ryan's morning. Not one of them carried an
> error bar. The corpus had the file that would have prevented it (note 07, Overfitting &
> Validation Discipline — git history) and the entry path did not route to it.
>
> Note 23 (Derivation First — git history) makes ONE attempt careful. This note makes attempts
> COMPARABLE and stops them from thrashing. They are two halves; 23 alone produced the thrash.

---

## THE DIAGNOSIS (grounded, not invented)

The external literature says three things the Senate had not internalised:

1. **Verification is the bottleneck, and it scales by BREADTH not DEPTH.** Sampling many
   independent candidates and verifying across them beats one deeply-reviewed candidate — and the
   mechanism is specifically that *comparing across responses localises errors*
   (Zhao/Awasthi/Gollapudi, *Sample, Scrutinize and Scale*, arXiv 2502.01839). **Our "three
   adversarial rounds" is depth on one artifact — the shape that provably does not get this
   benefit.** [[45 - CONTACT]] already recorded the symptom: *"each round attacked the reasoning
   harder, and the defect was never in the reasoning."* We diagnosed it and never changed the shape.
2. **Point-estimate uncertainty is THE documented agentic failure mode.** The fix is structured
   uncertainty across the process, not a confidence number on the final answer
   (arXiv 2602.05073).
3. **Specification hacking** — choosing which analysis to run after seeing results — is the AI
   analogue of researcher degrees of freedom, and the countermeasure is pre-registration
   (arXiv 2606.11217).

**75% of agentic failures are "silent gray errors"**: plausible, well-formatted, unusable — visible
only on manual inspection (arXiv 2601.00481). In this project that inspection is *Ryan's morning*.
Every hour he spends catching a confident wrong answer is the exact labour the Senate exists to
delete.

---

## THE RULES

### R1 — NO NUMBER ENTERS A DECISION NAKED
Every measurement that touches a decision carries three things or it is not admissible:
- **n_eff** — the count of *independent* units, which is almost never the row count. Fills inside
  one market are one unit. Rungs of one ladder are one unit ([[43]] §3). Orders inside one day are
  not independent across days.
- **an interval**, computed, not asserted.
- **its out-of-period status**: in-sample / out-of-period / untested. Note 07's case study (git
  history) is the standard — the favourite edge passed *four* checks including split-half and
  died out-of-period, because every check lived in one window.

### R2 — THE ANTI-OSCILLATION RULE (the one that would have saved today)
**A point estimate may not overturn a standing conclusion.** To overturn, the new measurement must
either (a) have non-overlapping intervals with the old, or (b) be out-of-period where the old was
not. Otherwise it is *added to the evidence*, and the conclusion is restated as a range.
> Today's violation: fill-rate T was measured at 4.7 / 48.9 / 1.3 / 0.6 h across bands and a whole
> capital plan was rebuilt on it. n_eff was **22–25 independent events over TWO DAYS**, and the
> configuration actually being proposed (small orders in deep books) had **zero observations in
> the sample.** The table could not answer the question it was used to answer.

### R3 — PRE-REGISTER THE FALSIFIER
Before running a measurement, write: *"result X would kill this hypothesis."* If no result would,
it is not a measurement, it is a search for confirmation. Write it in the same message that
proposes the measurement, before the data is seen.

### R4 — THE SAMPLE MUST CONTAIN THE CONFIGURATION YOU ARE PROPOSING
An estimate drawn from configuration A does not license a decision about configuration B.
Our whole tape is 350–400-contract orders in thin books; it is silent on 1–3 contract orders in
deep books, which is what every design since has proposed. **Silence is not evidence of safety.**

### R5 — VERIFY BY BREADTH, NOT BY DEPTH
For any decision above a stated dollar threshold: **N independent attempts that cannot see each
other, then compare across them.** Disagreement between independent attempts is the error signal.
Sequential adversarial review of one artifact is explicitly the shape the literature says fails,
and the shape this project has always used.

### R6 — DECIDE UNDER UNCERTAINTY, DO NOT WAIT FOR CERTAINTY
The files hunt for *the* answer. That is the wrong object when several configurations are live and
each day of delay costs the income we already have a receipt for. The right object is **a portfolio
of simultaneous small experiments, sized so the total downside is bounded, run in ONE day, with the
receipt as the discriminator.** One day of six configurations beats six days of one configuration —
and it breaks the venue/price/size confound that makes every single-configuration day uninterpretable.

---

## WHAT CHANGES ELSEWHERE

- **[[42 - SPIN-UP]]** — note 07 (git history) joined the mandatory concept list, and this note with it. The entry
  path routed a fresh session past the exact file it needed.
- **Note 23, Derivation First (git history)** — Part VI: a derivation whose inputs have no intervals is not a
  derivation, it is arithmetic on guesses. "Every claim carries its because" now also reads
  **"every measurement carries its n and its interval."**
- **`briefs/`** — every brief carries R1–R4 as a stanza, and the reviewer brief's first question
  becomes *"list your numbers; give n_eff and the interval for each"* before anything else.
- **Workers** — [[42]] gives athena one line for execution problems. A question with this many
  branches must be fanned out, not thought through serially in one context. Serial single-context
  analysis IS the oscillation mechanism.
