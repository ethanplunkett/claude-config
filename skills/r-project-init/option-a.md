## Project Structure

This project follows the research compendium (non-package) structure:

project/
├── CLAUDE.md
├── 00_setup.R # Global parameters, constants, paths, options.
│ # Sourced first by every other numbered script.
├── 01_import.R # Reads raw data, minimal transformation only.
├── 02_clean.R # Cleaning, tidying, merging.
├── 03_analysis.R # Modeling / statistical analysis.
├── 04_report.R # Tables, figures, final outputs.  Could also be markdown doc.
├── R/ # All custom functions live here, one concern per file.
│ ├── clean_helpers.R
│ ├── analysis_helpers.R
│ └── plot_helpers.R
├── tests/
│ └── testthat/
│ ├── test-clean_helpers.R
│ └── test-analysis_helpers.R
├── data/
│ ├── raw/ # Read-only. Never written to by any script.
│ ├── interim/ # Intermediate, reproducible outputs. Safe to delete.
│ └── output/ # Final outputs: cleaned data, tables, figures.
└── renv.lock

Rules for this structure:
- Numbered top-level scripts (`01_`, `02_`, ...) are **drivers only**. They
  should call functions defined in `/R`, not define substantial logic inline.
  They don’t need to have the names above but the name should 
  clearly state what they do.
- `00_setup.R` is sourced at the top of every other numbered script. It
  holds shared parameters (file paths, model constants, random seeds,
  package loads via `library()`). It should also source all the code in `R/`.
  Never duplicate a parameter outside of
  `00_setup.R` — update it there and let every script inherit the change.
-  Use relative paths from the project base directory (assuming RStudio)
  (e.g., "data/raw/file.csv").  Do not add here::here() or setwd() unnecessarily.
  Exception: for .Rmd/.qmd files that do not live in the project root 
  (e.g., in vignettes/, reports/, or docs/) use `here::here()` to 
  re-anchor paths to the project root. Do not use `setwd()` for this purpose.
- Never use absolute paths (e.g., "/Users/name/project/data/raw/file.csv").
- Files in `data/raw/` are never modified or overwritten by code, although 
  they can be written by code that downloads raw data. 
