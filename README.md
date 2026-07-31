# Enchiridion

The handbook of **the Senate** — the shared knowledge between
[athena](https://github.com/neotechnites/athena) (research) and
[nestor](https://github.com/neotechnites/nestor) (execution): strategy verdicts,
doctrines, backtest conclusions, operating method, and the live project state.
This is what any fresh Claude reads to spin up cold.

- **Entry point: `56 - THE MACHINE (fresh-Claude implementation guide).md`** — read order 56 → 55 → 54 → 47. Everything else is evidence behind pointers.
- Current operational snapshot: the highest-numbered **LIVE STATE** note (state, not concept).
- `implementation/` — build orders and specs that bridge research → nestor. True implementation detail lives in the code repos; what's here is the shared contract.
- Verdicts are DATED and change weekly by design (kill-scan). The newest dated note wins every conflict.
- Secrets never live here: keys are in `SECRETS.local.md` (untracked, gitignored).

## Vault law: EDIT, don't create (2026-07-31)

The vault was purged of notes 36 and below — it had grown into context poison.
The enchiridion is a **prompt**, not an archive. Large stale context degrades the
model that reads it; the archive is **git history**, not more files.

1. **Never create a new numbered note.** Every topic already has an owner file
   (37–56). New understanding gets EDITED into the note that owns it, with a
   dated line. A new file requires an explicit order from Ryan.
2. **One LIVE STATE.** Update the current LIVE STATE note in place — do not
   mint a new dated LIVE STATE file per day. Old state is in git.
3. **Delete as you write.** When you add a dated correction, remove the
   paragraph it supersedes. A note that only grows is poisoning the next reader.
4. **Superseded whole notes go to git, not `archive/`.** `git rm` them; the
   commit message says what replaced them.

The root holds only LIVE notes: if a note is in the root, it is current doctrine or current state. `archive/` is legacy (pre-07 era) — new supersessions go to git history per rule 4, not there.

This folder is also an Obsidian vault — open it in Obsidian for graph/backlinks.
