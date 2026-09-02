---
name: worklog-entry
description: Append an entry to dev/worklog.md following the dev/ convention (date+branch heading, newest-on-top, per-scope sub-heading), and flag when dev/worklog-archive.md rotation is due. Use after finishing a chunk of work in a project that has dev/worklog.md, before declaring the work done.
user-invocable: true
allowed-tools:
  - Read
  - Edit
  - Write
  - Bash(git branch*)
  - Bash(wc*)
---

# /worklog-entry — append to dev/worklog.md

Handles the mechanics of the `dev/` worklog convention (heading
matching, newest-on-top placement, rotation threshold) so they don't
need to be re-derived from CLAUDE.md prose each time. This skill
places and formats an entry — the scope title and body content
should already be known from the current conversation, or passed in
`$ARGUMENTS`.

Arguments passed: `$ARGUMENTS` — optionally a short scope title and
body for the entry. If not given, draft it from what was actually
done earlier in this conversation (code edits, doc edits, findings,
etc.) — don't ask the user to re-summarize work you already have
context for.

## 1. Confirm the convention is opted in

Check that `dev/worklog.md` exists at the project root. If it
doesn't, tell the user this project isn't opted into the `dev/`
worklog convention and stop. Never create `dev/` or `worklog.md`
yourself here — that's a separate, explicit opt-in step (see the
global CLAUDE.md `dev/` setup section), not something this skill does
implicitly.

## 2. Gather heading info

- Today's date, in `YYYY-MM-DD` (use the actual current date, not a
  guess from conversation history).
- Current branch: `git branch --show-current`.

## 3. Place the entry

Read `dev/worklog.md`. Find the first `## YYYY-MM-DD — branch <name>`
heading in the file (entries are newest-on-top, so the first one is
the most recent).

- **If it matches today's date and the current branch exactly**:
  insert a new `### <short scope>` sub-heading with its body
  immediately after that `##` heading line, above any sub-headings
  already under it (newest-first within the day too).
- **Otherwise**: insert a brand-new block at the very top of the
  entries — after the file's intro text and its `---` separator, if
  present, and before the existing first `##` heading:

  ```
  ## <YYYY-MM-DD> — branch <name>

  ### <short scope>

  <body>
  ```

Keep the body in complete sentences, with enough context that a
reader could reconstruct what happened without diffing — this is
more detailed than a user-facing changelog, not less.

## 4. Check rotation triggers — don't act on them yet

After writing, check:

- Total line count of `dev/worklog.md` (`wc -l`) — flag if over
  ~500.
- The date in the *oldest* `## ` heading (last one in the file) —
  flag if it's more than ~30 days before today.

If either trips, tell the user rotation is due and ask before doing
anything — per the "confirm before the first split of any session"
rule, this skill never moves blocks to `dev/worklog-archive.md` on
its own initiative. If asked to proceed, move whole `## ` date blocks
(never split one) from the bottom of `worklog.md` to the top of
`worklog-archive.md`, preserving the newest-on-top order in both
files.

## 5. Confirm

Report which heading the entry landed under (new block vs. appended
to today's existing one) and whether rotation was flagged.
