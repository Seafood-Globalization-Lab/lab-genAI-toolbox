---
name: roxygen2-function-documentation
description: Write and update well-structured roxygen2 documentation for R package functions. Use when the user asks to write, update, or draft a roxygen2 header for a function. 
license: MIT
metadata:
  author:
    - name: Althea Marks
      orcid: 0000-0002-9370-9128
      url: https://github.com/theamarks/
  repository: https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox
  version: 0.3.0
---

## Purpose

This skill guides an AI assistant in writing and updating roxygen2 documentation
for functions in a lab R package. All functions are exported and require full
documentation. The goal is consistent, accurate, developer-facing documentation
that reflects both official roxygen2 conventions and lab package style.

---

## Package Context

### How to establish package context

**Before writing or updating any documentation**, read the repo root `README.md`
to establish package context. Extract the following from it:

- What the package does — scientific or analytical purpose
- What the package provides — main categories of tools or functions
- How functions are organized — pipeline, module, utility, or other structure
- Any domain-specific terminology, naming conventions, or key concepts

This context is essential for writing accurate `@details` (pipeline narrative)
and `@seealso` (direct relationships) sections. Do not begin documentation
until this context is established.

### If README.md is missing or insufficient

If the repo root `README.md` does not exist, or exists but does not provide
enough information to understand the package structure and purpose, do not
guess or proceed with incomplete context. Instead, ask the user:

> The `README.md` does not contain enough context for me to document this
> function accurately. Could you point me to another file that describes the
> package? For example:
> - `DESCRIPTION` — for a brief package summary
> - A vignette in `vignettes/` — for a narrative overview
> - A specific source file — if you can point me to where this function fits
>   in the pipeline

Wait for the user's response before proceeding.

### DESCRIPTION configuration

Also check the `DESCRIPTION` file for roxygen2 configuration. If it contains:

```
Roxygen: list(markdown = TRUE)
```

then markdown is enabled package-wide. Do not add `@md` tags to individual
blocks.

---

## Workflow: Writing Documentation from Scratch

### Step 1 — Read the full function body before writing anything

Look for:
- What the function does at a high level (for `@description`)
- Input types and roles (for `@param`)
- What is returned — shape, columns, rows, downstream usage (for `@return`)
- Integrity checks, warnings, or validation logic (for `@details` subsections)
- Manual correction blocks with any scoping conditions (for `@details`)
- Any dropped or filtered rows with a specific rationale (for `@note`)
- Calls to other package functions — both callers and callees (for `@seealso`)
- Any external reference data or documentation URLs (for `@seealso`)

### Step 2 — Identify the function's place in the package

Ask:
- What function calls this one?
- What functions does this one call?
- Does it read from disk? Write to disk?
- Is its output used by multiple downstream functions?

This context drives `@details` (pipeline narrative) and `@seealso` (link list).

### Step 3 — Write the header in tag order

Draft the title first: one line, action-oriented, sentence case. Then write
`@description` as the second paragraph. Then work through the remaining tags
in the order specified in the Tag Reference below, completing each section
before moving to the next.

---

## Tag Reference and Style Rules

### Tag order

Always write tags in this order:

```
title (implicit, first line)
@description (if multi-paragraph; otherwise implicit second paragraph)
@details (if needed)
@param (one per parameter, in argument order)
@return
@note (if needed)
@seealso
@import / @importFrom
@export
```

---

### Title

- One line, sentence case, no trailing full stop
- Describes what the function does — use action-oriented wording
- Think about how a developer would search for this function

```r
#' Clean and validate input records table
```

---

### `@description`

- One short paragraph summarizing the goal of the function
- If the description is a single paragraph with no blank lines, write it
  implicitly (no tag needed) as the second paragraph after the title
- Use an explicit `@description` tag only if the description spans multiple
  paragraphs (i.e. contains a blank `#'` line)
- Do not repeat the title verbatim; rephrase it
- Inline code (column names, function arguments, file names) in backticks

```r
#' Transforms a raw input table into a validated, standardized form suitable
#' for downstream processing, filtering invalid rows and normalizing column
#' values according to the package reference data.
```

---

### `@details`

- Use for extended explanation: pipeline context, logic, checks, caveats
- Opens with pipeline context: what calls this function, what it produces,
  where the output goes, which downstream functions consume it
- Use `##` markdown subsections only when there are multiple distinct
  conceptual sections (e.g. integrity checks, manual corrections). Do not
  add subsections for a single topic.
- Use bullet lists (`*`) for enumerating related items (e.g. downstream
  consumers, check types)
- Use **bold** (`**text**`) for named checks or concepts within a list
- Inline code in backticks throughout

```r
#' @details
#' Called inside [run_pipeline()] as the first validation step. The output is
#' passed directly to [process_records()] and also consumed by
#' [generate_summary()]:
#'
#' * [process_records()] — applies transformation logic to validated rows
#' * [generate_summary()] — aggregates validated records for reporting
#'
#' ## Data integrity checks
#'
#' Runs two data integrity checks and emits `cli` warnings if violations are
#' found:
#'
#' * **ID uniqueness check** — detects duplicate row identifiers that would
#'   cause one-to-many join errors downstream.
#' * **Required fields check** — detects rows where mandatory columns are `NA`.
#'
#' ## Manual corrections
#'
#' Manual corrections are applied after initial validation and before the
#' integrity checks. See the *Apply manual corrections* section of the
#' function body.
```

---

### `@param`

- One `@param` per argument, in the same order as the function signature
- Format: `Type. Description sentence.`
  - Type is capitalized (e.g. `Character.`, `Data frame.`, `Logical.`)
  - Description ends with a full stop
  - If not obvious from the name, explain what the argument does
- Inline code for argument values, column names, function calls
- For arguments with a fixed set of allowed values, list them inline with
  backtick-quoted strings
- For arguments with default values that are non-obvious, document the default
- For arguments that accept `NULL`, note it explicitly in the type and document
  the behaviour: e.g. `Character or \`NULL\`. If \`NULL\`, [describe fallback
  behaviour]. Default: \`NULL\`.`

```r
#' @param the_df Data frame. Raw input table to be validated and cleaned.
#' @param the_version Character. The reference data version string (e.g.
#'   `"25.04"`). Used to scope manual corrections to the correct version and
#'   prevent corrections from propagating silently into future data releases.
#' @param the_source Character. One of `"sourceA"` or `"sourceB"`. Used
#'   alongside `the_version` to scope manual corrections to the correct
#'   data source.
#' @param verbose Logical or `NULL`. If `TRUE`, emits progress messages via
#'   `cli`. If `NULL`, falls back to the package-level option. Default: `NULL`.
```

---

### `@return`

- Briefly describe the type and shape of the output
- For data frames, follow the official roxygen2 pattern: describe how the
  output relates to the input, then use a bullet list to enumerate key
  properties (columns present, row semantics, grain of the output)
- Also note downstream usage where it adds clarity
- Do not describe implementation details
- For functions called for their side effects (writing to disk, printing,
  modifying global state), use: `@return Called for its side effects. Returns
  \`invisible(NULL)\`.` — and describe the side effect in `@details` instead

Simple case:
```r
#' @return A data frame with one row per validated input record.
```

Richer case (data frame with notable structure):
```r
#' @return
#' A data frame with one row per validated input record. The output has the
#' following properties:
#'
#' * Rows represent individual validated records after filtering.
#' * Columns include `id`, `name`, `year`, `value`.
#' * Passed directly to [process_records()] for downstream transformation.
```

Side-effect case:
```r
#' @return Called for its side effects. Returns `invisible(NULL)`. Writes
#'   `validated_records.csv` to the path specified by `output_path`.
```

---

### `@note`

- Use for implementation caveats, data quirks, or filtering decisions that
  don't fit naturally in `@details`
- Typically one or two sentences
- Common uses: rows dropped for a specific reason, known upstream data issues,
  version-specific behaviour
- Inline code for column names and values

```r
#' @note Rows where `id` is `0` or `NA` are dropped prior to validation —
#'   these represent placeholder entries not yet assigned a valid identifier.
```

---

### `@seealso`

- Always written as an unordered bullet list
- Include every function that calls this one and every function this one calls,
  with a brief `—` dash description of the relationship
- Include external documentation URLs as a final bullet where relevant
- Use `[function()]` link syntax throughout
- Do not include functions that are only indirectly related (connected via an
  intermediate function) — use `@details` for pipeline narrative instead

```r
#' @seealso
#' * [run_pipeline()] — calls this function as the first validation step
#' * [process_records()] — receives the validated output of this function
#' * [generate_summary()] — also consumes the validated output
#' * Reference data documentation: <https://example.org/reference-data-docs>
```

---

### Import tags

- Use `@import pkg` for packages used pervasively in the function body
- Use `@importFrom pkg fun` for single function imports
- Document only the imports actually used in the function body — do not copy
  a fixed list from another function without checking
- Common lab patterns (include only those that apply):

```r
#' @import dplyr
#' @import cli
#' @importFrom magrittr %>%
#' @importFrom rlang .data
```

---

### `@export`

- All functions in this package are exported
- Always include `@export` as the final tag

---

## Workflow: Updating Existing Documentation

1. Read the current roxygen2 block in full
2. Read the current function body in full
3. Update any tags where the function body has changed
4. For any content in the existing block that cannot be verified from the
   function body alone (e.g. manually written caveats in `@note`, pipeline
   context in `@details`), **preserve it and flag it** to the developer with
   a comment such as:
   > ⚠️ Could not verify this from the function body — please confirm it is
   > still accurate: `[quoted text]`
5. Do not silently delete content you cannot verify

---

## Complete Example

```r
#' Clean and validate input records table
#'
#' Transforms a raw input table into a validated, standardized form suitable
#' for downstream processing, filtering invalid rows and normalizing column
#' values according to the package reference data.
#'
#' @details
#' Called inside [run_pipeline()] as the first validation step. The output is
#' passed directly to [process_records()] and also consumed by
#' [generate_summary()]:
#'
#' * [process_records()] — applies transformation logic to validated rows
#' * [generate_summary()] — aggregates validated records for reporting
#'
#' ## Data integrity checks
#'
#' Runs two data integrity checks and emits `cli` warnings if violations are
#' found:
#'
#' * **ID uniqueness check** — detects duplicate row identifiers that would
#'   cause one-to-many join errors downstream.
#' * **Required fields check** — detects rows where mandatory columns are `NA`.
#'
#' ## Manual corrections
#'
#' Manual corrections are applied after initial validation and before the
#' integrity checks. See the *Apply manual corrections* section of the
#' function body.
#'
#' @param the_df Data frame. Raw input table to be validated and cleaned.
#' @param the_version Character. The reference data version string (e.g.
#'   `"25.04"`). Used to scope manual corrections to the correct version and
#'   prevent corrections from propagating silently into future data releases.
#' @param the_source Character. One of `"sourceA"` or `"sourceB"`. Used
#'   alongside `the_version` to scope manual corrections to the correct
#'   data source.
#'
#' @return A data frame with one row per validated input record.
#'
#' @note Rows where `id` is `0` or `NA` are dropped prior to validation —
#'   these represent placeholder entries not yet assigned a valid identifier.
#'
#' @seealso
#' * [run_pipeline()] — calls this function as the first validation step
#' * [process_records()] — receives the validated output of this function
#' * [generate_summary()] — also consumes the validated output
#' * Reference data documentation: <https://example.org/reference-data-docs>
#'
#' @import dplyr
#' @import cli
#' @importFrom magrittr %>%
#' @export
clean_validate_input <- function(the_df, the_version, the_source) {
  # function body
}
```

---

## Common Mistakes to Avoid

- **Do not** put extended pipeline context or logic inside `@description` —
  that belongs in `@details`
- **Do not** add `##` subsections in `@details` unless there are genuinely
  multiple distinct conceptual sections
- **Do not** add `@md` tags to individual blocks when markdown is enabled
  package-wide via `Roxygen: list(markdown = TRUE)` in DESCRIPTION
- **Do not** use `@examples`, `@inherit`, or `@family` — these are not used
  in lab packages
- **Do not** omit `@export` — every function in this package is exported
- **Do not** silently drop unverifiable content when updating existing docs —
  flag it to the developer instead
- **Do not** include functions in `@seealso` that are only indirectly related
  (connected via an intermediate function) — use `@details` for pipeline
  narrative instead
- **Do not** copy import tags from another function without checking the
  function body — only document imports actually used

---

## Open Questions

### Shared parameters across functions

`@inherit` is not used in lab packages. Where the same parameter appears
across multiple functions, the current approach is undecided.

> ⚠️ Open question for the development team: should shared `@param` text be
> copy-pasted explicitly across all functions, or is there a preferred
> internal reference approach? Until resolved, duplicate the `@param` text
> verbatim and keep it consistent manually.