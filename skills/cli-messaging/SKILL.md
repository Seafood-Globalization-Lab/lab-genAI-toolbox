---
name: cli-messaging
description: >
  Write user-facing messages in the artis R package using the cli package.
  Use when adding, updating, or reviewing cli messages in any artis function:
  progress reporting, data quality warnings, validation checks, and developer
  action items.
license: MIT
metadata:
  author:
    - name: Althea Marks
      orcid: 0000-0002-9370-9128
      url: https://github.com/theamarks/
  repository: https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox
  version: 1.0.0
---

## Purpose

This skill guides an AI assistant in writing `cli` messages for functions in
the `artis` R package. All user-facing output must use the `cli` package.
Never use `message()`, `cat()`, or `print()` for user-facing output.

---

## Package-Level Rules

- **Only `cli`** for all user-facing output — no `message()`, `cat()`, or
  `print()`
- Add `@import cli` to the roxygen2 header of any function that uses `cli`
- Always call cli functions as `cli::cli_*()` with the namespace prefix
- Messages should be actionable: tell the developer what happened and, when
  relevant, what to do next

---

## Message Function Reference

### Headers

Use headers to introduce a named block of related messages. Headers render with
a horizontal rule.

| Function | When to use |
|---|---|
| `cli::cli_h1()` | Top-level section header — use sparingly, typically to introduce a list of items (e.g. `cli_h1("Missing scientific names")` before `cli_ul()`) |
| `cli::cli_h2()` | Sub-section header — introduces a block of alerts for a named check or result group |

```r
cli::cli_h2("Results: Fishbase / Sealifebase matching and synonym resolution")
cli::cli_h2("Malformed prod taxa manual corrections table - Check 1")
cli::cli_h1("Missing scientific names")
```

---

### Alert Functions

Alert functions emit a single message line with a type prefix symbol.

| Function | Symbol | When to use |
|---|---|---|
| `cli::cli_alert_success()` | ✔ | Positive outcome — all items matched, resolved, or passed a check |
| `cli::cli_alert_warning()` | ! | Data quality issue requiring developer attention but not stopping execution |
| `cli::cli_alert_info()` | ℹ | Supporting detail, next step, or developer instruction |
| `cli::cli_alert_danger()` | ✖ | Critical issue — downstream results may be wrong or incomplete |

```r
cli::cli_alert_success("All production taxa matched to Fishbase / Sealifebase")
cli::cli_alert_warning("{.val {no(n_missing)}} unmatched production taxa")
cli::cli_alert_info("Add manual fixes to {.fn fill_prod_taxa_gaps}")
cli::cli_alert_danger("{length(missing_scinames)} {.field SciName}s in {.field the_prod_data} are NOT found in {.field prod_taxa_classification_clean}.")
```

---

### `cli::cli_warn()` with Named Vector

Use `cli::cli_warn()` with a named character vector for structured warnings.
The `"!"` key is the main message; `"i"` keys are supporting details. This
pattern is for more formal warnings that should appear in the R warning system.

```r
cli::cli_warn(c(
  "!" = "{length(missing_species)} species in HS taxa match are not in production data",
  "i" = "Missing species: {.val {missing_species}}",
  "i" = "Check HS taxa matching process or production data completeness"
))
```

Use `cli::cli_warn()` (rather than `cli::cli_alert_warning()`) when the issue
is formal enough to warrant an R-level warning that can be caught with
`tryCatch()` or suppressed with `suppressWarnings()`.

---

### `cli::cli_ul()` — Bulleted List

Use for enumerating developer action items or a list of values (e.g., missing
names). Each element of the `c()` vector becomes one bullet.

```r
cli::cli_ul(c(
  "Manual corrections required for taxa names returned in {.var taxa_need_corrections} dataframe",
  "Open {.file ./R/build_corr_tbl_prod_sciname.R} to add manual corrections — follow instructions in help page {.code ?build_corr_tbl_prod_sciname()}",
  "Run {.code devtools::load_all} or {.code devtools::install} and {.code library(artis)} to integrate changes",
  "Proceed running {.file 01-clean-input-data.R}"
))

# Also used to list values directly
cli::cli_ul(missing_scinames)
```

---

## Inline Markup Reference

`cli` interprets `{.class text}` markup inside message strings. Use the
appropriate class for each type of value.

| Markup | Use for | Example |
|---|---|---|
| `{.val {x}}` | A concrete value, count, or vector of values | `"{.val {n_resolved}} taxa resolved"` |
| `{.field name}` | A column name or named data field | `"{.field SciName}"`, `"{.field sciname_prod}"` |
| `{.fn name}` | A function name (no parentheses in the markup itself) | `"{.fn build_corr_tbl_prod_sciname}"` |
| `{.file path}` | A file path | `"{.file ./R/build_corr_tbl_prod_sciname.R}"` |
| `{.code expr}` | An R expression the developer should run | `"{.code devtools::load_all()}"` |
| `{.var name}` | A variable name in the calling environment | `"{.var taxa_need_corrections}"` |
| `{.strong text}` | Bold emphasis for a label or heading within text | `"{.strong Developer Notes}:"` |

Bare `{n}` (without a class) interpolates a value as plain text. Use it for
simple count interpolation when `.val` formatting is not needed.

---

## Pluralization Helpers

Use `cli`'s built-in pluralization so messages read correctly for both singular
and plural counts.

| Pattern | Effect |
|---|---|
| `{?s}` | Appends "s" when the preceding count is ≠ 1 |
| `{no(n)}` | Substitutes "no" when `n == 0`, otherwise inserts `n` |

```r
# {?s} — plural suffix
cli::cli_alert_warning("{nrow(missing_habitat_scinames)} {.field SciName}{?s} missing habitat information")

# {no()} — "no" instead of 0
cli::cli_alert_warning("{.val {no(n_resolved)}} taxa were identified as synonyms; no names were resolved")
```

---

## Message Architecture Patterns

### Pattern 1 — Conditional success/warning block

The most common pattern: wrap messages in an `if/else if` block checking a
count. Use `cli_h2()` once before the block (or inside it), then emit the
appropriate alert based on the outcome.

```r
cli::cli_h2("Results: Fishbase / Sealifebase matching and synonym resolution")

if (n_resolved > 0) {
  cli::cli_alert_success("{.val {n_resolved}} taxa were synonyms resolved to accepted names")
} else {
  cli::cli_alert_warning("{.val {no(n_resolved)}} taxa were identified as synonyms; no names were resolved")
}

if (n_missing > 0) {
  cli::cli_alert_warning("Found {.val {no(n_missing)}} unmatched production taxa")
  cli::cli_alert_info("{.strong Developer Notes}:")
  cli::cli_ul(c(
    "Manual corrections required — inspect {.var taxa_need_corrections}",
    "Open {.file ./R/build_corr_tbl_prod_sciname.R} and follow the instructions in {.code ?build_corr_tbl_prod_sciname()}"
  ))
} else if (n_missing == 0) {
  cli::cli_alert_success("All production taxa matched to Fishbase / Sealifebase")
  cli::cli_alert_info("No further manual corrections required — proceed with clean input data script")
}
```

### Pattern 2 — Named validation check block

Used when a function runs multiple named integrity checks. Each check gets its
own `cli_h2()` header so the developer can scan the output and identify which
check failed.

```r
if (nrow(n_raw) > 0) {
  cli::cli_h2("Malformed prod taxa manual corrections table - Check 1")
  cli::cli_alert_warning("{.fn build_corr_tbl_prod_sciname} table has duplicate {.field sciname_prod} values.")
  cli::cli_alert_info("Multiple rows detected for: {n_raw$sciname_prod}")
}

if (nrow(add_to_one) > 0) {
  cli::cli_h2("Malformed prod taxa manual corrections table - Check 2")
  cli::cli_alert_warning("Some {.field sciname_prod} values have more than one {.field Species01, Genus01, Family01, Other01} assignments.")
  cli::cli_alert_info("Check {.fn build_corr_tbl_prod_sciname} for duplicate {.field sciname_prod} values or other entry errors.")
}

if (nrow(not_valid_taxa) > 0) {
  cli::cli_h2("Malformed prod taxa manual corrections table - Check 3")
  cli::cli_alert_warning(
    "{.val {nrow(not_valid_taxa)}} {.field sciname_corrected} value{?s} in {.fn build_corr_tbl_prod_sciname} {?is/are} not found within
    Fishbase and Sealifebase {.val {snapshot}} version taxa tables: {.val {not_valid_taxa}}"
  )
  cli::cli_alert_info(
    "Make corrections where possible, but there may be instances where we choose to insert taxa not represented
    in FB/SLB into the ARTIS. These instances are contained within the downstream {.fn fill_taxa_classification_gaps} function."
  )
}
```

### Pattern 3 — Danger with list of values

Use `cli_alert_danger()` for critical issues, followed by `cli_h1()` and
`cli_ul()` to enumerate the problematic values.

```r
if (length(missing_scinames) > 0) {
  cli::cli_alert_danger(
    "{length(missing_scinames)} {.field SciName}s in {.field the_prod_data} are NOT found
    in {.field prod_taxa_classification_clean}. These names could not be matched to
    {.file fishbase} or {.file sealifebase}. They may not properly match to hs product
    codes in {.fn match_*} functions downstream in {.file ./01-clean-input-data.R}.
    Missing names written to {.file outdir} as {.file missing_scinames_yyyy-mm-dd_HHMM.csv}
    to add to manual corrections in {.fn build_corr_tbl_prod_sciname}."
  )
  cli::cli_h1("Missing scientific names")
  cli::cli_ul(missing_scinames)
}
```

### Pattern 4 — Loop progress message

When iterating, emit a brief `cli_alert_info()` at the top of each iteration
so the developer can track progress.

```r
for (i in 1:length(HS_year)) {
  hs_version <- paste("HS", HS_year[i], sep = "")
  cli::cli_alert_info("{.field {hs_version}} Matching taxa/products/conversion factors")
  # ... loop body
}
```

---

## Message Tone and Content Guidelines

- **Warning messages** state the count first, then describe what is wrong.
  e.g., `"{.val {n}} {.field SciName}{?s} missing habitat information"`
- **Info messages** tell the developer what to do or where to look.
  e.g., `"Add manual fixes to {.fn fill_prod_taxa_gaps}"`
- **Success messages** confirm that a check passed or a step completed cleanly.
  e.g., `"All production taxa matched to Fishbase / Sealifebase"`
- **Danger messages** describe the consequence for downstream steps, not just
  the immediate problem.
- Keep message text concise. Use `cli_ul()` to offload enumerated items rather
  than embedding long lists in alert text.
- Use American English spelling (e.g., "standardizing", "visualization").
- No emojis unless the user explicitly requests them.

---

## What Not to Do

- **Do not** use `message()`, `cat()`, `print()`, or `warning()` for
  user-facing output — only `cli` functions
- **Do not** omit the `cli::` namespace prefix
- **Do not** put developer action items inside a `cli_alert_warning()` — use
  `cli_alert_info()` for instructions and `cli_alert_warning()` for the
  problem statement
- **Do not** use `cli_alert_danger()` for routine data quality checks —
  reserve it for issues that will break downstream steps
- **Do not** embed long vectors of values directly in alert text — use
  `cli_ul()` instead
- **Do not** use `cli_warn()` (R-level warning) for informal notices — use
  `cli_alert_warning()` for those

---

## Complete Example

The following is a complete, production-style message block from
`match_prod_taxa_to_fb_slb()`:

```r
# Compute counts
n_missing  <- length(missing_scinames_post_syn)
n_resolved <- nrow(synonym_resolutions)

# Open with a section header
cli::cli_h2("Results: Fishbase / Sealifebase matching and synonym resolution")

# Synonym resolution result
if (n_resolved > 0) {
  cli::cli_alert_success("{.val {n_resolved}} taxa were synonyms resolved to accepted names")
} else {
  cli::cli_alert_warning("{.val {no(n_resolved)}} taxa were identified as synonyms; no names were resolved")
}

# Unmatched taxa result
if (n_missing > 0) {
  cli::cli_alert_warning("Found {.val {no(n_missing)}} unmatched production taxa")
  cli::cli_alert_info("{.strong Developer Notes}:")
  cli::cli_ul(c(
    "Manual corrections required for taxa names returned in {.var taxa_need_corrections} dataframe",
    "Open {.file ./R/build_corr_tbl_prod_sciname.R} to add manual corrections — follow instructions in help page {.code ?build_corr_tbl_prod_sciname()}",
    "Open Fishbase taxa table with {.code fb_taxa <- fread(file.path(current_fb_slb_dir, 'fb_taxa_info.csv'), data.table = FALSE)}",
    "Run {.code devtools::load_all} or {.code devtools::install} and {.code library(artis)} to integrate changes",
    "Proceed running {.file 01-clean-input-data.R}; the second pass of {.fn match_prod_taxa_to_fb_slb} will apply new corrections"
  ))
} else if (n_missing == 0) {
  cli::cli_alert_success("All production taxa matched to Fishbase / Sealifebase")
  cli::cli_alert_info("No further manual corrections required — proceed with clean input data script")
}
```
