# Progress Log

## Session: 2026-09-20

### Current Status
- **Phase:** 1 - Inventory and manifest sync
- **Started:** 2026-09-20

### Actions Taken
- 读取 personal-tutor、pdf、planning-with-files Skill。
- 核对学习包与 ChemBlender 工作区实际路径和 Git 状态。
- 发现 24 个本地资料文件，manifest 尚未登记。
- 确认 OCR 环境、fitz、PaddleOCR 和 examforge.cli 调用方式。

### Test Results
| Test | Expected | Actual | Status |
|------|----------|--------|--------|
| fitz import in 20260911 .venv | available | imported | Passed |
| paddleocr import | available for later OCR | imported | Passed |
| python -m examforge.cli --help with PYTHONPATH=src | CLI available | exit 0 | Passed |
| python -m examforge --help | not required entrypoint | no __main__ | Not Used |
| pypdf/pdfplumber import | optional | missing | Not Run |

## Implementation update

- Manifest synchronized: 17/22 resources mapped by actual path, hash, and local edition evidence; 5 intentionally remain not_downloaded.
- Directory catalog added at learning/chemblender_qc_learning/materials/catalog with source hashes and page mappings.
- OCR completed only for BK03 directory pages 11–18 and BK04 candidate pages 1–30; raw JSON and Markdown retained.
- Verification passed for manifest JSON, catalog JSON, all mapped hashes, and raw OCR JSON parsing. learning_state.json remains unchanged with status not_started and all nine capabilities unverified.
- Current phase is LX00 training; final user-facing turn will contain only the LX00 prompt.

## Session: 2026-09-20 — first-sprint scope and environment gate

### Current Status
- **Phase:** 4 - LX00 training
- **Scope:** data-semantics-first; quantum-chemistry core formats; explanation and validation only
- **Next:** present LX00 main question and wait for the learner's independent answer

### Scope Decisions
- Required first-sprint topics: minimum Schrödinger/HF/DFT/SCF assumptions, MO/density/ESP semantics, Grid3D metadata, FCHK/Molden/QCSchema/Cube/CBQ/.npy data chain, and minimum numerical/provenance checks.
- Deferred: derivations, solver implementation, coupled-cluster/multireference/GPU, periodic and spectroscopy topics, local-analysis methods, and full Reader coverage.
- Online DT01–DT04 remain URL/version/date references by default; no bulk local snapshot is required.

### Read-only Environment Gate
| Check | Result | Status |
|---|---|---|
| Learning package and current exercise | `learning/chemblender_qc_learning` exists; current exercise `LX00` | Passed |
| Learning state before answer | `status=not_started`, competence `unverified`, no evidence or completed exercise | Passed |
| ChemBlender worktree | `feat/2.5-real-user-tutorials`, HEAD `2e94f90`, clean | Passed |
| Project runtime | Python 3.12.13; numpy, rdkit, gemmi, cbq_core and chemblender_prepare import | Passed |
| Scientific fixtures | H2O and CH3 FCHK exist with manifest-linked SHA-256; water CBQ manifest exists | Passed |

### State Guard
- No `learning_state.json` or `evidence/LX00-session-*.md` was changed before the learner answers.

### Textbook preparation
- Read the report concept skeleton and current ChemBlender Grid3D/wavefunction documentation.
- Confirmed the H2O FCHK teaching fields: RHF/STO-3G, neutral singlet, 10 electrons, 5 alpha/5 beta electrons and 7 basis functions.
- Next action: create the standalone Markdown textbook without answering LX00 or changing learning state.
- Source model check completed: ArrayData dimensions/units, Grid3D affine geometry, OrbitalSet channel contracts, DensityMatrix level/spin role and ambiguous-dataset policy are recorded in findings.md.

### Textbook delivery — 2026-09-20
- Created `learning/chemblender_qc_learning/QUANTUM_CHEMISTRY_DATA_SEMANTICS.md` as the standalone first-sprint textbook: 12 chapters, appendix glossary and self-check questions.
- Added the textbook entry to `learning/chemblender_qc_learning/README.md`.
- Validation passed for required topic coverage, local cross-links and first-occurrence term explanations; `learning_state.json` remains `not_started` / `unverified`, and no LX00 evidence was created.
- Phase 4 remains in progress because the learner's independent LX00 answer is still required before updating learning state and evidence.

### Commit preparation — 2026-09-20
- Added scoped `.gitignore` rules for downloaded source documents and rendered/extracted images under the whole `learning` directory (`pdf`, `djvu`, common office/ebook/archive formats and raster/vector images).
- Catalog indexes, OCR Markdown/JSON, source hashes, textbook, exercises and learning state remain eligible for tracking.
- Sanitized catalog raw OCR JSON `input_path` values to repository-relative paths and removed workstation-specific absolute paths from the commit notes.

## Session: 2026-09-20 — 12-hour textbook and format atlas

### Current Status
- **Phase:** 5 - 12-hour textbook and format atlas
- **Scope:** documentation only; no ChemBlender code or schema changes

### Actions Taken
- Reloaded personal-tutor, planning-with-files and PDF instructions plus learning-pattern guidance.
- Verified the learning repository is clean before edits and read the existing textbook, README and learning state.
- Re-verified ChemBlender at `2e94f90419aab186f522fb3b701746839ee8c5c4` and loaded the current format documentation and reader capability matrix.

### State Guard
- `learning_state.json` and LX00 evidence remain unchanged; authoring progress is not learner evidence.

### Source and fixture review
- Enumerated the 22 reader entries and collapsed duplicate/adapter entries into user-visible format families.
- Located existing minimal fixtures for all planned atlas families; no new scientific fixture is required for the documentation task.
- Visually checked six preserved course pages for the Schrödinger, effective one-electron, Roothaan, Hohenberg-Kohn and Kohn-Sham equations.
- Re-extracted selected English textbook pages with UTF-8 stdout after the first GBK console attempt failed on mathematical glyphs.

### Authoring
- Replaced `QUANTUM_CHEMISTRY_DATA_SEMANTICS.md` with the 12-hour core textbook. The new theory chain includes the molecular Hamiltonian, BO separation, units, variational/single-determinant assumptions, Gaussian primitives/contractions, Roothaan-Hall, SCF caveats, Kohn-Sham DFT, common result types and formula-to-data mappings.
- Added `CHEMBLENDER_INPUT_FORMAT_ATLAS.md`, covering every current reader entry through user-visible format families with real fixture snippets, unit/semantic boundaries, dependencies, conversion loss and validation checks.
- Updated the learning-package README to link both Markdown teaching artifacts and record the ChemBlender source snapshot.
- The first whole-file replacement patch was rejected because one patch contained delete and add operations for the same path; the file was then replaced with separate delete and add patches.

### Final validation
- Formula structure passed: 68 display-math delimiters form 34 closed blocks; all 19 key formula groups have `成立条件`、`符号`、`数据对应` and `不能推出` entries.
- Format coverage passed: all 22 reader IDs appear exactly once in the coverage mapping; all 20 format/persistence cards contain a real snippet, physical quantity, units, calculation conditions, ChemBlender mapping, ambiguity, loss, validation and fixture reference.
- Fixture traceability passed: all 25 fixture paths named by the atlas exist in the pinned ChemBlender workspace.
- Markdown validation passed: numbered atlas sections are continuous from 1 to 26, all checked local links resolve, no trailing whitespace remains and `git diff --check` passes.
- Scientific state guard passed: ChemBlender HEAD remains `2e94f90419aab186f522fb3b701746839ee8c5c4`; `learning_state.json` and LX00 evidence are unchanged.
## Session: 2026-09-22 — LX00 first response

- Learner selected a long-term conceptual route for molecular quantum chemistry: deepen topics 1–5, cover excited states, and defer other advanced topics. Gaussian, ORCA, and PySCF serve as input/output examples; software operation is not currently required.
- Learner's first independent LX00 response was exactly “未知”. Recorded it in `evidence/LX00-session-2026-09-22.md` without inferring a misconception or assigning a capability level.
- Updated `learning_state.json` to `in_progress`, aligned the learning target with the learner's current scope, and left every capability `unverified`.
- Next step: ask one narrower evidence question, then adapt the explanation to the learner's answer.

## Session: 2026-09-22 — Long-term molecular tutorial

- Completed the full concept tutorial in `QUANTUM_CHEMISTRY_DATA_SEMANTICS.md` for the learner's selected scope: molecular inputs, HF/DFT/basis/SCF, single-point/optimization/frequencies, orbitals/density/partial charge/ESP, reliability, and excited states.
- Added task-specific output reading, Gaussian/ORCA/PySCF input comparison, a seven-stage reading route across Levine, Szabo–Ostlund, and Jensen, and independent interpretation checkpoints. Removed the periodic Si teaching case and the 12-hour time contract from the tutorial.
- Checked Levine PDF pp.494, 510-511, 566-567 and Jensen PDF pp.340-350 for geometry/frequency, DFT, and partial-charge claims; cross-checked ORCA and PySCF official input/output documentation. Gaussian example remains tied to an existing local project fixture.
- Validation passed: 12 chapters, 10 closed code fences, 35 closed display-math blocks, 21 Markdown links with all local targets present, and `git diff --check`. `learning_state.json` still has LX00 in progress and every capability unverified; no new learner evidence was inferred from authored material.

## Session: 2026-09-22 — QCBlender questionnaire second draft

- Read the first questionnaire and current QCBlender, MolecularNodes and ChemBlender descriptions. QCBlender has a development extension skeleton but no formal first release; the new respondent-facing text reflects that state.
- Added `docs/survey/qcblender_survey_draft_2026-09-22.md` with 17 questions: recent experience screening, observed workflow, planned first-version task priorities, later priorities and an optional final ideas question. The invitation uses two explicit placeholders for public contact and repository links.
- Updated root README to distinguish the preserved ChemBlender first questionnaire from the QCBlender second draft. No learning-state or literature records were changed for this task.
- Structural and link checks passed; elapsed completion time and question comprehension await 10–15 cognitive interviews. The two invitation links must be filled before distribution.
