# claude-config

Ethan's personal [Claude Code](https://claude.com/claude-code) configuration:
global instructions, settings, and slash-command skills for all projects.

## Contents

- **`CLAUDE.md`** — personal instructions loaded into every session
  (language conventions, commit/PR attribution, push restrictions,
  the `dev/` workplan+worklog convention).
- **`settings.json`** — model, theme, and update-channel preferences.
  Never put secrets here (see "Secrets" below).
- **`skills/`** — user-invocable skills: `dev-init`, `new-r-function`,
  `lint-changed`, `news-entry`, `worklog-entry`, `worklog-rotate`,
  `workplan-close` (backing the `dev/` convention), and
  `r-project-init` (adds R project conventions — structure, tidyverse
  style, roxygen2 — to a project's `CLAUDE.md`, and merges in
  R-specific `.gitignore`/`.gitattributes` lines; doesn't scaffold
  files itself, points to `usethis`/`devtools` for that).
- **`.githooks/pre-commit`** — blocks commits that look like they add
  a credential (see "Secrets" below).

Only these files are tracked; `.gitignore` allowlists them
explicitly, so other local state under `~/.claude` (memory, project
history, credentials) never ends up in this repo.

## Use

Clone to `~/.claude` (or symlink these files in) so Claude Code picks
them up automatically. Then, once per clone, enable the pre-commit
hook:

```sh
git config core.hooksPath .githooks
```

## Secrets

Machine-specific credentials (e.g. `ANTHROPIC_AUTH_TOKEN` for a
non-default API endpoint) must never go in tracked `settings.json`.
Use `apiKeyHelper` instead — a settings.json key pointing at a local,
untracked script that prints the token to stdout (e.g. reading it
from the OS keychain or an untracked file). `ANTHROPIC_BASE_URL` is
fine to track as long as it's a plain endpoint with no embedded
credential.

The `.githooks/pre-commit` hook is a backstop, not the primary
defense — it does a best-effort regex check (plus a full `gitleaks`
scan if that's installed on PATH), but it can't catch everything.
