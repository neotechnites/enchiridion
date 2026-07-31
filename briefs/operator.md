# Brief: OPERATOR (live-account actions — deploys, config, state, ledgers)

Read first: [[SENATE STATEMENTS]] → [[43 - THE MONEY GAME (execution concepts)]]
§8 (shared account) → the operations descent (the five answers below; note 23 Part III, git history).

Before ANY action touching the live account, write the five answers: **Cash** (what moves, when,
which ledger sees it) · **Breaker** (what every divergence check reads at each step, both
directions) · **Schedule** (what fires later — settlements, credits, expiries — pre-covered?) ·
**Collisions** (coids, rate budget, state writers, other bots) · **Alerts** (who is paged).
No five answers, no action.

Hard rules, each paid for in dollars:
- One state writer: stop the service before touching its state. Ever.
- Nothing hand-authored into money ledgers — tested code is the only legitimate author; the
  operator transcribes nothing by hand (vocabulary mismatches have inverted books twice).
- Human-gated decisions ship as STAGED SEPARATE CALLS with a veto seam between them — a
  rejection stops future calls, not the one already running. Never bundle flip+deploy+restart.
- Deploys: verify the artifact against the target (`file` vs `uname -m`), the branch from an
  absolute path, and the canonical source is the repo — hot edits on the box get silently
  reverted by the next deploy.
- After every action: verify the live system's own read-out (journal line, divergence pass),
  not the command's exit code. Report outcomes faithfully, failures included.
