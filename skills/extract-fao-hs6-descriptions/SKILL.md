---
name: extract-fao-hs6-descriptions
description: >
  Write the extract_fao_hs6_descriptions() R function for the artis package.
  This function extracts the "Full description of fisheries and aquaculture products"
  table from the FAO & WCO HS Codes handbook PDF into a tidy data frame with columns
  hs_version, hs6, and description_orig. Use when adding or updating the FAO HS
  reference table in the ARTIS pipeline, or when a new edition of the FAO handbook
  is released.
allowed-tools: Read Bash Write Edit
license: MIT
metadata:
  authors:
    - name: Althea Marks
      orcid: 0000-0002-9370-9128
      url: https://github.com/theamarks/
  version: 1.0
  last_updated: 2026-10-07
---

# Extract FAO HS6 Descriptions

Write the `extract_fao_hs6_descriptions()` R function for the `artis` package.
This function parses the FAO & WCO *HS Codes for Fisheries and Aquaculture Products*
handbook PDF and returns a tidy table of 6-digit HS codes with their full
product descriptions, for use in downstream taxa and product matching.

---

## When to use this skill

Apply this skill when:

- Ingesting a new edition of the FAO fisheries HS codes handbook
- Re-implementing or updating `extract_fao_hs6_descriptions()` in the `artis` package
- A new HS nomenclature year is released (new handbook PDF)

---

## Source document

The function targets the FAO & WCO fisheries HS handbook, published jointly
by the Food and Agriculture Organization of the United Nations (FAO) and the
World Customs Organization (WCO).

| Edition | Nomenclature | Published | DOI | Direct PDF |
|---|---|---|---|---|
| 2nd (current) | HS 2022 | 2023 | [10.4060/cc6347en](https://doi.org/10.4060/cc6347en) | https://www.fao.org/3/cc6347en/cc6347en.pdf |
| 1st | HS 2017 | 2021 | [10.4060/cb3813en](https://doi.org/10.4060/cb3813en) | via DOI landing page |

The function accepts either a local file path or a direct PDF URL.

---

## Before writing the function

### Step 1 — Read package context files

Read the following before writing anything:

```
README.md
DESCRIPTION
```

Confirm from `DESCRIPTION`:
- `Roxygen: list(markdown = TRUE)` is set — do not add `@md` tags to individual blocks
- `pdftools` is listed in `Imports:` — if absent, add it

### Step 2 — Read lab skill references

This function requires two other skills in this toolbox. Read both before writing:

- `skills/roxygen2-function-documentation/SKILL.md` — for roxygen2 header format, tag order, and footer
- `skills/cli-messaging/SKILL.md` — for all user-facing message patterns

### Step 3 — Get the toolbox commit hash

Run:
```bash
git -C <path-to-.lab-genAI-toolbox> log -1 --format="%H %h"
```

Use the full SHA for the roxygen2 footer URL and the short SHA as display text.

---

## PDF structure

The handbook is a typeset, two-column PDF (Adobe InDesign origin).
It is divided into three sections:

| Section | Content | Pages (HS2022 edition) |
|---|---|---|
| I | Species listed alphabetically with treatment × HS code lookup tables | 1–99 |
| II | **Full description of fisheries and aquaculture products** — target section | 100–111 |
| III | Species photographs and biological profiles | 112–end |

### Section II layout

Each page in Section II:
- Opens with a **range header** of the form `XXXX.XX - XXXX.XX` (e.g. `0301.11 - 0302.41`)
- Contains a **two-column table** of HS code entries

Each entry follows this pattern:
```
0301.11   Freshwater ornamental fish, live
0301.91   Trout (Salmo trutta, Oncorhynchus mykiss, Oncorhynchus clarki, Oncorhynchus
          aguabonita, Oncorhynchus gilae, Oncorhynchus apache and Oncorhynchus
          chrysogaster), live; excluding ornamental
```

- The HS code is followed by 2+ spaces then the description on the same line
- Wrapped continuation lines are indented (no HS code at the start)
- Some HS codes have **multiple distinct descriptions** listed consecutively
  under the same code (see [Multiple descriptions](#multiple-descriptions) below)

---

## Parsing algorithm

### Finding Section II page range

Use `pdftools::pdf_text()` to extract all pages.

**Start:** The first page containing `"Full description of fisheries and aquaculture products"`.

**End:** Every Section II page carries the range header pattern. Scan forward
from the start page; the last page that matches this pattern is the end of Section II:

```r
range_header_pat <- "\\d{4}\\.\\d{2}\\s*-\\s*\\d{4}\\.\\d{2}"
```

> **Important:** Do NOT use `"Photo credits"` or `"Pictures, basic information"` to
> detect the Section II end. These phrases appear only at content pages 284+ (PDF page
> 294+) in the HS2022 edition, and using them will pull Section III species photo pages
> into the parse, inflating row counts to ~3,000.

### Column split detection

Each page is two-column. `pdftools::pdf_text()` returns both columns merged
into single lines. Detect the column boundary dynamically per page:

1. Find all lines containing **two** HS code patterns (`\d{4}\.\d{2}\s{2,}`)
2. Record the character start position of the second code on each such line
3. Use the **median** position as the column split for that page

```r
.detect_col_split_fao <- function(page_text) {
  lines <- stringr::str_split(page_text, "\n")[[1]]
  positions <- integer(0)
  for (line in lines) {
    m <- gregexpr("\\d{4}\\.\\d{2}\\s{2,}", line)[[1]]
    if (length(m) == 2 && m[1] != -1) positions <- c(positions, m[2])
  }
  if (length(positions) > 0) as.integer(median(positions)) else NA_integer_
}
```

Split each line at that position. Process the left and right halves as
**independent text streams**.

### Parsing a stream

Within each stream, lines are classified as:

- **New entry:** matches `^(\d{4}\.\d{2})\s{2,}(.+)$`
- **Continuation:** any other non-empty line — appended to the current description
- **Skip:** range header, "Full description" heading, bare page numbers

### Multiple descriptions

Some HS codes list more than one product description consecutively under the
same code. Each description should become its own data frame row with the
`hs6` code repeated.

A continuation line starts a **new description** (rather than continuing the
previous one) when:

1. It starts with an uppercase letter (not `(`, not lowercase)
2. **AND** the accumulated previous description does NOT end with a grammatical
   connector: `,`, `(`, or the words `and`, `or`, `of`, `for`, `to`, `from`,
   `excluding`, `including`, `whether`, `not`, `the`

```r
.is_new_desc_fao <- function(line, prev_desc) {
  if (is.null(prev_desc) || nchar(stringr::str_trim(line)) < 5) return(FALSE)
  starts_upper  <- stringr::str_detect(line, "^[A-Z]")
  ends_connector <- stringr::str_detect(
    prev_desc,
    paste0(
      ",\\s*$|\\(\\s*$",
      "|\\band\\s*$|\\bor\\s*$|\\bof\\s*$|\\bfor\\s*$|\\bto\\s*$",
      "|\\bfrom\\s*$|\\bexcluding\\s*$|\\bincluding\\s*$",
      "|\\bwhether\\s*$|\\bnot\\s*$|\\bthe\\s*$"
    )
  )
  starts_upper && !ends_connector
}
```

---

## Output specification

| Column | Type | Description |
|---|---|---|
| `hs_version` | character | HS nomenclature year, extracted from the PDF cover/citation text. Format: `"HS2022"`, `"HS2017"`. Extracted using `"Nomenclature\\s+(\\d{4})"` pattern. |
| `hs6` | character | 6-digit HS code with the period removed. `"0301.11"` → `"030111"` |
| `description_orig` | character | Full description text as extracted from the PDF, whitespace-squished. Case is preserved — [clean_hs()] lowercases descriptions downstream. |

Expected output for HS2022: **266 unique HS6 codes**, approximately **270–300 rows**
(more rows than codes due to multi-description entries).

---

## Package conventions

| Convention | Rule |
|---|---|
| Pipe | Use `%>%` (magrittr), not `\|>` |
| File I/O | `pdftools::pdf_text()` for PDF reading |
| Messaging | `cli` package only — never `message()`, `cat()`, `print()` |
| Internal functions | Prefix with `.` (not exported) |
| Sections | `# Section title ---` and `## Subsection title ---` |
| Inline comments | On the line above the code, same indentation |

---

## Step-by-step procedure

### Step 4 — Write the function file

Create `R/extract_fao_hs6_descriptions.R`.

The file structure is:

```
# Internal helpers -------------------------------------------------------
.detect_col_split_fao()
.is_new_desc_fao()
.parse_stream_fao()
.parse_section2_fao()

# Main function ----------------------------------------------------------
# [roxygen2 block]
extract_fao_hs6_descriptions <- function(pdf_path) { ... }
```

Load the complete reference implementation from
[`references/function-template.R`](references/function-template.R) — use it
directly. Do not rewrite the parsing helpers from memory; the heuristics are
precise and must match the reference exactly.

### Step 5 — Write the roxygen2 header

Follow the `roxygen2-function-documentation` skill. Key points for this function:

- **Title:** `Extract HS6 product descriptions from the FAO fisheries HS codes handbook`
- **`@details`:** pipeline context (called during HS code setup; output feeds `clean_hs()`), PDF parsing approach, multiple-description heuristic
- **`@param pdf_path`:** `Character. File path or URL to the FAO & WCO fisheries HS codes handbook PDF.`
- **`@return`:** data frame with `hs_version`, `hs6`, `description_orig`; one row per description; sorted by `hs6`
- **`@note`:** `hs6` has no period separator; `description_orig` preserves original case
- **`@seealso`:** `clean_hs()`, DOI links for HS2022 and HS2017 editions
- **Import tags:** `@import dplyr`, `@import cli`, `@import stringr`, `@importFrom pdftools pdf_text`
- **Footer:** include documentation footer with this skill's commit hash per the roxygen2 skill instructions

### Step 6 — Update DESCRIPTION

Add `pdftools` to the `Imports:` field in `DESCRIPTION` if not already present.
Insert it in alphabetical order.

### Step 7 — Verify output

After writing the function, run a quick smoke test:

```r
devtools::load_all()
pdf_tmp <- tempfile(fileext = ".pdf")
download.file("https://www.fao.org/3/cc6347en/cc6347en.pdf", pdf_tmp, mode = "wb", quiet = TRUE)
result <- extract_fao_hs6_descriptions(pdf_tmp)
```

Expected console output:
```
✔ 310 pages read from PDF
✔ HS version detected: "HS2022"
ℹ Section II located on PDF pages 110–121
✔ ~270–300 description rows extracted across 266 unique HS6 codes
```

Confirm:
- `nrow(result)` is between 266 and 310
- `dplyr::n_distinct(result$hs6)` is 266
- No `hs6` values contain a period (`"."`)
- `result$hs_version` is uniformly `"HS2022"`

---

## Edge cases and known limitations

- **Short descriptions:** A small number of entries (e.g. `"Caviar"`, `"Agar-agar"`) are
  genuinely short. The function flags descriptions under 10 characters as possible
  parsing artifacts — verify manually.
- **New PDF editions:** Page layout and column positions may shift. The dynamic
  column detection handles this automatically. Verify the `Section II located` message
  shows a plausible page range (typically 12 pages) before trusting row counts.
- **Single-column pages:** Some early or late Section II pages may have only one column.
  When no split position is detected, the full line is treated as the left column.
