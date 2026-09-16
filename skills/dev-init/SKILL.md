---
name: dev-init
description: Scaffold the dev/ workplan + worklog convention on a project that doesn't have it yet — create dev/workplan.md and dev/worklog.md placeholders, and exclude dev/ from whatever packaging/distribution mechanism the project uses (if any). Use once the user has agreed to opt a project into the convention (see the personal CLAUDE.md's "Setting up dev/ on a new project" section).
user-invocable: true
allowed-tools:
  - Read
  - Write
  - Glob
---

# /dev-init — scaffold the dev/ convention

Creates the two files the `dev/` convention needs and wires up
whatever packaging-exclusion mechanism the project actually has, so
none of that has to be redone from CLAUDE.md prose by hand each time
a project opts in.

Arguments passed: `$ARGUMENTS` — not normally needed; this skill
scaffolds a fixed structure.

## 1. Guard against re-running on an already-set-up project

Check whether `dev/workplan.md` and `dev/worklog.md` already exist at
the project root.

- **Both exist**: tell the user `dev/` is already scaffolded here and
  stop — don't touch an active plan or overwrite history.
- **Only one exists**: stop and describe the inconsistency instead of
  guessing which file to (re)create; let the user resolve it.
- **Neither exists**: continue.

## 2. Confirm intent, if it isn't already established

Per the CLAUDE.md rule, setting up `dev/` is opt-in per project and
should be confirmed before creating anything. If this skill was
invoked directly (e.g. the user typed `/dev-init`), that itself is
the confirmation — don't ask again. If you're about to call this
skill on your own initiative rather than in direct response to the
user answering yes to "want me to set up `dev/` here?", ask that
question first instead of proceeding silently.

## 3. Create the two files

`dev/workplan.md` — placeholder only, no active plan:

```
# Work plan

Scratch space for the *currently active* plan only. One plan at a
time — this file is overwritten when new work begins. Once work is
implemented, capture the outcome in `worklog.md` and clear this file
back to this placeholder (see the `workplan-close` skill).

No plan is active right now.
```

`dev/worklog.md` — header plus the empty newest-on-top structure,
no dated entries yet:

```
# Worklog

Running log of changes made in this repo: code edits, doc edits,
config tweaks, exploratory findings, memory/settings updates, build
fiddling. More detailed than any user-facing changelog — include
enough context that a reader can reconstruct what happened without
diffing.

Newest entries on top. Group by `## YYYY-MM-DD — branch <name>`
headers, then `### <short scope>` sub-headers under them. If today's
date and current branch already have a heading, append a new
sub-heading rather than starting a new date block.

Older entries get moved to `worklog-archive.md` once this file gets
long (~500 lines) or the oldest entry is more than ~30 days old. Move
whole date blocks, never split one. Read this file first when
recalling history; consult the archive only if the answer isn't here.

---
```

If the project's own CLAUDE.md or existing docs establish a different
tone (e.g. it names other `dev/*.md` files like `backlog.md` or
`rollout_workplan.md` that `workplan.md` should point to), fold in a
short pointer sentence — but don't invent files that don't exist yet.

## 4. Exclude `dev/` from packaging, if applicable

Detect what packaging/distribution mechanism the project actually
uses — only act on mechanisms with real evidence, never guess a
project type:

- **R package** (`DESCRIPTION` at project root): ensure `.Rbuildignore`
  contains a `^dev$` line. Create the file if missing; append the
  line (don't duplicate) if the file exists but lacks it.
- **Python sdist** (`pyproject.toml` or `setup.py` at root): ensure
  `MANIFEST.in` contains a `prune dev` line, same create-or-append
  rule.
- **npm package** (`package.json` at root): ensure `.npmignore`
  contains a `dev/` line, same create-or-append rule.
- **Docker images built from the tree** (`Dockerfile` at root or in a
  build context): ensure `.dockerignore` contains a `dev/` line, same
  create-or-append rule.

A project can match more than one (e.g. npm + Docker) — apply every
mechanism you find evidence for. If none of these marker files exist
(e.g. an exploratory, non-packaged repo like this one), skip this
step entirely and say so — there's nothing to exclude `dev/` from.

## 5. Confirm

Report what was created (`dev/workplan.md`, `dev/worklog.md`) and
which packaging-exclusion file(s) were created or updated, or that
none applied. This skill doesn't commit anything — that stays a
separate, explicit step.
