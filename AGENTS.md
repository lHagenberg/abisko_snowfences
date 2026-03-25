# AGENTS.md — abisko_snowfences

## Project Overview

R analysis project studying snow fence effects on Arctic vegetation near Abisko, Sweden.
Scripts are R Markdown (`.Rmd`) and Quarto (`.qmd`) notebooks, not a formal R package.
No CI, no automated tests, no renv lockfile.

## Directory Structure

```
1_scripts/           # All analysis notebooks
  sf_data_cleaning.Rmd
  publication_plots.Rmd
  plot_level_bayes_stats.qmd
  biomass/           # Biomass estimation scripts
  community/         # Community composition analysis
  traits/            # Functional trait analysis
2_data/              # Raw and cleaned data (git-ignored)
3_output/            # Figures and statistics output (git-ignored)
```

## Running Scripts

There is no Makefile or build system. Scripts are rendered individually:

```bash
# R Markdown
Rscript -e 'rmarkdown::render("1_scripts/sf_data_cleaning.Rmd")'

# Quarto
quarto render 1_scripts/plot_level_bayes_stats.qmd
```

Rmd files use a custom knit function that outputs to `here::here("outputs")`.
Quarto files use standard `quarto render`.

## Testing

No test framework is configured. There are no test files.
If you add tests, use `testthat` and place them in `tests/testthat/`.

## Linting and Formatting

No `.lintr` or `styler` config exists. If linting is needed:

```r
# Install and run lintr
install.packages("lintr")
lintr::lint_dir("1_scripts/")

# Format with styler
styler::style_dir("1_scripts/")
```

## Code Style

### Naming Conventions
- Use `snake_case` for all variables and functions
- Common abbreviations: `bm` (biomass), `sf` (snowfence), `ctl` (control),
  `gr` (ground layer), `vp` (vascular plant), `sp`/`spec` (species)
- Single-letter variables (`x`, `d`, `tr`) are acceptable for short-lived intermediates

### Imports
- Prefer `library()` over `require()` at the top of each script
- Use `conflicted` to resolve namespace conflicts:
  ```r
  library(conflicted)
  conflict_prefer("select", "dplyr")
  conflict_prefer("filter", "dplyr")
  ```
- Use `::` qualification when conflicts are not resolved via `conflicted`

### Pipe Operator
- Use `%>%` (magrittr) exclusively — do not introduce `|>`

### ggplot2
- Base theme: `theme_classic()`
- Add panel border: `panel.border = element_rect(color = "black", fill = NA, linewidth = 1)`
- Treatment colors: `c("#999999", "#4da6ff")`
- Habitat colors: `c("#917D53", "#667E4E", "#A8AA97")`
- Save with `ggsave()` to `3_output/1_figures/`

### Statistical Modeling
- Frequentist: `glmmTMB`, `lme4::lmer`, `car::Anova`, `emmeans`
- Bayesian: `brms::brm`, `tidybayes` for posteriors
- Diagnostics: `DHARMa::simulateResiduals`, `performance::check_collinearity`
- Model comparison: `AIC()`, `loo::loo()`, `loo_compare()`

### Error Handling
- Use `try()` for operations that may fail (e.g., external tool calls)
- Check results with `inherits(result, "try-error")`
- Avoid `stop()` in notebooks; prefer `warning()` and `error = TRUE` chunk options

### Data I/O
- Read: `readr::read_csv()`, `readxl::read_excel()`, `read.csv()`, `readRDS()`
- Write: `readr::write_csv()`, `openxlsx::saveWorkbook()`, `saveRDS()`
- Prefer `here::here()` for paths; avoid `setwd()` and hardcoded absolute paths

## Key Dependencies

`tidyverse`, `conflicted`, `here`, `brms`, `tidybayes`, `lme4`, `glmmTMB`,
`vegan`, `emmeans`, `DHARMa`, `performance`, `ggpubr`, `patchwork`,
`traitstrap`, `gllvm`, `readxl`, `openxlsx`, `osfr`

## Common Pitfalls

- Some older scripts contain hardcoded Windows paths — replace with `here::here()`
- The function `extr_3()` is duplicated across multiple scripts — prefer defining it once in a shared helper
- `.RData` is git-ignored but present on disk — do not commit it
- Data and output directories (`2_data/`, `3_output/`) are git-ignored
