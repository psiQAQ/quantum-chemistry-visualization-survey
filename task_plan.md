# Task Plan: ChemBlender materials and on-demand OCR training

## Goal
在现有学习资料和 LX00 训练基础上，直接完成 `QUANTUM_CHEMISTRY_DATA_SEMANTICS.md` 的长期分子量子化学概念教程：主题 1–5 深入，扩展仅激发态，用 Gaussian、ORCA、PySCF 解释输入输出，不要求操作软件。

## Next Step
等待学习者回答 LX00 的缩小证据追问；能力等级保持未验证。

## Current Phase
Phase 4 - LX00 training (awaiting learner)

## Phases

### Phase 1: Inventory and manifest sync
- [x] 盘点本地资料文件、PDF 元数据、文字层和重复/分卷关系
- [x] 只登记能由标题、版本或内容确认的 manifest 资源
- **Status:** complete

### Phase 2: Directory-first OCR
- [x] 对文字层可靠的目录直接提取
- [x] 对不可复制文字的 PDF 只渲染目录候选页
- [x] 用 20260911 的 examforge.cli ocr 保存目录文本、页码、源哈希和原始 JSON
- **Status:** complete

### Phase 3: Verification
- [x] 检查资源映射、哈希、目录页码和 OCR 输出非空
- [x] 保持源 PDF 不变；记录缺失 SW03、DT01–DT04
- **Status:** complete

### Phase 4: LX00 training
- [x] 按用户确认的“数据语义优先 / 量化核心格式 / 解释与核验”范围启动首轮
- [x] 完成学习包、项目工作区、输入哈希和 Python 运行时的只读核验
- [x] 保持 learning_state.json 能力为 unverified
- [x] 只提出 LX00，不提前讲答案
- [x] 记录 LX00 首次回答并作最小状态更新，能力仍为 unverified
- [ ] 学习者回答缩小后的证据追问
- **Status:** in_progress

### Phase 5: 12-hour textbook and format atlas
- [x] 重构核心教材的最小公式链、假设、数据映射与页级来源
- [x] 建立覆盖当前 reader 能力矩阵的输入格式图鉴
- [x] 更新学习包入口，不修改 learning_state.json 或 LX00 evidence
- [x] 核验术语首现、公式、链接、格式覆盖与实例数据链
- **Status:** complete

### Phase 6: Long-term conceptual tutorial
- [x] 核对现有教材结构、已修改工作树和用户选择的范围
- [x] 核对新章节所需教材小节与软件官方资料
- [x] 在同一教材文件中完成概念主线、任务/输出、软件对照、验证和激发态
- [x] 移除与当前范围冲突的核心路线和周期案例，保留格式图鉴的独立参考边界
- [x] 验证章节、链接、公式、来源和学习状态边界
- **Status:** complete

### Phase 7: QCBlender second questionnaire draft
- [x] 核对第一版问卷、QCBlender 当前开发定位及 MolecularNodes、ChemBlender 的实际用途
- [x] 新建第二版问卷，分开调查近期行为、首版任务和后续方向
- [x] 保留可选的最后留言题，并加入共建邀请与发放前待填入口
- [x] 更新根目录 README，区分两版问卷并核验题号、变量、链接和隐私边界
- **Status:** complete

## Decisions Made
| Decision | Rationale |
|----------|-----------|
| 目录先处理，正文按需 OCR | 避免对整本教材进行高成本 OCR；目录足以支持后续页码定位 |
| 不使用 examforge repair 做目录准备 | 当前 CLI 没有页范围参数；用现有 fitz 只渲染目录候选页 |
| examforge 通过 python -m examforge.cli 调用 | .venv 未安装 editable 包，但源码位于 src/，设置 PYTHONPATH 即可复用现有环境 |
| 缺失或有歧义的资源不猜测登记 | 保持 DOI、版本和文件身份可追溯 |
| 首轮采用数据语义优先、量化核心格式 | 直接覆盖 ChemBlender 的 H2O MO/密度/ESP 数据链，不把完整量子化学课程作为前置 |
| 首轮产出为解释与核验 | 先验证学习者能否判断物理量、单位、网格和来源；暂不把代码修改作为首轮门槛 |
| 学习者作答前不更新学习状态或证据 | 保持能力为 unverified，避免把导师核验或渲染成功误记为学习证据 |
| 现场核验采用当前本地分支和文件哈希 | 报告中的历史快照不替代当前 ChemBlender 工作区事实 |
| 长期概念教程与完整格式图鉴分文件维护 | 教程按理解证据推进，完整格式覆盖作为按需查阅材料 |
| 教材完成不改变学习者能力状态 | 只有学习者独立作答和迁移证据才能更新 learning_state.json |

## Errors Encountered
| Error | Resolution |
|-------|------------|
| reading_manifest.json 仍是 22 项 not_downloaded | 将本地文件与 manifest 分离核验，后续只登记可确认项 |
| python -m examforge 找不到 __main__ | 改用 python -m examforge.cli |
| examforge 未安装为命令/模块 | 使用外部 OCR 工作区的 `src` 与项目 `.venv` |
| pypdf/pdfplumber 不在 OCR 环境 | 使用已安装的 fitz 做只读探查；不安装新依赖 |
| 直接 import paddleocr 会触碰用户默认缓存 | 设置 EXAMFORGE_HOME、TEMP/TMP、PADDLE_PDX_CACHE_HOME、MODELSCOPE_CACHE 和 HF_HOME 后再导入；CLI 按同一规则运行 |
| PDF text extraction failed under the PowerShell GBK output codec | Reconfigured Python stdout to UTF-8 with replacement and used `pymupdf`; the selected pages then extracted successfully |
| `apply_patch` rejected delete and add operations targeting the same file in one patch | Replaced the textbook using separate delete and add patch operations; no content was lost |
