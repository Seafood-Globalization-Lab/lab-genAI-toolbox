---
name: give-ai-some-credit
description: Create and format an AI attribution statement for code commits, work logs, file headers, readmes, reports, papers and other scientific research outputs. Use when users want to document, track, record, update or account for AI contributions to any project artifact to supports transparency, reproducibility, auditability of AI-assisted work.
allowed-tools: Bash, Read
license: MIT
metadata:
  author:
    - name: Althea Marks
      orcid: 0000-0002-9370-9128
      url: https://github.com/theamarks/
  repository: https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox
  version: 1.0
  last_updated: 2026-09-17
---

# GenAI Attribution

A skill for documenting and classifying meaningful generative AI contributions to any artifact produced in a standardized format.

At a high level, the process of giving generative AI attribution goes like this:

- identify the attribution fields (required field are a priority, then option fields if they apply)
- Review the work session to help evaluate what kinds of contributions the AI made and classify them into the CRediT taxonomy.
- Use attribution template to format and record the AI contributions
- Ask user to review and tweak any misclassifications, reclassify, and repeat until satisfied
- Provide the user with a code chunk to copy and paste into their artifact. 

Your job when using this skill is to assist the user evaluate, characterize, and record how they used generative AI in their workflow, and prompt them for their evaluation of your CRediT assignments. You may need to clarify with the user what "chunk" of work they want to evaluate for AI attribution, or worksession. For instance, the user could say "provide me with an overview of the work you did", You can help narrow down what they mean, is it the new feature on this branch? Is it this specific chat session? etc. 

IF the output attribution format is unclear from the users prompt, ask something along the lines of if they would the attribution recorded for a git commit message, a disclousure statement, or a detailed contribution table. 

Attribution in this skill is aligned with the
[FORCE11 Software Citation Principles](https://doi.org/10.7717/peerj-cs.86)
(Smith et al., 2016), treating the AI model as a citable software product
with a unique identifier, version, location, and contributor role. The
[CRediT contributor taxonomy](https://doi.org/10.3789/ansi.niso.z39.104-2022) is used to
document the nature of AI contributions.


## Attribution Fields

### Required

| Field              | FORCE11 Principle          | Description                                               | Example                                                  |
|--------------------|----------------------------|-----------------------------------------------------------|----------------------------------------------------------|
| `model`            | Specificity, Unique ID     | Provider API model tag — not the human-readable name. For Anthropic, this is the API model identifier. | `claude-sonnet-4-6`          |
| `provider`         | Credit and Attribution     | Company or organization that develops the model           | `Anthropic`                                              |
| `model_identifier` | Unique Identification      | Persistent, resolvable identifier for the specific model version. DOI preferred; stable model card URL acceptable. | `https://www.anthropic.com/claude/sonnet` |
| `model_location`   | Accessibility              | Where the model is accessed or distributed                | `https://huggingface.co/meta-llama/Meta-Llama-3-8B`     |
| `interface`        | Accessibility              | Software or product used to interact with the model, including its version | `Posit Assistant 1.4.1`               |
| `platform`         | Accessibility              | IDE or environment and its version                        | `Positron 2026.09.0`                                     |
| `credit_role`      | Credit and Attribution     | One or more CRediT contributor roles describing the AI's contribution. Assign all that apply — see CRediT Taxonomy and `CREDIT-ROLES-REFERENCE.md`. | `Software; Writing – original draft` |
| `credit_role_uri`  | Unique Identification      | Persistent URI for each assigned CRediT role              | `https://credit.niso.org/contributor-roles/software/`   |

### Optional

| Field       | Description                                             | Example                      |
|-------------|---------------------------------------------------------|------------------------------|
| `toolbox`   | Skill or prompt library name and version or commit hash | `github.com/Seafood-Globalization-Lab/lab-genAI-toolbox/commit/372c7be` |
| `task`      | Brief description of what the AI contributed            | `Code refactoring`           |
| `date`      | Date of the AI-assisted session (`YYYY-MM-DD`)          | `2026-06-23`                 |

## CRediT Taxonomy

Use the [NISO CRediT standard](https://credit.niso.org/) to describe the
nature of AI contributions. **Assign all roles that genuinely apply** —
per the standard, an individual contributor (including an AI) can hold
multiple roles, and the same role can be assigned to multiple contributors.

Use `CREDIT-ROLES-REFERENCE.md` (included with this skill) to self-assess
which roles apply based on what was done in the session. Do not assign
roles speculatively; only assign roles for work actually carried out.

The URI is used in human-facing attribution templates (resolvable, human-readable).
The ID is the NISO-standard UUID for machine-readable contexts such as JATS XML.
See <https://credit.niso.org/implementing-credit/> for full implementation guidance.

| Role                       | URI                                                                              | ID (UUID)                              |
|----------------------------|----------------------------------------------------------------------------------|----------------------------------------|
| Conceptualization          | <https://credit.niso.org/contributor-roles/conceptualization/>                   | `8b73531f-db56-4914-9502-4cc4d4d8ed73` |
| Data Curation              | <https://credit.niso.org/contributor-roles/data-curation/>                       | `f93e0f44-f2a4-4ea1-824a-4e0853b05c9d` |
| Formal Analysis            | <https://credit.niso.org/contributor-roles/formal-analysis/>                     | `95394cbd-4dc8-4735-b589-7e5f9e622b3f` |
| Funding Acquisition        | <https://credit.niso.org/contributor-roles/funding-acquisition/>                 | `34ff6d68-132f-4438-a1f4-fba61ccf364a` |
| Investigation              | <https://credit.niso.org/contributor-roles/investigation/>                       | `2451924d-425e-4778-9f4c-36c848ca70c2` |
| Methodology                | <https://credit.niso.org/contributor-roles/methodology/>                         | `f21e2be9-4e38-4ab7-8691-d6f72d5d5843` |
| Project Administration     | <https://credit.niso.org/contributor-roles/project-administration/>              | `a693fe76-ea33-49ad-9dcc-5e4f3ac5f938` |
| Resources                  | <https://credit.niso.org/contributor-roles/resources/>                           | `ebd781f0-bf79-492c-ac21-b31b9c3c990c` |
| Software                   | <https://credit.niso.org/contributor-roles/software/>                            | `f89c5233-01b0-4778-93e9-cc7d107aa2c8` |
| Supervision                | <https://credit.niso.org/contributor-roles/supervision/>                         | `0c8ca7d4-06ad-4527-9cea-a8801fcb8746` |
| Validation                 | <https://credit.niso.org/contributor-roles/validation/>                          | `4b1bf348-faf2-4fc4-bd66-4cd3a84b9d44` |
| Visualization              | <https://credit.niso.org/contributor-roles/visualization/>                       | `76b9d56a-e430-4e0a-84c9-59c11be343ae` |
| Writing – original draft   | <https://credit.niso.org/contributor-roles/writing-original-draft/>              | `43ebbd94-98b4-42f1-866b-c930cef228ca` |
| Writing – review & editing | <https://credit.niso.org/contributor-roles/writing-review-editing/>              | `d3aead86-f2a2-47f7-bb99-79de6421164d` |

## Attribution Templates

### Git commit footer

Place at the end of the commit body after a blank line. May be also used for READMEs or other technical documentation. 

```
-- AI-assisted or generated content --
model: <model-api-tag> (<provider>) <model_identifier-or-doi>
access: <interface> <interface_version> in <platform> <platform-version>
date: <date>
CRediT attributions: <role>; <role>
Tools: <toolbox-doi-or-github-tree-url>
```

If multiple CRediT roles apply, list each separated by `;` on the same line.
Toolbox: use DOI if the toolbox has one (e.g. via Zenodo); otherwise use
the GitHub tree URL (`https://github.com/<org>/<repo>/tree/<hash>`).

**Example:**
```
-- AI-assisted or generated content --
model: claude-sonnet-4-6 (Anthropic) https://www.anthropic.com/claude/sonnet
access: Posit Assistant 1.4.1 in Positron 2026.09.0
date: 2026-09-17
CRediT attributions: Software; Data Curation
Tools: https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox/tree/ef0496a
```

### Research output disclosure

For README files, papers, or reports requiring a prose disclosure. Include
a formal citation of the model where possible.

```
This [output type] was developed with the assistance of <model-api-tag>
(<provider>), accessed via <interface> <interface_version> in <platform> on <date>.
The AI contributed to the following CRediT role(s): <credit_role(s)>.
Model identifier: <model_identifier>.
```

**Example:**
```
This codebase was developed with the assistance of claude-sonnet-4-6
(Anthropic), accessed via Posit Assistant 1.4.1 in Positron 2026.09.0 on
2026-09-17. The AI contributed to the following CRediT role(s): Software
(https://credit.niso.org/contributor-roles/software/). Model identifier:
https://www.anthropic.com/claude/sonnet.
```


### CRediT table row

For research outputs using the CRediT contributor taxonomy. Append to an
existing attribution table. Include the URI in the role column to make
attribution machine-resolvable (FORCE11 Unique Identification principle).

One row per CRediT role. If the AI contributed multiple roles, add a row
for each.

```
| <task> | [<credit_role>](<credit_role_uri>) | <model-api-tag> | <provider> | <interface> <interface_version> | <platform> | <date> |
| <task> | [<credit_role>](<credit_role_uri>) | <model-api-tag> | <provider> | <interface> <interface_version> | <platform> | <date> |
```

**Example:**
```
| Code refactoring | [Software](https://credit.niso.org/contributor-roles/software/) | claude-sonnet-4-6 | Anthropic | Posit Assistant 1.4.1 | Positron 2026.09.0 | 2026-09-17 |
| Roxygen documentation | [Writing – original draft](https://credit.niso.org/contributor-roles/writing-original-draft/) | claude-sonnet-4-6 | Anthropic | Posit Assistant 1.4.1 | Positron 2026.09.0 | 2026-09-17 |
```

---

## Workflow

Start every session by asking the user which mode they want:

> **Quick footer** — generate the git commit attribution footer only, no CRediT role assessment.
> **Full CRediT attribution** — evaluate roles and produce one or more attribution templates (commit footer, research disclosure, or CRediT table).

---

### Quick footer path

Gather the minimum fields and return the formatted block ready to paste. Skip the CRediT assessment entirely.

1. Ask the user to confirm the **model API tag** (e.g. `claude-sonnet-4-6`) — never guess.
2. Confirm **provider** (e.g. `Anthropic`) and **model identifier URL**.
3. Confirm **interface and version** (e.g. `Posit Assistant 1.4.1`).
4. Confirm **platform and version** (e.g. `Positron 2026.09.0`).
5. Retrieve the **toolbox commit hash** if a skill or prompt library was used (see Step 4 of the full path below). Omit the `Tools` line if no toolbox was used.
6. Return the formatted footer:

```
-- AI-assisted or generated content --
model: <model-api-tag> (<provider>) <model_identifier>
access: <interface> <interface_version> in <platform>
Tools: <toolbox-github-tree-url>
```

---

### Full CRediT attribution path

Follow Steps 1–6 below.

### Step 1 — Identify the model

Ask the user to confirm the provider API model tag — not the
human-readable name. For Anthropic models, this is the API model
identifier (e.g. `claude-sonnet-4-6`), visible in Posit Assistant
settings or the provider's API documentation. **Do not assume or guess**
— the AI cannot reliably self-report its own identifier, and incorrect
attribution undermines reproducibility.

### Step 2 — Self-assess CRediT roles

Before gathering other fields, review the session history and assign all
applicable CRediT roles using `CREDIT-ROLES-REFERENCE.md`:

1. Read through what was done in the session or the piece of work the user specified.
2. Make the distinction between what was prompted or proposed by the user and what 
   was novel conent creation or contributions by the generative AI model. 
2. Work through the self-assessment checklist in `CREDIT-ROLES-REFERENCE.md`.
3. Assign **all roles that genuinely apply** — multiple roles are expected
   and encouraged for typical GenAI sessions.
4. Do not assign roles speculatively. Only assign roles for work that was
   actually carried out.
5. Present the proposed role assignments to the user for confirmation
   before finalising the attribution.

### Step 3 — Gather required fields

Collect the remaining required fields. If any are unavailable, note that
in the attribution rather than omitting the field:

1. **Provider** — the company or organization behind the model
   (e.g. `Anthropic`, `OpenAI`, `Meta`, `Mistral AI`).
2. **Model identifier** — DOI is strongly preferred per FORCE11 Principle
   3 (Unique Identification). Check whether the model has a DOI (e.g.
   via Zenodo, a model paper, or the provider's documentation). If no DOI
   exists, use the stable model card URL as a fallback:
   - Anthropic API: `https://www.anthropic.com/claude/sonnet`
   - HuggingFace: `https://huggingface.co/<org>/<model-name>`
   - GitHub release: `https://github.com/<org>/<repo>/releases/tag/<version>`
   - Ollama: `https://ollama.com/library/<model>`
3. **Model location** — where the model is accessed or distributed, if
   different from the identifier. Note the distribution platform
   (e.g. `API`, `HuggingFace`, `Ollama`, `GitHub`, `Proprietary`).
4. **Interface** — the software used to interact with the model and its
   version (e.g. `Posit Assistant 1.4.1`, `Claude.ai`, `ChatGPT`, `GitHub Copilot`, `API`).
   In Positron, the Posit Assistant version is visible in the Extensions panel.
5. **Platform** — IDE or environment and version (e.g. `Positron 2026.09.0`).
   In Positron this is visible in the About dialog.

### Step 4 — Identify optional fields

6. **Toolbox version** — if a skill or prompt library was used, retrieve
   the identifier. **DOI is preferred** (check the toolbox README for a
   Zenodo DOI badge, or search [zenodo.org](https://zenodo.org) for the
   repo name). If no DOI exists, use the GitHub tree URL:
   ```bash
   git -C <path-to-toolbox> log --oneline -1
   # returns: <hash> <commit message>
   # full URL: https://github.com/<org>/<toolbox-repo>/tree/<hash>
   ```
7. **Date** — use today's date in `YYYY-MM-DD` format.

### Step 5 — Choose the right template(s)

| Context                  | Template                   |
|--------------------------|----------------------------|
| Git commit               | Git commit footer          |
| README / paper / report / poster / website | Research output disclosure |
| CRediT attribution table | CRediT table row           |

A single session may produce multiple artifacts. 

### Step 6 — Format and return

Fill in the chosen template(s) with the gathered fields. Return
formatted attribution blocks ready to copy or insert. Do not leave
required fields blank; if a value is genuinely unavailable, note it
explicitly (e.g. `model_identifier: not available`) and follow up with the user.

## Notes

- **Model API tag must be user-supplied.** Always ask the user to
  confirm — never infer or guess. For Anthropic models, the API tag
  (e.g. `claude-sonnet-4-6`) is visible in Posit Assistant settings.
- **DOI is preferred for `model_identifier`** per FORCE11 Principle 3.
  For models without a DOI, a stable model card or release URL satisfies
  the Unique Identification principle as a fallback.
- **Toolbox attribution is optional but recommended** for
  reproducibility — it pins the exact skill or prompt version used.
  Omit the toolbox line entirely if no toolbox was used.
- **CRediT is required** for all attribution contexts. It makes AI
  contributions machine-readable and interoperable with existing scholarly
  attribution infrastructure.

## References

- Smith AM, Katz DS, Niemeyer KE, FORCE11 Software Citation Working
  Group. (2016) Software Citation Principles. *PeerJ Computer Science*
  2:e86. <https://doi.org/10.7717/peerj-cs.86>
- NISO ANSI/NISO Z39.104-2022 CRediT standard.
  <https://doi.org/10.3789/ansi.niso.z39.104-2022>
- NISO CRediT implementation guidance.
  <https://credit.niso.org/implementing-credit/>
- Hosseini M, Kerridge S, Allen L, Kiermer V, Holmes K. (2026) CRediT
  Roles and Example Research Tasks That Could be Attributed to Them.
  Zenodo. <https://doi.org/10.5281/zenodo.18421449>
