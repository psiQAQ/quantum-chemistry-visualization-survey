# Broaden Survey Audience Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the 30-question calculator-centric survey with an 18-question role-aware survey that also supports collaborators, outsourced-computation clients, and result users.

**Architecture:** Keep one shared questionnaire and use `workflow_role` as the only branching gate. Seventeen questions are answerable from a respondent's own experience; the file-format question is shown only to respondents who personally run calculations, choose methods/parameters, or prepare inputs/workflows.

**Tech Stack:** Markdown, one-time `.xlsx` extraction with `@oai/artifact-tool`, Node.js verification scripts.

## Global Constraints

- Keep the questionnaire at exactly 18 questions and estimate 6–9 minutes.
- Do not ask non-calculators to guess programs, parameters, formats, or validation actions.
- Render every option as an indented Markdown list below `- 选项：`.
- Keep PySCF as one secondary awareness variable.
- Preserve evidence links and public privacy boundaries.
- Keep the questionnaire Markdown as the only source for question IDs, variables, types, options, and branches.

---

### Task 1: Rewrite the Markdown questionnaire

**Files:**
- Modify: `docs/survey/quantum_chemistry_software_survey_draft_2026-07-19.md`

**Interfaces:**
- Consumes: approved audience design and existing literature-note links.
- Produces: Q01–Q18 with variables `role`, `domain`, `workflow_role`, `result_artifacts`, `collaboration_pain_points`, `trust_evidence`, `viz_frequency`, `field_types`, `current_viz_forms`, `field_file_formats_12m`, `viz_pain_consequences`, `pipeline_value`, `feature_priority`, `evidence_package`, `delivery_modes`, `top_adoption_barrier`, `pyscf_awareness`, and `open_case`.

- [x] **Step 1: Record the current failing count**

Run:

```powershell
(rg -c '^### Q\d{2}' docs/survey/quantum_chemistry_software_survey_draft_2026-07-19.md)
```

Expected: `30`, which fails the 18-question target.

- [x] **Step 2: Replace the questionnaire body with Q01–Q18**

Use five sections: background and participation; collaboration and delivery; visualization; engine-independent concept; secondary variable and open feedback. Make Q10 conditional on `workflow_role` containing personal calculation, method/parameter decisions, or input/workflow preparation. All other questions remain common.

- [x] **Step 3: Format options as nested lists**

Every option block must use this exact shape:

```markdown
- 选项：
  - 选项一
  - 选项二
```

Do not keep semicolon-delimited option strings.

- [x] **Step 4: Check the Markdown structure**

Run:

```powershell
node tmp/final_verify.mjs
```

Expected: 18 continuous question IDs, 18 unique variables, valid Q10 branch, and no privacy hits.

### Task 2: Retire the workbook without losing unique content

**Files:**
- Create: `data/research-evidence-catalog-2026-07-20.md`
- Create: `docs/research/software_and_validation_reference_2026-07-20.md`
- Delete: `data/pyscf_quantum_chemistry_visualization_research_2026-07-19.xlsx`
- Delete: `assets/previews/pyscf_research_overview_preview_2026-07-19.png`
- Create (ignored helper): `tmp/export_workbook_to_md.mjs`

**Interfaces:**
- Consumes: the former workbook's 72 evidence rows, 18 software rows, and 20 validation rows.
- Produces: two Markdown references; the survey Markdown remains the only questionnaire source.

- [x] **Step 1: Load the existing workbook with the bundled artifact runtime**

Use `codex_app__load_workspace_dependencies` and create the required temporary `node_modules` junction for the one-time extraction.

- [x] **Step 2: Export unique workbook content to Markdown**

Export the 72-row evidence catalog separately; combine the 18-row software matrix and 20-row validation matrix in one research reference. Do not duplicate the questionnaire because its Markdown is already authoritative.

- [x] **Step 3: Verify exported row counts**

Run:

```powershell
node tmp/export_workbook_to_md.mjs
```

Expected: 72 evidence rows, 18 software rows, and 20 validation rows.

- [x] **Step 4: Remove the retired workbook and preview**

Delete the replaced `.xlsx` and preview image after checking the Markdown output. Both remain recoverable from Git history.

### Task 3: Update project summaries and publish

**Files:**
- Modify: `README.md`
- Modify: `AGENTS.md`
- Modify: `docs/research/literature_review_cross_cutting_findings_2026-07-19.md`
- Modify: `docs/research/pyscf_quantum_chemistry_visualization_research_2026-07-19.md`

**Interfaces:**
- Consumes: final 18-question questionnaire and Markdown research references.
- Produces: public documentation that consistently states the broader audience and 6–9 minute length.

- [x] **Step 1: Replace stale questionnaire counts and audience wording**

Update only references that still say 30 questions, 12–15 minutes, or imply respondents personally run calculations.

- [x] **Step 2: Run final repository checks**

Run:

```powershell
node tmp/final_verify.mjs
node tmp/check_repo_layout.mjs
node tmp/verify_notes.mjs
git diff --check
```

Expected: all commands exit 0; question count 18; no broken links, duplicate variables, tracked `tmp/` files, or privacy hits.

- [ ] **Step 3: Commit and push**

Stage only the survey, Markdown research references, workbook/preview deletions, README, research summaries, AGENTS, plan, and design update. Scan staged content for personal paths, institution names, credentials, and Zotero keys; commit with `docs: broaden survey audience`; push `main` to `origin` and verify local HEAD equals `origin/main`.
