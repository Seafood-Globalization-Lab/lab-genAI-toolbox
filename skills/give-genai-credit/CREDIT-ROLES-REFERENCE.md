# CRediT Role Descriptions and GenAI Self-Assessment Guide

Use this reference when self-assigning CRediT roles for a GenAI session.
Review what was actually done, match activities to the role descriptions
below, and assign **all roles that genuinely apply**. Do not assign roles
speculatively — only assign roles for work that was carried out in the session.

Per the NISO CRediT standard: an AI contributor can hold multiple roles,
and the same role can be attributed to multiple contributors.

Role descriptions are drawn from the
[NISO ANSI/NISO Z39.104-2022 standard](https://doi.org/10.3789/ansi.niso.z39.104-2022)
and example tasks from
[Hosseini et al. (2026)](https://doi.org/10.5281/zenodo.18421449).

---

## Role Descriptions and GenAI Applicability

### Conceptualization
*Ideas; formulation or evolution of overarching research goals and aims.*

**Assign if the AI:** identified or clarified the research question or
problem; proposed a research framework, design, or experimental paradigm;
helped refine goals, scope, or hypotheses during brainstorming.

**Does NOT apply if:** the AI only implemented a design that was fully
specified by the human.

---

### Data Curation
*Management activities to annotate, scrub, and maintain research data (including software code necessary for interpreting data) for initial use and later reuse.*

**Assign if the AI:** wrote data cleaning, processing, or wrangling
pipelines; produced metadata, schemas, or data documentation; integrated
data from multiple sources; implemented data preservation or archiving
logic; contributed to version control of data assets.

**Does NOT apply if:** the AI only retrieved or read data without
transforming or documenting it.

---

### Formal Analysis
*Application of statistical, mathematical, computational, or other formal techniques to analyse or synthesize study data.*

**Assign if the AI:** ran or wrote statistical tests; fit or evaluated
models; applied machine learning or data mining methods; performed
quantitative synthesis; developed or applied simulation models.

**Does NOT apply if:** the AI only formatted output or wrote wrapper code
around an analysis the human specified in full detail.

---

### Funding Acquisition
*Acquisition of the financial support for the project.*

**Does not apply to GenAI.** AI models do not identify funding, write
proposals, or secure financial resources.

---

### Investigation
*Conducting a research and investigation process — performing experiments or data/evidence collection.*

**Assign if the AI:** searched and synthesized literature or web sources;
retrieved and reviewed external documentation, datasets, or specifications;
ran experiments or queries as part of the research process.

**Does NOT apply if:** the AI only wrote code or documentation without
performing any search, retrieval, or synthesis of external knowledge.

---

### Methodology
*Development or design of methodology; creation of models.*

**Assign if the AI:** proposed or designed an analytical or computational
methodology; developed a data collection or processing framework;
determined study design or analysis strategy; recommended a modelling
approach and justified the choice.

**Does NOT apply if:** the AI only implemented a methodology that was
fully designed by the human.

---

### Project Administration
*Management and coordination responsibility for the research activity.*

**Does not apply to GenAI** in most cases. AI models do not manage
timelines, budgets, or team logistics.

---

### Resources
*Provision of study materials, reagents, instruments, computing resources,
or other analysis tools.*

**Does not apply to GenAI.** AI models do not provision physical or
computational infrastructure.

---

### Software
*Programming, software development; implementation of computer code and supporting algorithms; testing of existing code components.*

**Assign if the AI:** wrote, generated, or substantially refactored code;
designed algorithms or data structures; implemented functions, scripts,
or modules; wrote tests; debugged existing code; contributed to package
or library development; wrote code documentation (docstrings, roxygen
headers, inline comments).

This is the most commonly applicable role for GenAI contributions to
software projects.

---

### Supervision
*Oversight and leadership responsibility for the research activity, including mentorship.*

**Does not apply to GenAI.** AI models do not supervise people or
research teams.

---

### Validation
*Verification of the overall replication/reproducibility of results, experiments, and other research outputs.*

**Assign if the AI:** checked assumptions for statistical tests or models;
verified that code produces expected outputs; reviewed results for
correctness or consistency; identified edge cases or tested boundary
conditions; checked that methods align with stated goals.

---

### Visualization
*Preparation, creation, and/or presentation of published work, specifically visualization/data presentation.*

**Assign if the AI:** wrote code to generate charts, graphs, or figures;
designed or specified a visualization; created interactive media or
dashboards; produced tables for publication.

---

### Writing – original draft
*Preparation of the published work, specifically writing the initial draft.*

**Assign if the AI:** wrote original prose for
reports, or manuscripts; drafted a methods section; generated the first complete version of written content (not just
editing existing text).

**Does NOT apply** if the AI only edited or reviewed text written by the
human — use Writing – review & editing instead.

---

### Writing – review & editing
*Critical review, commentary, or revision of published work — including pre- or post-publication stages.*

**Assign if the AI:** reviewed and provided feedback on existing text;
edited or revised prose, documentation, or code comments for clarity or
correctness; reviewed figures, tables, or supplementary materials.

**Does NOT apply** if the AI wrote the original text from scratch — use
Writing – original draft instead.

---

## Self-Assessment Checklist

Work through this checklist against the session history:

| Activity in the session                          | Role(s) to assign                                |
|--------------------------------------------------|--------------------------------------------------|
| Generated or substantially refactored code       | Software                                         |
| Wrote docstrings, roxygen headers, or comments   | Software                                         |
| Wrote methods, report prose, or manuscript       | Writing – original draft                         |
| Reviewed / edited existing documentation         | Software                                         |
| Wrote data cleaning or wrangling pipelines       | Software; Data Curation                          |
| Ran or wrote statistical tests or models         | Software; Formal Analysis                        |
| Generated plots or visualization code            | Software; Visualization                          |
| Searched literature or external documentation    | Investigation                                    |
| Proposed the analytical or design approach       | Methodology                                      |
| Helped define the research question or scope     | Conceptualization                                |
| Checked assumptions or verified correctness      | Validation                                       |
| Brainstormed architecture or design options      | Conceptualization; Methodology                   |

## Roles that do not apply to GenAI

Do not assign: **Funding Acquisition**, **Project Administration**,
**Resources**, **Supervision**.
