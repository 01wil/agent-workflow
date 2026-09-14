# agent-workflow

An opinionated working system for coding agents (built for [opencode](https://opencode.ai),
portable to any agent that reads an `AGENTS.md`). It encodes how to work, not just what tools exist:
a mandatory engineering loop, a two-file memory contract, a hard safety protocol for destructive
operations, and a set of skills that load themselves when a task matches.

The core belief: an agent has no memory between sessions, so the *files* are the memory and the
*process* is the product. This repo is that process, version-controlled.

## What's inside

```
AGENTS.md      the bootstrap — read first, every session
wiki/          how work gets done: conventions you are told to read
  *.md         the loop, formats, safety protocol, style
  knowledge/   machine/employer-specific facts — LOCAL ONLY (gitignored)
skills/        self-loading capabilities matched by description
setup.sh       symlink this repo into ~/.config/opencode (Linux/macOS/WSL)
setup.ps1      the same, for Windows
```

## The ideas it enforces

- **The Loop.** Every change — one-line fixes included — runs Specify → Implement → Adversary →
  Verify. No code before checkable acceptance criteria. Meeting the criteria is the halt condition.
  See `wiki/loop-engineering.md`.
- **Two files are the memory.** `notes/requirements.md` is the contract (acceptance criteria,
  priority = list order); `notes/state.md` is the live log (blockers, prioritized todos, append-only
  history). Nothing else. `notes/` is a separate local-only repo, never published.
- **Destructive operations have a protocol, not a vibe.** Before any `DELETE`/`DROP`/`rm`/force-push
  or any SQL that writes to any server, the agent reads `wiki/destructive-operations.md` in full.
  It exists because it already went wrong once: a "harmless syntax check" deleted 130 production
  rows with no backup. `SET PARSEONLY` does not make a statement safe.
- **Portable vs. local, split by git.** General conventions and skills are public. Anything specific
  to a machine or employer — server topology, internal tools, credentials' locations, personal voice
  — lives in gitignored `wiki/knowledge/` and `skills/` entries, present on every machine, never
  pushed. Learning routes to the right destination automatically.
- **Skills load themselves.** Each skill's `description` is a set of trigger conditions; the agent
  matches the task to the skill without being told. Descriptions read as "use when…", not summaries.
- **Match ceremony to size.** A one-line fix gets a sentence of spec, not a meeting.

## Install

```bash
git clone <this-repo> ~/agent-workflow
cd ~/agent-workflow
./setup.sh          # or: pwsh -File .\setup.ps1 on Windows
```

`setup.sh`/`setup.ps1` symlink `~/.config/opencode/{AGENTS.md,wiki,skills}` to the clone, so the
repo stays canonical and the config directory just points at it. Existing files are backed up first.

On a fresh checkout `wiki/knowledge/` is empty except its README — that is expected. Populate it
with your own environment facts; they stay local.

## License

Personal configuration, shared as a reference. Use and adapt freely.
