---
name: repo-readme
description: Generate a structured README.md for a code repository. Use when asked to document a repo, write a README, or create repository documentation. Outputs a single markdown code chunk.
---

# Repo README Generator

Generate a standardized `README.md` for a code repository. Follow the instructions below to produce consistent, well-structured documentation.

## When to use this skill

Apply this skill when the user:
- Asks to write or generate a `README.md` for a code repository
- Asks to document a codebase or GitHub repo
- Provides a directory structure, scripts, or code files and wants documentation

## Step-by-step procedure

### 1. Survey the repository

Before writing, identify the following from the provided files or directory structure:
- **Purpose**: What does the repo do? What problem does it solve?
- **Audience**: Who is this for? What technical level and domain knowledge is assumed?
- **Scripts**: What scripts exist, in what order should they be run, and what does each do?
- **Inputs**: What input files or credentials are required (e.g., API keys, CSVs)?
- **Outputs**: What does the pipeline produce, and where?
- **Dependencies**: What packages or tools are required (`requirements.txt`, `package.json`, etc.)?
- **External services**: Does the repo connect to any APIs or external data sources?
- **Configuration options**: Are there optional filters, parameters, or flags the user can set?
- **Existing tags**: Are there any release tags, a CHANGELOG, or tag patterns visible in filenames or comments?

If any of these are unclear, ask the user before writing.

### 2. Follow the README structure

Always use the following section structure, renaming headers to reflect the domain and context of the specific repository where noted:

```
## Purpose

## Background
(rename to reflect the domain, e.g. "US Census Trade Background", "Salesforce Data Model Background")

### Intended Audience

### Versioning and Release Notes

## Repository Structure

## Instructions

### Setup

### Running the Pipeline

## Output

## Known Issues and Limitations
(optional — include only if relevant information is provided or issues are evident from the code)
```

### 3. Section guidance

### `## Purpose`
Write 1–2 sentences summarizing what the repo does and why it exists, followed by a bullet list of the pipeline's key actions.

### `## Background`
Provide domain or data source context a user needs to understand before working with the repo. Include a table of relevant datasets or endpoints if applicable, definitions of key concepts, codes, or variables referenced in the scripts, and links to official documentation where relevant.

### `### Intended Audience`
Describe who should use this repo, the assumed technical level (e.g. comfort with Python, command line, API credentials), and any domain knowledge required to interpret the data or outputs.

### `### Versioning and Release Notes`
Document how GitHub release tags are used to track discrete runs or data pulls. Follow this logic:
- If the user provides existing tags, a CHANGELOG, or release history — infer the tag naming convention from those and document the observed pattern with an example
- If tag patterns are visible in filenames, comments, or script output (e.g. date-stamped output directories) — use those as a signal for the likely tagging convention
- If no tag evidence exists — recommend a sensible default convention (e.g. `v<YYYYMMDD>-<descriptor>`)

Always include guidance on what metadata each tag's notes should capture (e.g. date of run, parameter settings, input files used, known data gaps).

### `## Repository Structure`
Provide a bulleted list describing what each key file and directory does. Do not repeat the directory tree (which is typically visible elsewhere) — focus on the purpose and role of each item.

### `### Setup`
Combine all prerequisites and configuration into one section. Include numbered steps for:
1. Cloning the repo
2. Installing dependencies
3. Storing credentials or API keys (note what is gitignored)
4. Preparing required input files
5. Any optional configuration (filters, parameters, flags)

Use fenced code blocks for all shell commands and code examples.

### `### Running the Pipeline`
List scripts in execution order. For each script provide:
- The command to run it
- A bullet list of what it does
- Any outputs it produces

### `## Output`
Describe what the pipeline produces, where output is stored, what file formats are used, and which directories are excluded from version control via `.gitignore`. Show the expected output directory tree using a code block, annotating key files inline with `#` comments.

### `## Known Issues and Limitations`
Optional section. Include only if the user provides relevant information or if issues, edge cases, API quirks, or known failure modes are evident from the code. Omit the section entirely if there is nothing to document.

### 4. Markdown formatting rules

Apply these formatting rules consistently throughout the README. They override general markdown habits.

- Use GitHub-flavored markdown syntax throughout
- Never use `---` horizontal rules as section breaks — use whitespace and heading hierarchy to separate sections instead
- Minimize bold text — avoid using `**bold**` for general emphasis or to label section content; reserve it only for genuinely critical warnings or required values that would be dangerous to miss
- Use backticks for all inline code values, file names, directory names, flag names, variable names, environment variables, and CLI commands (e.g. `requirements.txt`, `CTY_CODE`, `--verbose`, `output/`)
- Use GitHub callout syntax for notes, warnings, and tips where appropriate:

```
> [!NOTE]
> Use for helpful context that isn't critical

> [!TIP]
> Use for optional but useful suggestions

> [!WARNING]
> Use for things that could cause errors or data loss

> [!IMPORTANT]
> Use for required steps that are easy to miss
```

- Use callouts sparingly — no more than 2–3 per README, only where they add genuine value over plain prose

### 5. Output format

Wrap the entire README in a `~~~markdown` outer fence. Use ` ``` ` inner fences for all code blocks within the README. Do not output the README as plain text or as a rendered document — always use the fenced output format.

## Example output structure

~~~markdown
# Project Name

One-sentence description.

## Purpose
...

## [Domain] Background
...

### Intended Audience
...

### Versioning and Release Notes
...

## Repository Structure
- `script.py` — does X
- `helpers.py` — contains utility functions for Y
- `requirements.txt` — Python dependencies

## Instructions

### Setup

> [!IMPORTANT]
> You will need a Census API key before running any scripts. Store it in `census_api_key.txt` in the repo root — this file is gitignored and will not be committed.

```bash
git clone <repo-url>
pip install -r requirements.txt
```
...

### Running the Pipeline
```bash
python 01-script.py
```
...

## Output
```
output/
└── ...
```

## Known Issues and Limitations
...

## Edge cases

- If no `requirements.txt` or equivalent exists, note that dependencies are unknown and ask the user
- If scripts have no clear execution order, ask the user to confirm the intended sequence
- If the repo connects to an external API, always include a credential setup step and note gitignore behavior
- If the user provides only a directory tree with no file contents, write what you can and flag assumptions clearly
- If no tag evidence exists anywhere in the provided materials, recommend a default tagging convention and note that it is a suggestion