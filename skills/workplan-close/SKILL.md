---
name: workplan-close
description: Close out dev/workplan.md once its work is finished — capture the outcome as a dev/worklog.md entry (same placement rules as worklog-entry) and reset workplan.md back to its placeholder, then run the worklog-rotate check. Use when a plan tracked in dev/workplan.md has been fully implemented and it's time to log the outcome and clear the plan.
user-invocable: true
allowed-tools:
  - Read
  - Edit
  - Write
  - Bash(git branch*)
  - Bash(git log*)
  - Bash(wc*)
  - Skill
---

# /workplan-close — log a finished plan and clear it

Handles the "capture the outcome in worklog.md, then clear the plan
back to its placeholder" step of the `dev/` convention in one pass,
so a finished plan never lingers in `workplan.md` after its worklog
entry is written. Finishes by running the same rotation check
`worklog-entry` does — but where that skill only *flags* rotation,
this one triggers `worklog-rotate` directly, since closing out a plan
is exactly the "opportunistic, not mid-work" moment the CLAUDE.md
rotation rule calls for.

Arguments passed: `$ARGUMENTS` — optionally a short scope title for
the worklog entry, if it's not obvious from `workplan.md`'s own goal
statement.

## 1. Confirm there's a plan to close

Check `dev/workplan.md` exists. If it doesn't, tell the user this
project isn't opted into the `dev/` convention and stop. If it's
already at placeholder (no active plan body — just the header
describing its role), tell the user there's nothing to close and
stop.

## 2. Draft the entry

Read `dev/workplan.md` in full: goal, concrete steps, decision points,
and trade-offs already recorded there. Combine with what actually
happened in this conversation (edits made, testing/verification done,
any deviation from the plan as written) — the worklog entry should
describe what happened, not just repeat the plan, if they diverge.

Same body conventions as `worklog-entry`: complete sentences, enough
context that a reader could reconstruct the change without diffing,
more detail than a user-facing changelog would carry.

## 3. Place the entry (same mechanics as worklog-entry)

- Today's date (`YYYY-MM-DD`, the actual current date) and
  `git branch --show-current`.
- Read `dev/worklog.md`; find the first `## YYYY-MM-DD — branch
  <name>` heading (entries are newest-on-top).
  - **Matches today's date and branch**: insert a new
    `### <short scope>` sub-heading immediately after it, above any
    existing sub-headings for today.
  - **Otherwise**: insert a new `## <date> — branch <name>` block at
    the very top of the entries (after the file's intro/`---`, before
    the existing first `##`).

## 4. Reset workplan.md to its placeholder

Overwrite `dev/workplan.md` with just a short header explaining its
role — no active plan body. Before inventing new wording, check
whether this project already has an established placeholder style:
`git log -p --follow -- dev/workplan.md` and look for an earlier
commit where the file held no active plan. If one exists, match its
tone/structure (e.g. this repo's own pattern: a title, pointers to
where architecture/history/backlog live instead, then a one-line
"no plan is active right now — <last thing> finished, see worklog's
'<entry>' entry for the outcome"). If there's no prior placeholder to
match, use generic wording: state that the file is scratch space for
the currently active plan, one plan at a time, overwritten when new
work begins, and that `worklog.md` is the durable record — don't
accumulate stale plans here.

## 5. Run the rotation check

Check the same triggers `worklog-entry` does: total line count of
`dev/worklog.md` (`wc -l`, over ~500 is due) and the date of its
oldest `## ` heading (over ~30 days old is due). If either trips,
invoke the `worklog-rotate` skill now (via the Skill tool) instead of
just flagging it — `worklog-rotate` handles its own confirmation with
the user before moving anything, so chaining into it here is safe.
If neither trips, skip rotation and say so.

## 6. Confirm

Report where the entry landed (new block vs. appended to today's),
that `workplan.md` was reset to placeholder, and the outcome of the
rotation check (skipped, or delegated to `worklog-rotate` — summarize
what that produced if it ran).
