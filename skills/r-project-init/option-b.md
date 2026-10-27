## Project Structure

This project follows the package structure:

project/
├── CLAUDE.md
├── DESCRIPTION
├── NAMESPACE
├── R/ # One function (or tight family of functions) per file.
├── man/ # Auto-generated from roxygen2. Do not hand-edit.
├── tests/
│ └── testthat/
│ └── test-*.R # One test file per R/ file, mirrored by name.
├── vignettes/ # Longer worked examples, if needed.
└── data-raw/ # Scripts that generate internal/example datasets.

Rules for this structure:
- Every exported function needs full roxygen2 documentation (see below).
- Run `devtools::document()` after changing any roxygen comments — never
  hand-edit files in `man/`.
- Run `devtools::check()` before considering a change complete.
