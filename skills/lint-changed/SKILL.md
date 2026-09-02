---
name: lint-changed
description: Lint only the files that actually changed (unstaged, staged, or committed-but-not-yet-merged), using whatever linter the project already has configured, then fix any hits. Use before creating a commit, or whenever the user asks to "lint my changes" / "lint before commit".
user-invocable: true
allowed-tools:
  - Read
  - Edit
  - Grep
  - Glob
  - Bash(git diff*)
  - Bash(git status*)
  - Bash(git rev-parse*)
  - Bash(git merge-base*)
  - Bash(Rscript*)
  - Bash(npx eslint*)
  - Bash(ruff*)
  - Bash(flake8*)
  - Bash(ls*)
---

# /lint-changed — lint only what changed

Runs the project's own configured linter(s) against the files that
have actually changed, fixes what it finds, and re-checks — it never
invents a linter config that isn't already present, and it never runs
`git commit` itself.

Arguments passed: `$ARGUMENTS` (optional — a path or glob to restrict
to; if empty, use the full changed-file set below).

## 1. Compute the changed-file set

Union these, then drop deleted files and dedupe:

- `git diff --name-only` — unstaged changes
- `git diff --name-only --cached` — staged changes
- Committed-but-not-yet-merged changes, if a base is detectable:
  - Try `git rev-parse --abbrev-ref --symbolic-full-name @{u}` for the
    upstream branch.
  - If that fails, fall back to `main` or `master` (whichever exists
    locally, via `git show-ref --verify --quiet refs/heads/main`).
  - If a base was found: `git diff --name-only <merge-base>...HEAD`
    where `<merge-base>` is `git merge-base HEAD <base>`.
  - If no base is resolvable (e.g. no remote, no main/master), skip
    this source rather than guessing.

If `$ARGUMENTS` was given, intersect the changed-file set with it
instead of using the raw argument as a path (so you never lint files
that didn't change).

If the changed-file set is empty, say so and stop — nothing to do.

## 2. Group by extension and dispatch

Only run a linter when the project has actually configured one for
that language. Don't add a config file yourself — that's a separate,
explicit decision for the user to make.

- **`.R`**: for each file, run
  `Rscript -e 'lintr::lint("<file>")'`. This automatically respects
  a project-level `.lintr` if one exists (indent width, disabled
  linters, etc.) — don't pass linter options yourself.
- **`.js` / `.jsx` / `.ts` / `.tsx`**: only if an eslint config is
  present (`.eslintrc*`, `eslint.config.*`, or an `eslintConfig` key
  in `package.json`). Run `npx eslint <files>`.
- **`.py`**: only if a ruff config is present (`ruff` section in
  `pyproject.toml`, or `.ruff.toml`) — run `ruff check <files>`.
  Otherwise, if a `.flake8` or `setup.cfg`/`tox.ini` `[flake8]`
  section exists, run `flake8 <files>`.
- **Anything else**, or a language above with no config found: skip
  it and say explicitly "no linter configured for `<ext>`, skipping"
  rather than picking defaults on the project's behalf.

## 3. Fix and re-check

For every finding reported:

1. Read the flagged line(s) and fix the underlying issue with Edit —
   don't just suppress the lint rule inline unless the finding is a
   false positive for a documented reason.
2. After fixing all findings in a file, re-run the same lint command
   on that file to confirm it's clean.
3. If a finding can't be resolved without a substantive design
   decision (not a style fix), stop and ask rather than guessing —
   e.g. an `object_usage_linter` hit that implies a real bug.

## 4. Report, don't commit

Summarize what was linted, what was fixed, and confirm the changed
files are now lint-clean. This skill never stages or commits — that
stays a separate, explicit step the user (or the calling workflow)
takes afterward.
