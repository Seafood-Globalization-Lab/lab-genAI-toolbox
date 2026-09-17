---
name: git-commit-summary
description: Write concise, well-structured git commit messages following the Conventional Commits specification. Use when the user asks to write, draft, summarize, or format a git commit message.
allowed-tools: Read, Bash
license: MIT
metadata:
  authors:
    - name: Althea Marks
      orcid: 0000-0002-9370-9128
      url: https://github.com/theamarks/
  version: 1.0
  last_updated: 2026-09-17
---

# Git Commit Summary

Write concise, well-structured git commit messages following the
[Conventional Commits](https://www.conventionalcommits.org/) specification.

## Overview

This skill guides writing git commit messages that are human-readable,
machine-parseable, and useful for generating changelogs. Before writing a
commit message, read any changed files or diff output to understand the
scope and intent of the changes. Focus on **what** changed and **why**,
not how.

## Commit Types

Choose the type that best describes the primary change:

| Type       | Use when...                                                |
|------------|------------------------------------------------------------|
| `feat`     | A new feature is added                                     |
| `fix`      | A bug is corrected                                         |
| `refactor` | Code is restructured without adding features or fixing bugs|
| `docs`     | Documentation only changes                                 |
| `test`     | Tests are added or updated                                 |
| `style`    | Formatting or whitespace changes (not CSS)                 |
| `perf`     | A performance improvement                                  |
| `chore`    | Build process, tool config, or dependency updates          |

## Format

```
<type>: <short description>

<body>

<footer>
```

### Subject line

- **Imperative mood**: "add", "fix", "remove" — not "added", "fixes", "removed"
- **≤ 50 characters**
- Sentence case, no trailing period
- Describes the change, not the implementation

### Body

- Separated from subject by a blank line
- Wrap lines at **72 characters**
- Explain **what** changed and **why** — not how
- Use a bullet list (`-`) when summarizing multiple distinct changes

### Footer

- Breaking changes: `BREAKING CHANGE: <description>`
- Issue or PR references: `Closes #123`, `Refs #456`
- AI attribution block, placed last — use the `give-ai-some-credit` skill
  for format and field guidance

## Workflow

### Step 1 — Understand the changes

Read changed files, diff output, or the conversation context. Identify:

- The primary change type (`feat`, `fix`, `refactor`, etc.)
- Which files or components were affected
- The reason for the change (bug, design decision, new requirement)

### Step 2 — Write the subject line

Draft a subject line that passes this test: *"If applied, this commit
will [subject line]."* Keep it under 50 characters. Prefer specificity
over brevity when the two conflict within the limit.

### Step 3 — Write the body

Summarize the key changes in 2–5 bullet points or a short paragraph.
Omit implementation details that are visible in the diff. Include
context that is NOT visible in the diff (e.g. why a design decision was
made, which assumption was violated, what the alternative was).

### Step 4 — Add footer lines

Add any relevant issue references or attribution lines. If an AI
assistant contributed, use the `give-ai-some-credit` skill to generate
the attribution block and place it at the end of the footer.

## Example

```
refactor: replace synonym resolution loop with vectorized lookup

- Add resolve_synonyms(): lapply + try() per name, returns explicit
  lookup table with status column
- Replace side-effect for loop with vectorized join/coalesce pattern
- Add $synonym_resolution to function return list for audit trail
- query_synonyms() and all other callers unchanged

-- AI-assisted or generated content --
model: claude-sonnet-4-6 (Anthropic) https://www.anthropic.com/claude/sonnet
access: Posit Assistant 1.4.1 in Positron 2026.09.0
Tools: https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox/tree/ef0496a
```

## Notes

- If the change spans multiple unrelated concerns, encourage the user to
  split into separate commits rather than writing an omnibus message.
- A body is optional for truly trivial changes (e.g. fix a typo,
  update a version number).
- Do not add a body that merely restates the subject line.
