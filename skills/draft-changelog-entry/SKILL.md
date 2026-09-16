---
name: draft-changelog-entry
description: >
  Draft a CHANGELOG.md entry for the current git feature branch by
  synthesizing the branch's commit log and diff relative to develop.
  Use when the user asks to write, draft, or generate CHANGELOG entries
  for a feature branch or upcoming release.
allowed-tools: Bash, Read, Edit
license: MIT
metadata:
  authors:
    - name: Althea Marks
      orcid: 0000-0002-9370-9128
      url: https://github.com/theamarks/
  version: 1.2
  last_updated: 2026-09-16
---

# Draft Changelog Entry

Distill the work on a git branch into a structured
[Keep a Changelog 1.0.0](https://keepachangelog.com/en/1.0.0/) entry,
ready to insert into `CHANGELOG.md`.

## Overview

This skill operates in two modes depending on where in the gitflow
workflow the user is:

| Mode | Triggered when | What it does |
|---|---|---|
| **Feature** | On a feature branch, before merging to `develop` | Drafts entries under a labeled subheading inside `[Unreleased]` |
| **Release** | On `develop`, before merging to `main` | Collapses all branch subheadings into a flat versioned block |

Ask the user which mode they want before proceeding:

> "Are you documenting changes on a **feature branch** (before merging
> to develop), or preparing a **release** (collapsing unreleased entries
> into a version)?"

The two workflows are described separately below.

> **Changelogs are for humans, not machines.** Do not dump commit log
> output into the changelog. A commit documents a step in source code
> evolution; a changelog entry communicates the noteworthy difference to
> end users across potentially many commits. These are different things
> and require different writing.

> **Gitflow + rebase note**: Commit SHAs are intentionally omitted because
> rebasing feature branches onto `develop` rewrites them. Issue and PR
> numbers extracted from commit messages (`#123`) are stable and should be
> preserved. Dates are taken from commit timestamps and are approximate.

## Guiding Principles

Follow these principles when drafting and inserting entries, as specified
by [Keep a Changelog 1.0.0](https://keepachangelog.com/en/1.0.0/):

- Changelogs are **for humans**, not machines.
- There should be an entry for every single version.
- The same types of changes should be **grouped** under their category heading.
- Versions and sections should be **linkable** — maintain version
  comparison footer links at the bottom of `CHANGELOG.md`.
- The **latest version comes first** (reverse chronological order).
- The **release date** of each version is displayed in ISO 8601 format
  (`YYYY-MM-DD`).
- **List deprecations, removals, and breaking changes explicitly** — these
  are the most important entries for users upgrading between versions.

## Change Categories

Map every meaningful change to exactly one Keep a Changelog category.

| Category     | Keep a Changelog 1.0.0 definition                      |
|--------------|--------------------------------------------------------|
| `Added`      | New features                                           |
| `Changed`    | Changes in existing functionality                      |
| `Deprecated` | Soon-to-be removed features                            |
| `Removed`    | Now removed features                                   |
| `Fixed`      | Any bug fixes                                          |
| `Security`   | In case of vulnerabilities                             |

**Omit categories with no changes.** Do not manufacture entries to fill
empty sections. **Deprecated, Removed, and breaking Changes entries are
the most important** — users need these to safely upgrade. Never omit or
minimize them.

---

## Feature Branch Workflow

**Goal**: Draft changelog entries for the current feature branch and
insert them as a labeled subheading inside `## [Unreleased]` in
`CHANGELOG.md`. This keeps changes organized by branch as they accumulate
on `develop`, while keeping the content fresh in the developer's mind
before the merge.

### Step F1 — Identify the branch name

```bash
git branch --show-current
```

Use the branch name exactly as returned (e.g. `feature/v2-long-support`).
This becomes the subheading label in `CHANGELOG.md`.

### Step F2 — Gather commits and diff

```bash
# Commit subjects and bodies on this branch, not yet in develop
git log develop..HEAD --format="%s%n%b" --no-merges

# Approximate date range of the work
git log develop..HEAD --format="%ad" --date=short --no-merges | sort

# Summary of files changed
git diff develop...HEAD --stat

# Full diff (read selectively for large branches)
git diff develop...HEAD
```

From the commit log, extract:
- **Short descriptions** for change summaries — synthesize, do not copy
  commit subjects verbatim
- **Issue/PR references** — any `#NNN`, `Closes #NNN`, `Fixes #NNN`,
  `Resolves #NNN` patterns found in subjects or bodies
- **Approximate date range** of the work

Do **not** record or cite individual commit SHAs in the changelog entry.

Read any changed source files that are ambiguous from the diff alone.

### Step F3 — Categorize changes

Using the [Change Categories](#change-categories) table above, assign
every meaningful change to a category. Group related changes within a
category together.

### Step F4 — Draft the subheading block

Write entries under a branch-name subheading using this structure:

```markdown
### feature/branch-name

#### Added

-   **`function_name()`**: brief description of what was added
    (date range if known) (Resolves #NNN)

#### Changed

-   **`script_name.R` refactor**: description of what changed and why
    -   Sub-bullet for each distinct logical change within the same
        file or function

#### Fixed

-   **Brief label**: description of the bug and how it was resolved
    (Fixes #NNN)
```

#### Entry style rules

- Lead each top-level bullet with a **bold label** — a function name
  in backticks (`` `function_name()` ``), a file name, or a short
  descriptive phrase — followed by a colon and a brief description.
- Use sub-bullets (indented `-`) for distinct logical changes within the
  same function or file. Keep top-level bullets focused on one component.
- Include approximate **date or date range** in parentheses when it adds
  useful context (`(2026-08-01 to 2026-09-10)`). Omit for trivial or
  undated changes.
- Append issue/PR references at the end of the relevant bullet:
  `(Resolves #73)`, `(Fixes #80)`, `(Refs #45)`.
- Write in **past tense** ("Added", "Refactored", "Removed").
- Do **not** reference commit SHAs anywhere in the entry.
- Aim for the level of detail visible in the project's existing
  `CHANGELOG.md` — enough for a teammate to understand what changed
  without reading the diff.
- **Name the capability, not the implementation.** Sub-bullets should
  state what was added or changed in plain terms; they should not
  enumerate specific parameter values, reproduce conditional logic, or
  list individual cases handled. Write "Input validation added" rather
  than "Aborts with an error if `x` is not character, `y` is not
  numeric, or `z` is invalid."
- **Top-level function entries** should describe the function's purpose
  and key design approach. Omit parenthetical examples of specific
  parameter values or enumerated cases unless they represent a breaking
  change a caller needs to act on.

### Step F5 — Present the draft

Output the complete drafted block as a fenced markdown code block so the
user can review it clearly. Then ask:

> "Does this look accurate? Should anything be added, reworded, or
> removed before I insert it into `CHANGELOG.md`?"

Incorporate any requested edits before proceeding.

### Step F6 — Insert into CHANGELOG.md

After the user confirms the draft, read `CHANGELOG.md` to locate the
`## [Unreleased]` section.

- **If `[Unreleased]` exists**: append the new subheading block after
  any existing branch subheadings already present under it.
- **If `[Unreleased]` does not exist**: create it immediately after the
  preamble and insert the subheading block beneath it.

The result should look like:

```markdown
## [Unreleased]

### feature/earlier-branch

#### Added
- ...

### feature/branch-name   ← newly inserted

#### Changed
- ...

## [1.1.0] – 2025-08-13
...
```

Use the `Edit` tool to make this insertion precisely — do not rewrite
existing entries or touch the versioned release blocks below.

---

## Release Workflow

**Goal**: Collapse all feature branch subheadings accumulated under
`[Unreleased]` into a flat, standard Keep a Changelog versioned block,
and prepare `CHANGELOG.md` for the next development cycle.

### Step R1 — Read the current `[Unreleased]` section

Read `CHANGELOG.md` in full. Locate `## [Unreleased]` and identify all
`### feature/...` subheadings and their entries beneath it.

If `[Unreleased]` is empty or missing, tell the user and ask how they
would like to proceed before continuing.

### Step R2 — Collect version information

Ask the user for:

1. **Version number** — must follow
   [Semantic Versioning](https://semver.org/spec/v2.0.0.html)
   (`MAJOR.MINOR.PATCH`). If the user is unsure, summarize the scope of
   the accumulated changes and suggest a version:
   - New features present → MINOR bump
   - Breaking changes or removals present → MAJOR bump
   - Bug fixes only → PATCH bump
2. **Release date** in `YYYY-MM-DD` format (default: today's date).

### Step R3 — Merge and flatten the entries

Combine all entries from across the branch subheadings into a single set
of flat standard categories:

- Walk through each `### feature/...` subheading in order.
- Collect all bullets under each `#### Category` heading.
- Merge them into unified `### Category` sections for the new versioned
  block.
- If the same change appears in more than one branch subheading
  (unlikely but possible), consolidate into a single entry.
- Preserve issue/PR references and date context from the original entries.
- **Remove all `### feature/...` subheadings and `####` category
  headings** — they are scaffolding only and should not appear in the
  final release block.

### Step R4 — Draft the versioned block

Write the flattened entry in standard Keep a Changelog format:

```markdown
## [VERSION] – YYYY-MM-DD

### Added

-   ...

### Changed

-   ...

### Fixed

-   ...
```

Omit any categories with no entries.

### Step R5 — Present the draft

Output the complete versioned block as a fenced markdown code block. Ask:

> "Does this look accurate? Should anything be consolidated, reworded,
> or removed before I update `CHANGELOG.md`?"

Incorporate any requested edits before proceeding.

### Step R6 — Update CHANGELOG.md

After the user confirms, make the following edits using the `Edit` tool:

**6a — Replace `[Unreleased]` content with the versioned block**

Replace the entire `## [Unreleased]` section (heading + all branch
subheadings beneath it) with:
1. A fresh, empty `## [Unreleased]` section (for the next cycle).
2. The new versioned block immediately below it.

```markdown
## [Unreleased]

## [NEW VERSION] – YYYY-MM-DD

### Added
...

## [PREVIOUS VERSION] – YYYY-MM-DD
...
```

**6b — Update version comparison footer links**

Check the bottom of `CHANGELOG.md` for a footer link block such as:

```markdown
[1.1.0]: https://github.com/ORG/REPO/compare/v1.0.0...v1.1.0
```

If a footer block is present, add a new line for the new version and
update the `[Unreleased]` link to point from the new version tag to `HEAD`:

```markdown
[Unreleased]: https://github.com/ORG/REPO/compare/vNEW...HEAD
[NEW]: https://github.com/ORG/REPO/compare/vPREV...vNEW
```

If no footer block exists, ask the user whether they would like to add
one and, if so, what the GitHub repository URL is.

---

## What Belongs in the Changelog

The intended readers are **maintainers and developers** tracking
functional differences between releases. Default to including changes
that affect what the software does or how it is used; default to
excluding build toolchain and housekeeping changes.

### Include by default

| Type | Examples |
|---|---|
| New, changed, or removed functions or arguments | New `dev_mode` parameter, removed `build_artis_data.R` |
| Bug fixes | Incorrect export volume calculation, solver producing NAs |
| Breaking changes to behavior or output format | Output now `.qs2` instead of `.csv`, column renamed |
| Deprecations | Function still present but scheduled for removal |
| Security fixes | Vulnerability patches |
| Open-source documentation files | `LICENSE`, `CITATION.cff`, `CONTRIBUTING.md`, `inst/CITATION` |

### Exclude by default

| Type | Examples |
|---|---|
| Build and package config | `.Rbuildignore`, `.gitignore`, `DESCRIPTION` version bump only |
| Dependency lock files | `renv.lock` updates (unless a dependency change caused a behavior change) |
| CI/CD and infrastructure | GitHub Actions workflows, Dockerfile |
| Code style and formatting | Whitespace passes, linting fixes, comment rewrites |
| Roxygen2 formatting with no content change | Rewrapped `@param` lines, reordered tags |
| Test file additions | Unless the test reveals and documents a bug fix |
| Auto-generated files | `man/` files regenerated by `devtools::document()` |

If a changed file is ambiguous, ask: *"Would a maintainer upgrading
between versions need to know about this?"* If yes, include it.

## Notes

- If the branch contains commits that are clearly housekeeping (typo
  fixes, whitespace, dependency bumps) with no user-visible impact,
  silently omit them — do not create a catch-all bullet for them.
- If no `develop` branch exists locally, fall back to
  `git diff main...HEAD` and note the substitution to the user.
- Do not invent issue numbers. Only include `#NNN` references found
  verbatim in the commit log.
- **Yanked releases**: if a version was pulled due to a serious bug or
  security issue, mark it `## [X.Y.Z] – YYYY-MM-DD [YANKED]`. The
  `[YANKED]` tag is intentionally loud and should remain visible in the
  file permanently.
