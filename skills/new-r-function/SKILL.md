---
name: new-r-function
description: Scaffold a new one-function-per-file R script under R/ (BirdFlowR-style convention) with a roxygen doc skeleton, matching the project's own indent width. Use when adding a new R function to a project that keeps one function per file under R/.
user-invocable: true
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash(ls*)
---

# /new-r-function — scaffold a one-function-per-file R script

Creates `R/<snake_case_name>.R` with a roxygen skeleton, matching the
project's own style conventions instead of a fixed default. This
scaffolds structure only — the user (or a follow-up turn) fills in
the function body.

Arguments passed: `$ARGUMENTS` — expects a function name and,
optionally, its parameters and a short description, e.g.
`calculate_tidal_range depth_col, time_col — compute high/low tide range from a depth series`.
If the description is missing, ask for a one-line summary of what the
function does before writing the doc skeleton (a roxygen title needs
it) — don't invent one.

## 1. Confirm the convention applies

Check that `R/` exists at the project root. If it doesn't, tell the
user this project doesn't appear to use the one-function-per-file
`R/` convention and stop — don't create the directory yourself.

## 2. Derive the filename

Convert the function name to `snake_case` if it isn't already
(nouns for variables, verbs for functions — per tidyverse naming, so
nudge the user if the name reads like a noun for what is clearly an
action). Target path: `R/<name>.R`.

If that file already exists, stop and say so — never overwrite an
existing function file.

## 3. Detect the project's indent width

Don't hardcode a width — check, in order, and use the first match:

1. `.lintr` at the project root: look for
   `indentation_linter(indent = N)` (or `indent=N`) inside a
   `linters_with_defaults(...)` call and use `N`.
2. `<ProjectName>.Rproj` at the project root: look for
   `NumSpacesForTab: N` and use `N`.
3. Otherwise default to **2 spaces** (tidyverse style guide default).

## 4. Detect the project's pipe style

Grep existing files under `R/` for `%>%`. If any use it, match that
project and use `%>%` in the new file (and `magrittr`'s `.` placeholder
where needed). Otherwise default to the base pipe `|>` — never mix the
two styles within one project.

## 5. Write the file

Always double-quoted strings unless a quote mark is inside the
string. Use the indent width from step 3 and the pipe style from
step 4 for the function body and any wrapped argument lists.

Template (shown with a 2-space indent; substitute the detected
width):

```r
#' <One-line title, sentence case, verb phrase for what it does>
#'
#' <Optional one extra sentence of detail, only if it adds real
#' information beyond the title.>
#'
#' @param <arg1> <Description.>
#' @param <arg2> <Description.>
#' @return <Description of the return value.>
#' @examples
#' # <name>(<example args>)
<name> <- function(<arg1>, <arg2>) {

}
```

Rules for filling this in:

- One `@param` per argument, in signature order.
- If a parameter has a default value, show it in the signature
  (`arg = default`) and mention the default in its `@param` line if
  it's not obvious.
- Omit `@examples` only if no sensible one-liner example exists yet —
  prefer including a plausible (possibly commented-out) call.
- Leave the function body empty (just the braces) unless the
  invocation included enough detail to write real logic — this skill
  scaffolds the file, it doesn't design the implementation.
- Use Markdown inside the roxygen block (backticks for code,
  `[text](url)` for links) per this convention's eventual-package
  migration plan — don't write plain-text-only docs.

## 6. Confirm

Report the file path created and remind the user (if the body was
left empty) that the implementation still needs to be written.
