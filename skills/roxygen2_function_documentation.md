# ARTIS Roxygen2 Documentation Skill

<!-- Suggested filename: roxygen2-function-documentation.md -->

## Purpose

This skill guides an AI assistant in writing and updating roxygen2 documentation
for functions in the `artis` R package. All functions are exported and require
full documentation. The goal is consistent, accurate, developer-facing
documentation that reflects both official roxygen2 conventions and ARTIS
package style.

---

## Package Context

**ARTIS (Aquatic Resource Trade in Species)** is a global model and database
that estimates bilateral seafood trade flows at the species level from 1994 to
2023. It integrates production data, detailed trade statistics, and biological
characteristics of seafood products to reconstruct trade networks and infer
domestic consumption. The model accounts for re-exports, product
transformations (e.g. fishmeal production), and multiple trade classification
systems (HS codes).

The package provides tools to:
- Clean and standardize seafood trade and production data
- Allocate trade flows and compute consumption metrics
- Match taxa names against FishBase and SeaLifeBase
- Resolve synonyms, HS product codes, and conversion factors

Functions are organized into a pipeline. Understanding where a function sits in
that pipeline — what calls it, what it calls, what it reads from disk and what
it writes — is essential for writing accurate `@details` and `@seealso`
sections.

### DESCRIPTION configuration
```
Roxygen: list(markdown = TRUE)
RoxygenNote: 7.3.3
```
Markdown is enabled package-wide. Do not add `@md` tags to individual blocks.

---

## Workflow: Writing Documentation from Scratch

### Step 1 — Read the full function body before writing anything

Look for:
- What the function does at a high level (for `@description`)
- Input types and roles (for `@param`)
- What is returned — shape, columns, rows, downstream usage (for `@return`)
- Integrity checks, warnings, or validation logic (for `@details` subsections)
- Manual correction blocks with snapshot/server scoping (for `@details`)
- Any dropped or filtered rows with a specific rationale (for `@note`)
- Calls to other package functions — both callers and callees (for `@seealso`)
- Any external reference data or documentation URLs (for `@seealso`)

### Step 2 — Identify the function's place in the pipeline

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
#' Clean FishBase / SeaLifeBase synonym corrections table
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
#' Transforms a raw FishBase or SeaLifeBase synonyms table into a cleaned
#' synonym corrections table that maps non-accepted synonym name strings to
#' their accepted taxonomic names via `spec_code`.
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
#' Called inside [collect_fb_slb_data()] for both FishBase and SeaLifeBase.
#' The output is written to `fb_synonyms_clean.csv` / `slb_synonyms_clean.csv`
#' and later read by multiple downstream functions that pass it to
#' [query_synonyms()] for synonym resolution:
#'
#' * [match_prod_taxa_to_fbslb()] — resolves unmatched production scientific
#'   names during taxa matching
#' * [clean_hs()] — resolves HS product code species names to accepted names
#' * [compile_cf()] — resolves unmatched species names during conversion factor
#'   compilation
#'
#' ## Data integrity checks
#'
#' Runs three data integrity checks and emits `cli` warnings if violations are
#' found. All three checks test the assumption that a single synonym name string
#' maps to a single accepted taxonomic name:
#'
#' * **`spec_code` integrity check** — groups by `spec_code` and detects cases
#'   where a single FishBase/SeaLifeBase species ID resolves to more than one
#'   `accepted_name`. Indicates an upstream database error.
#' * **Synonym string ambiguity check** — groups by `synonym` name string and
#'   detects cases where the same synonym text points to different `spec_code`s.
#' * **`accepted_status` integrity check** — groups by `spec_code` and detects
#'   cases where both `"accepted name"` and `"provisionally accepted name"`
#'   coexist for the same species ID.
#'
#' ## Manual corrections
#'
#' Manual corrections (snapshot- and server-specific) are applied after table
#' assembly and before the assumption checks. See the
#' *Apply manual corrections* section of the function body.
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
  backtick-quoted strings: `"fishbase"` or `"sealifebase"`
- For arguments with default values that are non-obvious, document the default
- For arguments that accept `NULL`, note it explicitly in the type and document
  the behaviour: e.g. `Character or \`NULL\`. If \`NULL\`, [describe fallback
  behaviour]. Default: \`NULL\`.`

```r
#' @param the_df Data frame. Raw synonyms table as returned by
#'   `rfishbase::fb_tbl("synonyms", ...)`.
#' @param the_snapshot Character. The `rfishbase` snapshot version (e.g.
#'   `"25.04"`). Used to scope manual corrections to the correct snapshot and
#'   prevent corrections from propagating silently into future snapshot data.
#' @param the_server Character. One of `"fishbase"` or `"sealifebase"`. Used
#'   alongside `the_snapshot` to scope manual corrections to the correct
#'   database.
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
#' @return A data frame with one row per synonym–accepted name pair.
```

Richer case (data frame with notable structure):
```r
#' @return
#' A data frame with one row per production record. The output has the
#' following properties:
#'
#' * Rows represent individual species–country–year production events.
#' * Columns include `species`, `country_iso3c`, `year`, `live_weight_t`.
```

Side-effect case:
```r
#' @return Called for its side effects. Returns `invisible(NULL)`. Writes
#'   `fb_synonyms_clean.csv` to the path specified by `output_path`.
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
#' @note `spec_code` values of `0` are dropped — these are
#'   FishBase/SeaLifeBase backlog entries awaiting validation and have not yet
#'   been assigned a valid species ID.
```

---

### `@seealso`

- Always written as an unordered bullet list
- Include every function that calls this one and every function this one calls,
  with a brief `—` dash description of the relationship
- Include external documentation URLs as a final bullet where relevant
- Use `[function()]` link syntax throughout

```r
#' @seealso
#' * [collect_fb_slb_data()] — calls this function and writes the output to
#'   disk
#' * [query_synonyms()] — performs the synonym lookup using the output CSV
#' * [match_prod_taxa_to_fbslb()] — reads the output CSV and passes it to
#'   [query_synonyms()] for synonym resolution
#'   [query_synonyms()] for synonym resolution
#' * [clean_hs()] — reads the output CSV and passes it to
#'   [query_synonyms()] for synonym resolution
#' * [compile_cf()] — reads the output CSV and passes it to
#'   [query_synonyms()] for synonym resolution
#' * FishBase SYNONYMS table documentation:
#'   <https://www.fishbase.se/manual/english/FishBaseThe_SYNONYMS_Table.htm>
```

---

### Import tags

- Use `@import pkg` for packages used pervasively in the function body
- Use `@importFrom pkg fun` for single function imports
- Always include `@import dplyr` and `@import cli` if used
- This package uses the **magrittr pipe** (`%>%`). Always include
  `@importFrom magrittr %>%` when the pipe is used in the function body

```r
#' @import dplyr
#' @import cli
#' @importFrom magrittr %>%
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
#' Clean FishBase / SeaLifeBase synonym corrections table
#'
#' Transforms a raw FishBase or SeaLifeBase synonyms table into a cleaned
#' synonym corrections table that maps non-accepted synonym name strings to
#' their accepted taxonomic names via `spec_code`.
#'
#' @details
#' Called inside [collect_fb_slb_data()] for both FishBase and SeaLifeBase.
#' The output is written to `fb_synonyms_clean.csv` / `slb_synonyms_clean.csv`
#' and later read by multiple downstream functions that pass it to
#' [query_synonyms()] for synonym resolution:
#'
#' * [match_prod_taxa_to_fbslb()] — resolves unmatched production scientific
#'   names during taxa matching
#' * [clean_hs()] — resolves HS product code species names to accepted names
#' * [compile_cf()] — resolves unmatched species names during conversion factor
#'   compilation
#'
#' ## Data integrity checks
#'
#' Runs three data integrity checks and emits `cli` warnings if violations are
#' found. Both checks test the assumption that a single synonym name string
#' maps to a single accepted taxonomic name, but via different pathways:
#'
#' * **`spec_code` integrity check** — groups by `spec_code` and detects cases
#'   where a single FishBase/SeaLifeBase species ID resolves to more than one
#'   `accepted_name`. Indicates an upstream database error — the same species
#'   ID was assigned conflicting accepted names in FishBase/SeaLifeBase.
#' * **Synonym string ambiguity check** — groups by `synonym` name string and
#'   detects cases where the same synonym text appears as multiple distinct
#'   FishBase/SeaLifeBase entries (different `syn_code`s) pointing to different
#'   `spec_code`s. The synonym string alone is insufficient to uniquely
#'   identify a species, causing a one-to-many join error in [query_synonyms()].
#' * **`accepted_status` integrity check** — groups by `spec_code` and detects
#'   cases where both `"accepted name"` and `"provisionally accepted name"`
#'   coexist for the same species ID. Indicates FishBase/SeaLifeBase assigned
#'   conflicting acceptance statuses to the same species ID. No working
#'   protocol exists for this scenario; requires developer investigation via
#'   WoRMS.
#'
#' ## Manual corrections
#'
#' Manual corrections (snapshot- and server-specific) are applied after table
#' assembly and before the assumption checks. See the
#' *Apply manual corrections* section of the function body.
#'
#' @param the_df Data frame. Raw synonyms table as returned by
#'   `rfishbase::fb_tbl("synonyms", ...)`.
#' @param the_snapshot Character. The `rfishbase` snapshot version (e.g.
#'   `"25.04"`). Used to scope manual corrections to the correct snapshot and
#'   prevent corrections from propagating silently into future snapshot data.
#' @param the_server Character. One of `"fishbase"` or `"sealifebase"`. Used
#'   alongside `the_snapshot` to scope manual corrections to the correct
#'   database.
#'
#' @return A data frame with one row per synonym–accepted name pair.
#'
#' @note `spec_code` values of `0` are dropped — these are
#'   FishBase/SeaLifeBase backlog entries awaiting validation and have not yet
#'   been assigned a valid species ID.
#'
#' @seealso
#' * [collect_fb_slb_data()] — calls this function and writes the output to
#'   disk
#' * [query_synonyms()] — performs the synonym lookup using the output CSV
#' * [match_prod_taxa_to_fbslb()] — reads the output CSV and passes it to
#'   [query_synonyms()] for synonym resolution
#' * [clean_hs()] — reads the output CSV and passes it to
#'   [query_synonyms()] for synonym resolution
#' * [compile_cf()] — reads the output CSV and passes it to
#'   [query_synonyms()] for synonym resolution
#' * FishBase SYNONYMS table documentation:
#'   <https://www.fishbase.se/manual/english/FishBaseThe_SYNONYMS_Table.htm>
#'
#' @import dplyr
#' @import cli
#' @importFrom magrittr %>%
#' @export
clean_fb_slb_synonyms <- function(the_df, the_snapshot, the_server) {
  # function body
}
```

---

## Common Mistakes to Avoid

- **Do not** put extended pipeline context or logic inside `@description` —
  that belongs in `@details`
- **Do not** add `##` subsections in `@details` unless there are genuinely
  multiple distinct conceptual sections
- **Do not** add `@md` tags to individual blocks — markdown is enabled
  package-wide via `Roxygen: list(markdown = TRUE)` in DESCRIPTION
- **Do not** use `@examples`, `@inherit`, or `@family` — these are not used
  in this package
- **Do not** omit `@export` — every function in this package is exported
- **Do not** silently drop unverifiable content when updating existing docs —
  flag it to the developer instead

---

## Open Questions

### Shared parameters across functions

`@inherit` is not used in this package. Where the same parameter (e.g.
`the_snapshot`, `the_server`) appears across multiple functions, the current
approach is undecided.

> ⚠️ Open question for the development team: should shared `@param` text be
> copy-pasted explicitly across all functions, or is there a preferred
> internal reference approach? Until resolved, duplicate the `@param` text
> verbatim and keep it consistent manually.