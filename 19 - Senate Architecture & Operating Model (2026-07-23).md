# 19 - Senate Architecture & Operating Model (2026-07-23)

> How the whole operation is structured as software. Read with [[18 - LIVE STATE (2026-07-23)]] (strategy state) — this note is the SYSTEM state. Named by Ryan 2026-07-23.

## The trinity (`~/Documents/senate/`, all on GitHub under `neotechnites`)
| repo | role | language | data it owns |
|---|---|---|---|
| **nestor** | execution — implements proven rules, places bets | Rust (cargo workspace: `crates/engine` + one crate per strategy + `nestor_bin`) | **participation record**: fills, misses, partials, fees, submit/ack/fill latencies, decision-moment book snapshots, observation log of its own polls (`data/`, compressed nightly, keep everything) |
| **athena** | research — observes everything, ideates, kill-tests, verdicts | Python (stdlib-first) | **observational firehose** (`~/kalshi_data/`): 100ms books, dutch/deribit/weather/xvenue captures, all backtest data |
| **enchiridion** | shared knowledge — THIS vault; the manual both live by | Markdown (also an Obsidian vault; local git + GitHub) | verdicts, doctrines, specs, state, conversation log |

**The relationship (Ryan's framing, adopted):** Athena is the goddess informing nestor — research decides *what's worth betting on*, nestor is *the implementation of what research concludes*. Athena never places a bet; nestor never decides what's true. Enchiridion is the contract between them.

## Knowledge boundaries (what goes where — keep these strict)
- **Enchiridion**: everything shared — strategy rules, verdicts (DATED, newest wins), doctrines, build orders/specs (`implementation/`), the log, this note. If nestor's builder or athena's researcher needs it to agree with the other, it lives here.
- **Code repos**: true implementation detail only (how a crate/script does its job). Thin docs that POINT here; never duplicate truth — duplicated docs rot into the stale-verdict bug that nearly shipped the dead lock edge.
- **Claude auto-memory** (`~/.claude/projects/-Users-ryanwhitehead/memory/`): TINY — a bootstrap pointer to this vault + Ryan-specific working preferences that transcend the project. The moment anything project-substantive appears there, move it here.
- **Secrets**: NEVER in any repo. Vault keys → `SECRETS.local.md` (gitignored); nestor trading keys → `nestor/secrets/*.pem` (gitignored); on VPS/GitHub → environment secrets.

## Data doctrine (Ryan-ordered 2026-07-23)
Keep ALL data, forever. Research owns observation (~0.5-1GB/day raw, 10-20× smaller compressed; ~100GB disk ≈ 2-3 yrs compressed); nestor owns participation (~<1MB/day of events + ~30-80MB/day of its own polls). **What only a live bot can capture — and therefore must never discard**: fill-or-miss conditional on OUR order arriving (adverse selection), submit→ack→fill latency, own book impact, real fees, exchange rejections. Everything observational a research bot can capture equally well — so nestor never duplicates athena's firehose.

## Runtime vs repo (athena)
Machines RUN from `~/kalshi_data/scripts/` under launchd (`com.nestor.machines`); data in `~/kalshi_data/`. The athena repo is the versioned copy — sync direction runtime→repo via `athena/snapshot.sh`, review, commit. This holds until the VPS migration moves the runtime.

## Operational facts
- GitHub access: SSH alias `github-olympus` (key `~/.ssh/olympus_ed25519`) authenticates as `neotechnites`; the `gh` CLI is a DIFFERENT account (RyanStackIntegrated) and cannot create neotechnites repos — Ryan creates empty repos on github.com, Claude pushes.
- Old vault path `~/Documents/Obsidian/nestor` is a SYMLINK to `senate/enchiridion` (compat for anything holding the old path). The vault is standalone now — open `senate/enchiridion` in Obsidian directly.
- nestor's git had pre-existing uncommitted WIP (kalshi.rs, state.rs) from the implementor Claude — theirs to commit.

## The structure is a working asset
Per the charter (note 21, bootstrap doctrine): this file layout, the note structure, memory patterns — ALL permanently on the table. Restructure whenever it serves the result; the files are the inputs and the inputs are the business.

## ⭐ THE ALWAYS-UPDATE DOCTRINE (Ryan-ordered 2026-07-23, standing, no exceptions)
**Every time new understanding lands — an architecture decision, a bug found, a doctrine, a verdict, a caught mistake, a model of how we work — it gets written into enchiridion IMMEDIATELY, without Ryan saying go.** Project-relative knowledge only (not other projects). Update the relevant note (or create a dated one), append Ryan's input to the log (note 17), and **commit + push** — the manual's git history must stay as honest as its content. Rationale: knowledge that exists only in a conversation dies with the context window; this vault is the only memory that survives. Ryan's words: "everytime you come to new understanding you must mark it down so that it is not forgotten... always updating it so that these things arent lost."
