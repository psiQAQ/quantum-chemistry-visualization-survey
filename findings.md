# Findings & Decisions

## Requirements
- 学习包实际目录：learning/chemblender_qc_learning。
- ChemBlender 工作区：用户本地的 `ChemBlender_2_x` 工作区。
- 首次训练只提出 LX00；学习者回答前不更新能力等级或生成答案。
- OCR 目录优先，正文只按后续学习需要处理。

## Research Findings
- 当前本地资料共 24 个文件：books 11、reviews 6、methods 3、software 4。
- BK01–BK05 均有候选文件，但 BK02、BK04 是分卷，BK05 有 PDF/DJVU 重复文件。
- RV01 有目标论文和一个额外候选标题；需要按 DOI/内容区分。
- FD01–FD03 已有候选文件。
- SW01、SW02、SW04、SW05 已有候选文件；SW03 未发现。
- DT01–DT04 未发现本地文件。
- fitz 可用；部分论文已有文字层，若干中文教材和部分分卷前 3 页无文字层。
- OCR 项目可用环境：用户本地的 `20260911` OCR 工作区及其 `.venv`，源码入口为 `src/examforge/cli.py`，PaddleOCR 可导入。

## Technical Decisions
| Decision | Rationale |
|----------|-----------|
| 资源状态以源文件哈希、实际路径、版次核对为准 | 用户文件名可能是分卷、重复或非标准命名 |
| 目录 OCR 结果保存为导航证据，不视为正文科学证据 | OCR 可能错识别公式、页码或标题 |
| 后续正文读取优先页面图像 + 可提取文本，必要时单页 OCR | 保留布局、公式和图表语义 |

## Issues Encountered
| Issue | Resolution |
|-------|------------|
| Manifest 尚未同步 | Phase 1 仅登记确证映射，不批量猜测 |
| OCR 项目没有 examforge.__main__ | 使用 examforge.cli |

## Resources
- learning/chemblender_qc_learning/CODEX_HANDOFF.md
- learning/chemblender_qc_learning/reading_manifest.json
- 外部 OCR 工作区的 `README.md`
- 外部 OCR 工作区的 `docs/ocr-backends.md`

## Completed verification: 2026-09-20

- Manifest contains 22 resources: 17 downloaded mappings with existing files and matching SHA-256; SW03 and DT01–DT04 remain not_downloaded.
- Catalog index contains 17 mapped resources. BK02 uses direct text-layer pages 4, 6, 8; BK03 uses OCR pages 11–18; BK04 uses OCR directory pages 4, 6–8, 10 after rendering the first 30 candidate pages.
- BK03 raw OCR has 8 JSON and 8 nonempty Markdown pages. BK04 has 30 JSON and 30 Markdown pages, 29 nonempty; page 13 is retained as an empty OCR result for review.
- ChemBlender live check passed on branch feat/2.5-real-user-tutorials at HEAD 2e94f90419aab186f522fb3b701746839ee8c5c4 with a clean worktree; Blender 5.1.1 and project Python imports numpy, rdkit, gemmi, cbq_core, and chemblender_prepare.
- OCR environment check passed with `EXAMFORGE_HOME` 指向外部 OCR 工作区，Python 3.13.12、fitz、paddleocr 和 examforge.cli 可用。学习仓库中的生成缓存已移除；源 PDF 未修改。

## Learning plan selection and live verification — 2026-09-20

- Learner selected the data-semantics-first route, the quantum-chemistry core format set, and explanation/validation (no code change) as the first-sprint deliverable.
- First-sprint core chain: Schrödinger assumptions → HF/DFT/SCF input/output meaning → MO, density, ESP → Grid3D metadata → FCHK/Molden/QCSchema/Cube → CBQ and `.npy` arrays.
- The learning package, current `LX00`, and project runtime were checked read-only. `learning_state.json` remains `not_started` with all capabilities `unverified`; no LX00 evidence exists before the learner's answer.
- Current ChemBlender branch is `feat/2.5-real-user-tutorials` at `2e94f90`; the worktree is clean. H2O and CH3 FCHK fixtures and the water CBQ manifest are present; project Python imports passed for numpy, rdkit, gemmi, cbq_core, and chemblender_prepare.

## Textbook drafting evidence — 2026-09-20

- Current project documentation defines the first-sprint scientific chain as normalized Gaussian basis → MO or one-particle reduced density matrix → sampled `Grid3D` → CBQ/array storage → Blender Volume/Mesh/Material views; Blender display objects consume derived data and do not define the grid semantics.
- Current model contracts retain grid `origin`, three `step_vectors`, `shape`, coordinate unit, value unit, `semantic_role`, dataset identity and provenance; unrestricted orbital sets retain alpha/beta channels.
- The H2O FCHK fixture reports RHF/STO-3G, charge 0, multiplicity 1, 10 electrons, 5 alpha electrons, 5 beta electrons and 7 basis functions. These fields are examples for teaching file-to-quantity mapping, not a claim that the fixture is a general accuracy benchmark.
- The new textbook must keep derivations, solver implementation, periodic/spectroscopy/local-analysis topics outside the first-sprint core and must not provide the LX00 diagnostic answer.
- Source inspection confirms `ArrayData` requires named dimensions and a lower-snake-case unit; `Grid3D` requires `(x, y, z)` trailing dimensions, finite origin, three linearly independent step vectors and a dimensional coordinate unit.
- Source inspection confirms `OrbitalSet` channel contracts: restricted → `restricted`, unrestricted → `alpha` plus `beta`, generalized → `generalized`; orbital energies use Hartree and occupations are dimensionless.
- Source inspection confirms `DensityMatrix` is a real square dimensionless AO matrix with explicit `scf`/`post_scf` level and `total`/`spin` role; `PropertyDataset` rejects unknown units unless status is `ambiguous`.

## Textbook validation — 2026-09-20

- `QUANTUM_CHEMISTRY_DATA_SEMANTICS.md` is present with 945 lines, 12 chapters, an appendix, local source links and immediate explanations for the first occurrence of required technical terms.
- The textbook includes the selected data-semantics route, actual H2O/CH3 teaching context, ChemBlender model contracts and the minimum validation/provenance boundaries; it intentionally omits the LX00 answer.
- Final state guard passed: `learning_state.json` remains `status=not_started`, current exercise `LX00`, competence `unverified`, with zero evidence and zero completed exercises.

## Commit scope — 2026-09-20

- Local source PDFs/DJVU, package archives and rendered page/crop images are excluded by scoped rules for the whole `learning` directory in the repository `.gitignore`; the learning package's catalog metadata and OCR text/JSON remain available for review and traceability.
- Raw OCR JSON retains page dimensions, model settings and parsed content while its image `input_path` is repository-relative rather than a workstation-specific absolute path.
