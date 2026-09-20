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
