---
name: news-entry
description: Add a bullet to the top-level NEWS.md changelog in the BirdFlowR setext-date-heading format — terse, user-facing highlights only, distinct from dev/worklog.md's full detail. Use after a noteworthy change ships, when the project keeps a NEWS.md.
user-invocable: true
allowed-tools:
  - Read
  - Edit
  - Write
---

# /news-entry — add a NEWS.md bullet

Handles the mechanics of the BirdFlowR-style `NEWS.md` format (setext
date heading, asterisk bullets, hanging indent) so they don't need to
be re-derived each time, and keeps `NEWS.md` terse the way it's meant
to be — highlights only, not the worklog's blow-by-blow detail.

Arguments passed: `$ARGUMENTS` — optionally the change to log. If not
given, draft it from what was actually done earlier in this
conversation, condensed to a highlight — don't just copy a worklog
entry's full detail in here.

## 1. Confirm NEWS.md exists

Check for a top-level `NEWS.md`. Unlike `dev/`, this file is
user-facing, so don't create it silently if it's missing — ask the
user whether they want one started, and only proceed once they say
yes.

## 2. Draft the bullet(s)

Keep it terse: what changed and why it matters to someone reading the
changelog, not implementation detail. Back-tick any function or file
names. Use semantic line breaks (one sentence or clause per line) —
this file follows the same Markdown convention as `CLAUDE.md`,
`dev/*.md`, and READMEs.

## 3. Place the entry

Read `NEWS.md`. Its entries are setext-style date headings:

```
YYYY-MM-DD
==========

*   First bullet, wrapped lines indented 4 spaces to align under
    the text after the marker.

*   Second bullet.
```

The underline is the same length as the date string above it (10
`=` characters for a 10-character `YYYY-MM-DD` date).

- **The first heading's date is today**: add the new bullet(s) after
  the existing bullets under that heading (don't create a second
  heading for the same day).
- **Otherwise**: insert a brand-new heading block at the very top of
  the file, above the existing first heading, formatted as shown
  above, followed by a blank line before that first heading.

Each bullet: `*   ` (asterisk, three spaces — four-space hanging
indent), with continuation lines indented to align under the first
character after the marker.

## 4. Confirm

Report where the bullet landed (new heading vs. appended to today's)
and show the text as written, so the user can adjust wording before
it's committed.
