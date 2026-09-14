# Wiki — Start Here

Entrypoint for agents. If you were told **"read the wiki"**, this file is the instruction set.
Read it fully, then load only what your task needs.

This file is a router. It holds no knowledge of its own, only where knowledge lives.

---

> # STOP — read this before touching data
>
> **If your task involves `DELETE`, `UPDATE`, `INSERT`, `TRUNCATE`, `DROP`, `ALTER`, `MERGE`,
> `rm`, `mv`, overwriting a file, or `git reset`/`push --force` — read
> [`destructive-operations.md`](destructive-operations.md) FIRST, in full, before writing a
> single statement.**
>
> The same applies to **any** SQL that writes, on **any** server. Never judge a server by its name —
> a box named like a legacy test server can hold live production data.
>
> This is mandatory, not advisory. A previous agent deleted 130 rows of production data
> while believing it was running a harmless syntax check. There was no backup. The user is
> personally accountable for lost production data.
>
> Three things to internalise right now:
> - **`SET PARSEONLY` and `SET NOEXEC` do NOT make a statement safe.** They provably execute DML.
> - **Every destructive T-SQL statement is triple-checked:** `SELECT` the affected rows → verified
>   backup → `BEGIN TRANSACTION` with guards that `ROLLBACK` on mismatch → `COMMIT`.
> - **Never delete anything.** Files, rows, folders, history. Ask first, every time.
> - **If the user said wait, wait.** Investigating is not an exception.
>
> Treat the data as money.

---

## Structure

```
<repo>/
├── AGENTS.md      the bootstrap instruction set
├── wiki/
│   ├── *.md       how work gets done      — you are told to read these
│   └── knowledge/ what is true about THIS machine/employer — local-only, load per task
└── skills/        how to do things well   — opencode loads these by description
```

`setup.sh`/`setup.ps1` symlink `~/.config/opencode/{AGENTS.md,wiki,skills}` to this repo, so the
repo is canonical and the config dir just points at it. Never edit the copies under `~/.config` —
they are the same files.

`knowledge/` contents are machine/employer-specific and gitignored; only its `README.md` and
`.gitkeep` are tracked. See [`folder-structure.md`](folder-structure.md).

Which kind a new file belongs in: [`wiki-guidelines.md`](wiki-guidelines.md).

---

## Do this at the start of every task

0. **If the task can destroy anything — read `destructive-operations.md` in full, first.**
   Any `DELETE`/`UPDATE`/`INSERT`/`TRUNCATE`/`DROP`/`ALTER`/`MERGE`, any SQL against any server
   (never judge a server by its name), `rm`/`mv`, overwriting a file, or
   `git reset`/`clean`/`push --force`. No exceptions, including "I am only checking the syntax".
1. **If the task writes or changes code, SQL, scripts, config, or deployment state — read
   [`loop-engineering.md`](loop-engineering.md) before the first line.** Specify → implement →
   adversary → verify, and no code before checkable acceptance criteria. One-line fixes included.
   For a vague or large new work item, `brainstorming` produces the confirmed
   `notes/requirements.md`, and the ordered todos in `notes/state.md` are the plan.
2. **Read [`folder-structure.md`](folder-structure.md)** for where files go in a work folder.
   Never restructure anything without asking.
3. **Check for a `state.md`** in the project folder. If it exists, read it — it is the work log.
   Do not redo work recorded there. Format: [`state-format.md`](state-format.md).
4. **Load the `knowledge/` files your task needs.** See `knowledge/INDEX.md`; skip the rest.
5. **If you are drafting anything the user will send to a person, read
   [`writing-style.md`](writing-style.md) first.** It must sound like the user, not like an agent.
6. **At the end**, offer to update `state.md`, and offer to add new durable knowledge per
   [`wiki-guidelines.md`](wiki-guidelines.md). Always ask before writing either.

---

## Conventions (top level)

| File | What it governs |
|---|---|
| [`destructive-operations.md`](destructive-operations.md) | **MANDATORY** before anything that writes or deletes. The protocol, the templates, the incident |
| [`loop-engineering.md`](loop-engineering.md) | **MANDATORY** before writing code. The four phases, acceptance criteria, the production guardrail |
| [`folder-structure.md`](folder-structure.md) | the two-folder work layout (`notes/` local-only, `src/` published), where each file goes, the machine-local `knowledge/` folder |
| [`requirements-format.md`](requirements-format.md) | `notes/requirements.md` — the confirmed spec and acceptance criteria (priority = list order) that gate all code |
| [`state-format.md`](state-format.md) | `notes/state.md` — blockers, prioritized todos with sub-todos, questions-to-people, and the append-only log |
| [`src-docs.md`](src-docs.md) | the docs that ship in `src/` — README, DEPLOYMENT, ARCHITECTURE (absorbs the old analysis file) |
| [`writing-style.md`](writing-style.md) | the user's voice for mail, chat, and status updates |
| [`wiki-guidelines.md`](wiki-guidelines.md) | what belongs where in this folder, and when to write it |

## Knowledge — load per task

`knowledge/` holds what is true about the specific machine and employer this repo is deployed on:
server topology, internal tools, environment gotchas, credentials' locations. Its contents are
local-only (gitignored), so the index of what exists lives beside them, not here.

- Read [`knowledge/INDEX.md`](knowledge/INDEX.md) for the list of available knowledge files and
  when each applies. On a fresh checkout this folder is empty except its README — that is expected.
- Unsure which applies? Grep rather than guess:

```bash
grep -ril "<server-or-table-or-tool>" wiki/knowledge/
```

New durable environment facts become a new `knowledge/` file plus a row in `knowledge/INDEX.md`.
A general, portable lesson goes into a wiki convention file or a skill instead —
see [`wiki-guidelines.md`](wiki-guidelines.md).

## Skills — loaded automatically

You do not need to load these by hand; opencode matches them by description. Listed so you know
they exist.

| Skill | Fires when |
|---|---|
| `brainstorming` | a vague or large new work item with no confirmed acceptance criteria, phase 1 of the loop |
| `ponytail` | before writing new code — the reuse-first minimalism ladder, phase 2 of the loop |
| `senior-software-engineering` | design and implementation judgment, phase 2 of the loop |
| `systematic-debugging` | a bug, exception, failing test, regression, or flaky behavior |
| `verification-and-testing` | designing tests, phase 4 of the loop |
| `high-signal-code-review` | reviewing a diff, PR, or implementation |
| `deslop` | stripping generated bloat from your own diff before reporting done |
| `continuous-learning` | end of a task that taught a durable lesson the wiki should hold |
| `engineering-principles` | repetitive edits, context budget, type discipline |
| `writing-for-agents` | creating or editing a skill, a wiki file, or `AGENTS.md` |
| `handoff` | context filling up, or work spanning sessions |
| `unslop` | always, on any prose |
| `lean` | user-invoked (`/lean` or "lean mode" persistent, "lean out X" one-shot) — terse, action-first output |
| `csharp-repository-structure` | laying out a .NET solution |

Environment-specific skills (integrations tied to a particular employer or toolchain) are kept in
the local-only skill set and are not part of this portable repo.

---

## Hard rules

These override convenience. They exist because violating them has caused real problems.

- **Never delete anything of value.** Received material, notes, working states, screenshots, data
  dumps, database rows — ask first, every time, even if it looks obsolete. Deleting your own scratch
  artifacts is expected; say what you removed.
- **Ask before restructuring** an existing project folder. An unusual layout may be deliberate.
- **Verify before asserting.** Run the query, read the file, check the permission. Never state a
  measured fact you did not measure. Say "unverified" when it is.
- **Confirm before writing** to `state.md` or to any wiki file.
- **Production is read-only** unless write access is explicitly confirmed for that specific task.
- **No secrets in the wiki.** Reference where a credential lives, never its value.
- **Ask instead of assuming.** One question costs a minute. A wrong assumption costs the task.

---

## Environment quick facts

Machine- and employer-specific facts (host, working roots, how to reach SQL and Windows tooling,
proxy, preferred language for deliverables) live in the local-only `knowledge/` folder, not here,
so this router stays portable. See `knowledge/INDEX.md`.

- **Production is read-only** unless write access is explicitly confirmed for that specific task.
- A work folder is usually a **git repo** — commit before and after any restructuring.

---

## Maintaining this folder

- What belongs where, and when to write it → [`wiki-guidelines.md`](wiki-guidelines.md).
- How to write it so an agent actually reaches it → the `writing-for-agents` skill.
- Recording work status → [`state-format.md`](state-format.md).
- A new environment fact means a new `knowledge/` file and a row in `knowledge/INDEX.md`. A new
  top-level convention file or skill means a new row in its table above, so the router stays complete.
