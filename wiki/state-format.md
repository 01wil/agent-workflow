---
name: state-format
description: The state.md work log - the single memory of a project. Holds blockers, the prioritized todo list (with sub-todos and questions-to-people), and an append-only dated log. Use when starting work on a project (read it first), when finishing any unit of work, when asked to "update state.md", when a question needs a person, or when picking up a project with no context.
---

# state.md — The Work Log

Purpose: persist what was done and the state the project is in, so any agent or human can resume
without re-deriving anything.

state.md is the memory of a project. It carries three things, top to bottom:

1. Blockers and critical context — what you must know before touching anything.
2. The open todos — prioritized, with sub-todos and open questions to people.
3. The log — one dated entry per completed thing, append-only.

It is the record half of the pipeline: requirements.md (the contract) then the work then state.md
(the record). requirements.md owns what is being built; state.md owns what happened.

> Boundary: state.md records what happened in this project. The wiki records how the world works.
> Durable understanding of how the system works goes in src/docs (see src-docs.md). Task status
> never goes in the wiki; environment truths never go in state.md.

There is no separate plan.md, questions.md, or analysis.md. Fine-grained execution steps live as
sub-todos here (or in the session's todo tool). Questions to people live as todos here. Durable
"how it works" understanding is a deliverable (src-docs.md). This is deliberate: two files to
resume from, not five.

---

## The auto-write exception

Unlike every other persisted file, state.md is maintained automatically. At the end of a unit of
work, update it without asking — append the log entry, move done todos into the log, refresh the
blocker block. It is append-mostly and low-risk, and it is the file the user should never have to
think about. The user sees the diff in the session anyway.

Everything else — requirements.md, wiki edits, src/ docs, anything a person will read as a
deliverable — stays always-ask.

Two hard limits even under auto-write:
- Never delete or rewrite past log entries. The log is append-only; done todos move down, they do
  not vanish.
- Never fabricate a timestamp or a result, and never log something as done that was not verified.
  Fetch the real clock: `date '+%Y-%m-%d %H:%M'`.

---

## Starting work: read this first

Read state.md before planning anything. It tells you what was tried, what is blocked and why, and
which decisions are settled. Do not redo work recorded in the log, and do not relitigate a decision
without saying so explicitly. Then give the user a short situation brief (see AGENTS.md).

---

## Structure, top to bottom

1. Title — `# state.md — <project>`
2. Blockers / critical (conditional) — a short block at the very top, only if there is something
   the reader must know first: an active blocker, a missing permission, a "do not run X". A blocking
   question lives here too. Omit the whole block if nothing qualifies. Do not pad it.
3. TODO (open) — the prioritized checklist, before the log. Order is priority: top item is next.
   Order inherits from requirements.md priority. Todos may nest sub-todos. Questions to people are
   todos with a paste-ready message (below).
4. Summary / Log — the core. Dated entries, newest at the bottom of the run, each one completed
   thing. Below the entries, a few context bullets (what exists, how it works) if useful.
5. Key Decisions (optional) — durable choices plus rationale, so they are not relitigated. A
   decision made while working (by you or the user) is recorded here. An answer that came from an
   outside person is recorded in the log entry that resolves its question-todo.

---

## Todos: priority, sub-todos, and questions

Priority is the list order. The top todo is what happens next. When you pick up requirements, the
todos come out in the requirements' priority order. A blocker on a high-priority todo floats into
the blocker block at the top; a blocker on a low one does not.

Break a large todo into sub-todos by indenting under it. One in-progress todo at a time. If
executing reveals the plan is wrong, stop and revise the todos — do not improvise past them. If it
surfaces new scope, that goes back to requirements.md for confirmation first, not silently into the
todos.

```markdown
## TODO (open)

- [ ] Migrate the loader to the new schema
  - [ ] Add the new columns (dev only) — verify: `\d contracts` shows them
  - [ ] Backfill from source — verify: row counts match prior day
  - [ ] Switch the job over — verify: dev run produces an unchanged report
- [ ] Lower priority: tidy the logging
```

A good sub-todo has the same shape a plan step used to: about 2–5 minutes of work, one concern
("add the field" and "validate the field" are two), checkable (it ends in a state you can
confirm — compiles, test passes, endpoint returns), and ordered by dependency so nothing
forward-references. If a sub-todo is bigger than a few minutes, split it; if ten are each ten
seconds, you are over-planning a trivial change — collapse them. Apply the `ponytail` ladder as you
write them, so the list already excludes what does not need to exist. Match the depth to the size:
a one-file fix needs a line of intent and its verification, not a ten-item breakdown.

### Questions to people

An open question that needs an answer from a named person is a todo with two parts: a normal todo
line, and an indented paragraph below it holding the exact message to send — written in the user's
voice (writing-style.md) so it can be pasted into a mail or chat with no editing. State your current
assumption inside the message so the person can confirm or correct rather than compose from scratch.

If the owner is unknown, say so in the todo and ask the user who it should go to; an unrouted
question never gets sent.

```markdown
- [ ] Ask Laszlo for the app-registration credentials (blocks: SharePoint sync go-live)

      hi laszlo, für das sharepoint sync tool bräuchte ich die app registration daten:
      tenant id, client id, client secret, plus die graph-berechtigung Sites.ReadWrite.All
      (application) mit admin consent. der code ist dafür schon ausgelegt, sobald ich die
      daten habe muss ich nur mehr testen. danke, lg
```

The indented paragraph is what makes this cheap: the user copies it out, sends it, and when the
answer comes back pastes the reply in. You then file the answer — move the todo into the log with
the answer and its source, and record any build-changing consequence in Key Decisions or the
relevant deliverable. The question text is preserved in the log entry, never deleted, so the
reasoning survives compaction and nobody re-asks it.

If a question is blocking, also surface it once in the blocker block at the top.

---

## What a log entry contains

Each entry states what was done, the result, and how it was verified.

Good:
```markdown
- **2026-08-19 13:15** — Root cause of the daily-report duplicates found: three causes
  (source re-inserts, duplicated feed rows, delete-window mismatch in the loader). Verified live:
  14,452 rows / 130 excess / 13.9% overstatement. Fix scripts written, not executed — no write
  permission on the prod DB.
```

Too vague:
```markdown
- **2026-08-19** — Looked into the duplicates, made some progress.
```

Include when relevant: measured numbers with units, exact error text for failures, what was not done
and why, and an explicit split of verified fact vs assumption.

---

## Rules

- One entry per thing done, not one per session. Three items completed is three entries.
- Todos before the log, always. Priority is the list order.
- Timestamps are real, from the system clock.
- Append, never overwrite. Done todos move into the log; nothing is deleted.
- state.md auto-writes; deliverables and wiki edits stay always-ask.
- Match the project's language for anything a local reader sees; otherwise English.
- The blocker block is conditional — present only when there is a real blocker.

---

## Location

`notes/state.md` (see folder-structure.md). One per project. For an existing project, follow
whatever it already does rather than moving the file.

---

## Template

```markdown
# state.md — <project>

> BLOCKER: <only if there is one, else delete this line>

## TODO (open)

- [ ] <highest-priority actionable item>
  - [ ] <sub-todo> — verify: <check>
- [ ] <next item>
- [ ] Ask <person> for <thing> (blocks: <what>)

      <paste-ready message in the user's voice>

## Summary / Log

- **YYYY-MM-DD HH:MM** — <what was done, result, how verified>
- <context bullet: what exists / how it works>

## Key Decisions

- <decision + short rationale>
```
