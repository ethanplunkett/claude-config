---
name: worklog-rotate
description: Move whole old date blocks from dev/worklog.md into dev/worklog-archive.md, per the dev/ convention's rotation rule (~500 lines or oldest entry ~30+ days old triggers it, never split a date block). Use when worklog-entry flags rotation is due, or when the user asks to rotate/archive the worklog.
user-invocable: true
allowed-tools:
  - Read
  - Edit
  - Write
  - Bash(wc*)
---

# /worklog-rotate — move old entries to dev/worklog-archive.md

Handles the mechanics of splitting `dev/worklog.md` into active vs.
archive: finding the boundary between recent and stale date blocks,
moving whole blocks without splitting one, and preserving
newest-on-top order in both files.

Arguments passed: `$ARGUMENTS` — optionally a cutoff hint (a date or
"keep last N entries"). If not given, use the ~500-line / ~30-day
thresholds below to decide what moves.

## 1. Confirm the convention is opted in

Check that `dev/worklog.md` exists at the project root. If it
doesn't, tell the user this project isn't opted into the `dev/`
worklog convention and stop.

`dev/worklog-archive.md` may not exist yet — this is expected the
first time a project rotates. If it's missing, you'll create it in
step 4 with the same newest-on-top format and a one-line header
identifying it as the archive.

## 2. Parse the date blocks

Read `dev/worklog.md`. Each top-level entry is a
`## YYYY-MM-DD — branch <name>` heading followed by its `###`
sub-headings and body text, running until the next `## ` heading or
end of file. Blocks are newest-on-top, so the first `## ` heading is
the most recent and the last is the oldest.

## 3. Decide what moves

Check both triggers from the CLAUDE.md convention:

- Total line count of `dev/worklog.md` (`wc -l`) — over ~500 is due.
- The date in the oldest (last) `## ` heading — more than ~30 days
  before today is due.

If neither trigger is tripped and `$ARGUMENTS` gave no explicit
cutoff, tell the user rotation isn't due yet and stop — don't rotate
just because you were asked, if the file doesn't actually warrant it
yet. (If the user insists, proceed anyway — this check is a
courtesy, not a gate.)

If rotation is due, work from the bottom (oldest) upward, selecting
whole `## ` blocks to move until the remaining file is comfortably
under the line-count threshold and its new oldest entry is within the
~30-day window — never partially move a block. If `$ARGUMENTS` gave
an explicit cutoff, honor that instead of computing one.

## 4. Confirm before the first split of the session

Per the CLAUDE.md rule, confirm with the user before the first
worklog split of any session. Show the exact list of date blocks (and
their date range) you're about to move, then wait for a go-ahead
before editing. Once confirmed, later rotations in the same
conversation don't need to be re-confirmed.

## 5. Move the blocks

- If `dev/worklog-archive.md` doesn't exist, create it with a short
  header paragraph describing it as the archive half of the worklog
  (same newest-on-top format as `worklog.md`), then a `---`
  separator.
- Insert the selected blocks at the top of `worklog-archive.md`
  (immediately after its header/separator), preserving their original
  newest-on-top relative order — the most-recently-moved block still
  ends up first.
- Remove those same blocks from the bottom of `worklog.md`, leaving
  everything above them untouched.

Do this as a single coherent edit per file — don't leave either file
in a half-moved state.

## 6. Confirm

Report which date blocks moved (range and count), the resulting line
count of `worklog.md`, and that `worklog-archive.md` was created or
updated.
