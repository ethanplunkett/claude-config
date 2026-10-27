---
name: r-project-init
description: Add R project conventions (research-compendium vs. package layout, tidyverse style, roxygen2 policy) to a project's CLAUDE.md, and merge in R-specific .gitignore/.gitattributes lines. Use when starting a new R project or adding these conventions to an existing one that doesn't have them yet. Does not scaffold files — points to usethis/devtools for that.
user-invocable: true
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - AskUserQuestion
---

# /r-project-init — add R project conventions

Writes R project conventions (structure, tidyverse style, roxygen2
policy) into the project's `CLAUDE.md`, and merges R-specific lines
into `.gitignore`/`.gitattributes`. This skill documents and tidies —
it never creates the R package/script skeleton itself. `usethis` and
`devtools` already do that well (`usethis::create_package()`,
`usethis::use_testthat()`, `usethis::use_mit_license()`, etc.); don't
duplicate their work.

## 1. Ask which structure applies

Ask the user (via `AskUserQuestion` or directly in chat) whether this
project is:

- **Option A — Research compendium**: numbered driver scripts
  (`00_setup.R`, `01_import.R`, ...) plus an `R/` folder of helpers.
  Not a package.
- **Option B — Package**: has (or will have) a `DESCRIPTION` and
  `NAMESPACE`, installable via `devtools::install()`.

Don't guess from existing files unless it's unambiguous (e.g. a
`DESCRIPTION` file already exists → Option B) — confirm with the user
either way, since this choice drives which structure section gets
written.

## 2. Write or merge `CLAUDE.md`

Read `option-a.md` or `option-b.md` (whichever matches step 1) and
`template-style.md`, both alongside this skill.

### No `CLAUDE.md` exists

Write a new `CLAUDE.md`:

```
# CLAUDE.md

Guidance for AI coding assistants (Claude, etc.) working in this repository.

<option-a.md or option-b.md content>

---

<template-style.md content>
```

### `CLAUDE.md` already exists

Read it first. Don't overwrite silently — show the user what the
structure section (option-a/b) and the style/roxygen section
(template-style) would add, and ask whether to:

- **Append** both as new sections at the end, or
- **Merge selectively** — user picks which sections to add, in case
  the file already covers some of this, or
- **Skip** — do nothing.

If appending or merging, insert a `---` separator before each new
section, matching the separators used within the source files.

## 3. Check the existing layout against the target (existing projects only)

Skip this step for a brand-new/empty project.

Using `Glob`, compare what's actually in the project against the
chosen option's target structure (e.g. for Option A: are there
numbered driver scripts? Is there an `R/` folder? Do functions live
loose at the top level instead? For Option B: is there `R/`, `man/`,
`tests/testthat/`?).

Report the gaps as a plain to-do list — e.g. "3 `.R` files at the
project root define functions; the convention expects those under
`R/`." **Never move, rename, or delete existing files yourself** —
this is informational, left for the user to act on.

## 4. Point to scaffolding tools, don't scaffold

For anything structural that's actually missing, name the `usethis`/
`devtools` call that creates it rather than creating it yourself,
e.g.:

- New package skeleton: `usethis::create_package("path")`
- Test harness: `usethis::use_testthat()`
- A vignette: `usethis::use_vignette("name")`
- `data-raw/` convention: `usethis::use_data_raw("name")`

For Option A's compendium layout there's no single blessed function —
just note which folders/numbered scripts are still missing; creating
plain folders and empty numbered `.R` files yourself is fine since
there's no tool for this that would be duplicated.

## 5. Merge `.gitignore` / `.gitattributes`

Read `gitignore-r.txt` and `gitattributes-r.txt`, alongside this
skill.

For each target file (`.gitignore`, `.gitattributes`) in the project:

- **Doesn't exist**: create it with the template's lines.
- **Exists**: append only the lines not already present (exact-line
  match is enough — don't try to dedupe near-equivalents). Leave
  everything already there untouched.

These templates are deliberately R-specific (`.Rhistory`, `renv/
library/`, etc.) — they don't include OS-level entries like
`.DS_Store` or `Thumbs.db`, since those are a personal preference, not
part of this convention. Don't add any OS-level lines yourself.

## 6. Confirm

Report: which `CLAUDE.md` sections were written/appended/skipped, any
structure gaps found (step 3), the scaffolding commands suggested
(step 4), and which `.gitignore`/`.gitattributes` lines were added.
