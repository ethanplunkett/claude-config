## Code Style

Follow the **tidyverse style guide** (https://style.tidyverse.org/) for all
R code:

- `snake_case` for variable and function names (no camelCase, no dots).
- Use `<-` for assignment, never `=`.
- Use the native pipe `|>` (or `%>%` if the project already uses magrittr
  elsewhere — stay consistent within a single project, don't mix).
- Spaces around binary operators (`x + y`, not `x+y`); no space before a
  comma, one space after.
- Lines wrapped at ~80 characters where reasonable. 
- Prefer `tidyverse` verbs (`dplyr`, `purrr`, `tidyr`) over base R
  equivalents when both are reasonably idiomatic, unless the project has
  already committed to a base-R/data.table style — match existing code.
- One logical statement per line; avoid deeply nested pipes without
  intermediate, well-named objects.

---

## Function Documentation: roxygen2 comments, always

Every function — **whether or not this project is a formal package** —
must be documented with roxygen2-style comments directly above its
definition. This applies to functions in `/R` in both the compendium and
package structures.

Minimum required tags:

```r
#' Short, one-line title
#'
#' One or two sentences describing what the function does and why.
#'
#' @param x Description of each parameter, including expected type/shape.
#' @param y Description of each parameter.
#'
#' @return Description of the return value (type/structure).
#'
#' @examples
#' my_function(x = 1, y = 2)
my_function <- function(x, y) {
  x + y
}
```
