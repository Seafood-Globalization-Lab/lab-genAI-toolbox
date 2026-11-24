---
name: git-commit-summary
description: Write concise, well-structured git commit messages following the Conventional Commits specification, or generate an AI attribution block for an existing commit message. Use when the user asks to write, draft, summarize, or format a git commit message, or when they ask for just the AI attribution footer.
allowed-tools: Read, Bash
license: MIT
metadata:
  authors:
    - name: Althea Marks
      orcid: 0000-0002-9370-9128
      url: https://github.com/theamarks/
  version: 1.3
  last_updated: 2026-11-23
---

# Git Commit Summary

Write concise, well-structured git commit messages following the
[Conventional Commits](https://www.conventionalcommits.org/) specification.

## Overview

This skill supports two modes:

| Mode | Use when... |
|------|-------------|
| **Full commit summary** | Writing a complete commit message from scratch — subject, body, and AI attribution footer |
| **AI attribution only** | The commit message is already written; just append the AI attribution block to the footer |

Determine which mode to use from the user's request. If it is ambiguous,
default to **Full commit summary**.

For a full commit summary, read any changed files or diff output to understand
the scope and intent of the changes. Focus on **what** changed and **why**,
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
- AI attribution block, placed last — see [AI Attribution Block](#ai-attribution-block) below

## AI Attribution Block

Place at the end of the commit footer after a blank line.

```
-- AI-assisted or generated content --
model: <model-api-tag> (<provider>) <model_identifier_url>
access: <interface> <interface_version> via <platform> <platform_version>
Tools: <toolbox-github-tree-url>
```

Omit the `Tools` line if no skill or prompt library was used.

### Fields

| Field | Description | Example |
|-------|-------------|---------|
| `model` | Provider API model tag — self-report best-known identifier, flag for user verification | `claude-sonnet-4-6` |
| `provider` | Company behind the model | `Anthropic` |
| `model_identifier_url` | Stable model card URL | `https://www.anthropic.com/claude/sonnet` |
| `interface` | Software used to interact with the model | `Posit Assistant` |
| `interface_version` | Version of that interface — read from session context if available | `2026.11.06` |
| `platform` | IDE or environment — read from session context | `Positron` |
| `platform_version` | Version of the IDE or environment — read from session context | `2026.11.1` |
| `toolbox` | GitHub tree URL pinned to the toolbox commit hash | `https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox/tree/40860d4` |

### Gathering the fields

1. **Model API tag** — self-report your best-known API-style identifier
   (lowercase, hyphenated, e.g. `claude-sonnet-4-6`). Do not use
   marketing names. Because models cannot guarantee their own exact
   identifier, always append a verification note to the output:
   > ⚠️ Verify model tag before committing — check Posit Assistant
   > settings to confirm the exact API identifier.
2. **Provider and model URL** — derive from the self-reported model tag.
   For Anthropic models use `https://www.anthropic.com/claude/<model-family>`.
3. **Platform** — read the host name and version from the session context
   (the `<host>` tag injected at the start of the conversation). Do not
   ask the user.
4. **Interface version** — read from session context if present. If not
   available in context, omit the version number and note it:
   `Posit Assistant (version unknown)`.
5. **Toolbox commit hash** — run:
   ```bash
   git -C <path-to-.lab-genAI-toolbox> log -1 --format="%h"
   ```
   Construct the URL as
   `https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox/tree/<hash>`.
   Omit the `Tools` line if no toolbox was used.

---

## Workflow

### Mode 1 — Full commit summary

#### Step 1 — Understand the changes

Read changed files, diff output, or the conversation context. Identify:

- The primary change type (`feat`, `fix`, `refactor`, etc.)
- Which files or components were affected
- The reason for the change (bug, design decision, new requirement)

#### Step 2 — Write the subject line

Draft a subject line that passes this test: *"If applied, this commit
will [subject line]."* Keep it under 50 characters. Prefer specificity
over brevity when the two conflict within the limit.

#### Step 3 — Write the body

Summarize the key changes in 2–5 bullet points or a short paragraph.
Omit implementation details that are visible in the diff. Include
context that is NOT visible in the diff (e.g. why a design decision was
made, which assumption was violated, what the alternative was).

#### Step 4 — Add footer lines

Add any relevant issue references or attribution lines. Generate the AI
attribution block following the [AI Attribution Block](#ai-attribution-block)
section above and place it at the end of the footer.

---

### Mode 2 — AI attribution only

Use this mode when the user already has a commit message and wants only
the AI attribution block to append to its footer.

#### Step 1 — Generate the attribution block

Gather the required fields and format the block following the
[AI Attribution Block](#ai-attribution-block) section above.
Output only the attribution block — do not rewrite or modify the
existing commit message.

#### Step 2 — Provide placement guidance

Remind the user to append the block at the end of the commit footer,
separated from any preceding lines by a blank line.

## Examples

### Mode 1 — Full commit summary

```
refactor: replace synonym resolution loop with vectorized lookup

- Add resolve_synonyms(): lapply + try() per name, returns explicit
  lookup table with status column
- Replace side-effect for loop with vectorized join/coalesce pattern
- Add $synonym_resolution to function return list for audit trail
- query_synonyms() and all other callers unchanged

-- AI-assisted or generated content --
model: claude-sonnet-4-6 (Anthropic) https://www.anthropic.com/claude/sonnet
access: Posit Assistant 1.4.1 via Positron 2026.09.0
Tools: https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox/tree/ef0496a
```

### Mode 2 — AI attribution only

Output only the attribution block for the user to append:

```
-- AI-assisted or generated content --
model: claude-sonnet-4-6 (Anthropic) https://www.anthropic.com/claude/sonnet
access: Posit Assistant 1.4.1 via Positron 2026.09.0
Tools: https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox/tree/ef0496a
```

## Notes

- If the change spans multiple unrelated concerns, encourage the user to
  split into separate commits rather than writing an omnibus message.
- A body is optional for truly trivial changes (e.g. fix a typo,
  update a version number).
- Do not add a body that merely restates the subject line.
