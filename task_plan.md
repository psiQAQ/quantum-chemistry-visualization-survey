# Task Plan: ChemBlender materials and on-demand OCR training

## Goal
核对本地学习资料，登记可确认的资源，按目录优先策略建立可追溯的按需 OCR 入口，并启动 LX00。

## Next Step
等待学习者完成 LX00 首次独立作答；作答后才更新 learning_state.json 和 evidence。

## Current Phase
Phase 4 - LX00 training

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
- [ ] 学习者回答后写入 LX00 证据和最小状态更新
- **Status:** in_progress

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

## Errors Encountered
| Error | Resolution |
|-------|------------|
| reading_manifest.json 仍是 22 项 not_downloaded | 将本地文件与 manifest 分离核验，后续只登记可确认项 |
| python -m examforge 找不到 __main__ | 改用 python -m examforge.cli |
| examforge 未安装为命令/模块 | 使用外部 OCR 工作区的 `src` 与项目 `.venv` |
| pypdf/pdfplumber 不在 OCR 环境 | 使用已安装的 fitz 做只读探查；不安装新依赖 |
| 直接 import paddleocr 会触碰用户默认缓存 | 设置 EXAMFORGE_HOME、TEMP/TMP、PADDLE_PDX_CACHE_HOME、MODELSCOPE_CACHE 和 HF_HOME 后再导入；CLI 按同一规则运行 |
