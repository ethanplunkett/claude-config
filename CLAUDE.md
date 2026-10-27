# Personal instructions for Claude Code

These rules apply to every project on this machine. Per-project
`CLAUDE.md` files override or extend them.

## Language

Use American English spelling in all output: code comments, docs,
commit messages, chat, and any files written or edited.
Examples: "color" not "colour", "normalize" not "normalise",
"analyze" not "analyse".

## Commits and PRs

Never credit Claude in commit messages or PR descriptions. Omit the
`Co-Authored-By: Claude ...` trailer and any "Generated with Claude
Code" footer. Write commits and PRs as if I authored them directly.

## Pushing

Never run `git push`, `git push --force`, `gh pr merge`, or anything
else that publishes commits to a remote. I push manually. Creating
local commits is fine; opening a PR via `gh pr create` is fine if I've
asked for it; *publishing* a branch or merging is not.

If you think a push is needed to complete a task, stop and tell me —
do not run it yourself, even if I previously approved a push in an
earlier turn.

## Plan before implementing

For any non-trivial work, develop a plan and reach alignment with me
on it **before writing code**. "Non-trivial" is anything more than a
typo fix, a single-function change with obvious requirements, or
something I've spelled out in detail. When in doubt, plan.

A good plan: states the goal, lists concrete steps with file/line
references where possible, calls out decision points needing my input,
and summarises trade-offs for any non-obvious choice. It should let me
redirect the approach before any code changes happen.

Plans live in `dev/workplan.md` (see "`dev/` directory" below). On
projects opted into the `dev/` convention, write the plan there and
ask for sign-off before implementing. On projects without `dev/`, you
can still surface the plan in chat — the requirement is "agree on the
approach first," not "write a file first."

## `dev/` directory

When a project is opted in, two files live at `dev/` at the project
root: `workplan.md` (the active plan) and `worklog.md` (the running
log of what was done).

### Work plan — `dev/workplan.md`

- **What it is** — scratch space for the *currently active* plan.
  One plan at a time; the file is overwritten when new work begins.
  When idle, it contains only a short header explaining its role.
- **Workflow** — write the plan here, ask for sign-off, implement
  against it, then capture the outcome in `worklog.md` and clear the
  plan back to its placeholder. Don't accumulate stale plans here —
  the worklog is the durable record.
- **Native plan mode** — plan mode is read-only, so a plan drafted
  there can't be written to `workplan.md` until after you approve it
  via `ExitPlanMode` (it lives in Claude Code's internal plan storage
  until then). Treat that approval as the sign-off this convention
  calls for, and make persisting the approved plan to
  `dev/workplan.md` the first action afterward, before any other
  edit.
- **What to include** — the same things a good in-chat plan would
  include: goal, concrete steps with file/line references, decision
  points, trade-offs. Markdown checklists are useful for tracking
  step-by-step progress.

### Worklog — `dev/worklog.md`

- **What goes in it** — every change made in a session: code edits,
  doc edits, config tweaks, exploratory findings, memory/settings
  updates, build / packaging fiddling. More detailed than any
  user-facing changelog (`NEWS.md`, `CHANGELOG.md`, etc.); include
  enough context that a reader can reconstruct what happened without
  diffing.
- **Format** — newest entries on top. Group by `## YYYY-MM-DD —
  branch <name>` headers, then `### <short scope>` sub-headers under
  them. If today's date and current branch already have a heading,
  append a new sub-heading rather than starting a new date block.
- **When** — update the worklog as part of the same session that
  made the change, before declaring the work done.
- **Relationship to other changelogs** — the worklog *supplements*
  the project's release changelog and version-bump conventions, it
  doesn't replace them. If the project has `NEWS.md`, `CHANGELOG.md`,
  or similar, continue to follow its rules for user-facing entries.

### Setting up `dev/` on a new project

If the project doesn't already have `dev/worklog.md`, **ask before
creating it.** Don't auto-set-up — this convention is opt-in per
project. A short question is fine, e.g. "This project doesn't have a
`dev/` yet. Want me to set up the workplan + worklog convention here
the same as BirdFlowR?" If I say no, skip the convention entirely for
that project; if I say yes:

1. Create `dev/` at the project root.
2. Exclude it from whatever packaging / distribution / source-control
   mechanism the project uses, so it doesn't ship with releases:
   `^dev$` in `.Rbuildignore` for R packages, `prune dev` in
   `MANIFEST.in` for Python sdists, `.npmignore` for npm, an entry
   in `.dockerignore` if Docker images get built from the tree, etc.
   The exact mechanism is project-specific; the goal is "stays local /
   in the repo, never ends up in a published artifact."
3. Create `dev/workplan.md` with a short placeholder header
   explaining its role (one active plan at a time; cleared between
   pieces of work).
4. Create `dev/worklog.md` with a one-paragraph header describing
   what it is and the newest-on-top format.

### Active vs. archive

`dev/worklog.md` is the *active* log; older entries live in
`dev/worklog-archive.md`. Same newest-on-top format in both. Read
`worklog.md` first when recalling history, and only consult the
archive if the user asks about something not in the active log.

When `worklog.md` exceeds ~500 lines or its oldest entry is more than
~30 days old, move whole `## YYYY-MM-DD — branch <name>` blocks
(don't split a date) from the bottom of `worklog.md` to the top of
`worklog-archive.md`. Do this opportunistically — not in the middle
of unrelated work — and confirm with me before the first split of
any session. 
