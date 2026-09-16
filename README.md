# claude-config

Ethan's personal [Claude Code](https://claude.com/claude-code) configuration:
global instructions, settings, and slash-command skills for all projects.

## Contents

- **`CLAUDE.md`** — personal instructions loaded into every session
  (language conventions, commit/PR attribution, push restrictions,
  the `dev/` workplan+worklog convention).
- **`settings.json`** — model, theme, and update-channel preferences.
- **`skills/`** — user-invocable skills backing the `dev/` convention:
  `dev-init`, `new-r-function`, `lint-changed`, `news-entry`,
  `worklog-entry`, `worklog-rotate`, `workplan-close`.

Only these files are tracked; `.gitignore` allowlists them
explicitly, so other local state under `~/.claude` (memory, project
history, credentials) never ends up in this repo.

## Use

Clone to `~/.claude` (or symlink these files in) so Claude Code picks
them up automatically. No install step beyond that.
