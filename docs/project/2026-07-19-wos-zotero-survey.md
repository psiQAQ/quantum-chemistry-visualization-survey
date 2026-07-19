# WoS、Zotero 文献调研与问卷完善实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 使用 Zotero 已有全文和不超过 12 次 Web of Science 定向检索，完成文献分组、元数据与 BibTeX/工作簿同步，并形成有全文证据支撑的 12–15 分钟中文问卷。

**Architecture:** Zotero 调研父分类是文献状态源，其内部 key 仅保存在 `tmp/`；WoS 只补证据缺口。公开文件保存可复核的文献元数据、结论和问卷；含隐私的检索日志、学校访问记录与临时清单只保存在已忽略的 `tmp/`。若 P0/P1 关键全文缺失，则输出精选下载清单并停止最终定稿。

**Tech Stack:** Zotero MCP、Web of Science Starter API、BibTeX、Markdown、XLSX、`@oai/artifact-tool`、PowerShell、Git。

## Global Constraints

- Web of Science 本轮调用计划 6–10 次，硬上限 12 次，每次最多返回 50 条。
- 不使用 Sci-Hub 或其他绕过付费墙的来源。
- 用户隐私、学校访问方式、凭据、个人身份信息和含隐私的原始记录只能进入 `tmp/`。
- Zotero 新增条目必须通过 DOI 或标题相似度查重，并同步到 BibTeX 和工作簿。
- P0/P1 关键全文缺失时不得宣布最终完成；等待用户确认精选文献已补全。
- 不覆盖 `.gitignore`、`README.md` 或其他用户已有未提交改动；Git 只暂存当前任务明确修改的文件。

---

### Task 1: 项目规则与基线

**Files:**
- Create: `AGENTS.md`
- Create: `tmp/wos_search_log.md`
- Create: `tmp/pdf_priority.md`

**Interfaces:**
- Consumes: `docs/project/2026-07-19-wos-zotero-survey-design.md`
- Produces: 公开文件约束、WoS 调用计数和缺 PDF 分级记录。

- [ ] **Step 1: 创建项目级规则**

在 `AGENTS.md` 写明：研究目标、公开/隐私边界、WoS 12 次硬上限、Zotero/BibTeX/工作簿同步、P0/P1 全文停止门槛和验证要求。

- [ ] **Step 2: 建立私密工作记录**

在 `tmp/wos_search_log.md` 记录 `调用序号｜查询式｜用途｜返回数｜纳入数`；在 `tmp/pdf_priority.md` 记录 `itemKey｜DOI｜优先级｜问卷模块｜需要全文确认的内容`。

- [ ] **Step 3: 验证隐私边界**

Run:

```powershell
git check-ignore -v tmp/wos_search_log.md tmp/pdf_priority.md
git status --short
```

Expected: 两个 `tmp/` 文件均由根目录 `.gitignore` 的 `tmp/` 规则忽略；Git 状态只显示公开任务文件。

- [ ] **Step 4: 提交项目规则**

```powershell
git add -- AGENTS.md
git commit -m "docs: define survey research safeguards"
```

### Task 2: Zotero 子分类与现有全文盘点

**Files:**
- Modify: `tmp/pdf_priority.md`

**Interfaces:**
- Consumes: Zotero 调研父分类中的记录及附件；内部 key 和动态条目数从 `tmp/` 的本地状态读取。
- Produces: 8 个子分类、主题归类结果、全文/缺 PDF 基线。

- [ ] **Step 1: 回读父分类和附件状态**

调用 `get_collection_items`、`find_missing_pdfs(action=report)` 和 `get_subcollections`；预期当前为 53 条、37 条有 PDF、16 条缺 PDF、0 个子分类。若实际状态变化，以回读值为准。

- [ ] **Step 2: 创建 8 个子分类**

使用保存在 `tmp/` 的父分类 key 调用 `create_collection`，创建：`00｜优先精读`、`01｜PySCF 核心与生态`、`02｜量子化学软件与采用`、`03｜数值可靠性与跨引擎基准`、`04｜互操作、自动化与工作流`、`05｜数据格式与科学可视化`、`06｜科研软件工程与可持续性`、`07｜用户研究、信任与维护`。

- [ ] **Step 3: 主题归类**

根据工作簿 `核心文献` 的主题、标题、证据用途和对项目含义，调用 `add_items_to_collection`；直接支撑问卷构念的条目同时加入 `00｜优先精读`。

- [ ] **Step 4: 回读验证**

调用 `get_subcollections(recursive=true)` 与每个子分类的 `get_collection_items`。Expected: 8 个子分类均存在；父分类仍为 53 条；交叉归类不生成重复 Zotero 条目。

### Task 3: 全文证据盘点与 WoS 定向筛选

**Files:**
- Modify: `tmp/wos_search_log.md`
- Modify: `tmp/pdf_priority.md`

**Interfaces:**
- Consumes: Zotero 37 篇现有 PDF、当前问卷 27 题、WoS `search_web_of_science`。
- Produces: 问卷构念证据映射、新增候选和 P0/P1/P2 缺全文分级。

- [ ] **Step 1: 读取优先全文**

对 `00｜优先精读` 中有 PDF 的条目使用 `get_content(mode=standard)`，围绕实际工作流、可视化行为、痛点、科学可信度、跨引擎验证、采用障碍和软件可持续性提取证据。无全文时不把摘要当作关键结论。

- [ ] **Step 2: 执行 6 个基础 WoS 查询**

每个查询调用一次，`maxResults=50`、`detail=full`，优先按相关性或被引排序：

```text
TS=((quantum chem* OR electronic structure) AND (visualization OR visualisation OR "three-dimensional field" OR isosurface))
TS=((quantum chem* OR electronic structure) AND (software adoption OR user survey OR usability OR workflow))
TS=((computational chem* OR scientific software) AND (trust OR reproducib* OR validation OR credibility))
TS=((quantum chem* OR electronic structure) AND (interoperab* OR provenance OR metadata OR workflow standard*))
TS=((quantum chem* OR density functional theory) AND (cross-code OR inter-code OR benchmark*) AND software)
TS=((cube OR volumetric data OR electron density OR molecular orbital) AND (Blender OR OpenVDB OR visualization))
```

- [ ] **Step 3: 仅在证据缺口仍存在时追加查询**

最多再调用 4 次；任何情况下总调用不超过 12。每次将查询式、用途、返回数和纳入数写入 `tmp/wos_search_log.md`。

- [ ] **Step 4: 筛选与查重**

纳入标准：同行评议论文或正式综述；直接支持问卷构念；有 DOI/PMID/ISBN；标题摘要相关。排除标准：仅算法性能且无工作流/可视化/验证含义；重复；社论或不可核实来源。先用 `search_library` 查 DOI/标题，再调用 `import_by_identifier(if_exists=skip, fetch_pdf=false)`。

- [ ] **Step 5: 补元数据与分类**

对新增条目调用 `enrich_item_metadata(confirm=true)`，加入父分类和对应子分类；把直接支持问卷的条目加入 `00｜优先精读`。

- [ ] **Step 6: 缺全文分级**

调用 `find_missing_pdfs(action=report)`，按 P0/P1/P2 更新 `tmp/pdf_priority.md`。如果存在 P0/P1，停止 Task 5 的最终定稿，向用户提交精选下载清单并等待确认。

### Task 4: 同步 BibTeX、工作簿、报告和预览

**Files:**
- Modify: `data/pyscf_quantum_chemistry_literature_2026-07-19.bib`
- Modify: `data/pyscf_quantum_chemistry_visualization_research_2026-07-19.xlsx`
- Modify: `docs/research/pyscf_quantum_chemistry_visualization_research_2026-07-19.md`
- Modify: `assets/previews/pyscf_research_overview_preview_2026-07-19.png`
- Modify: `README.md`

**Interfaces:**
- Consumes: WoS 纳入结果、Zotero 最终元数据和主题归类。
- Produces: DOI、标题、作者、年份、证据用途和条目统计一致的公开资料包。

- [ ] **Step 1: 更新 BibTeX**

为 WoS 新增且纳入的条目添加规范 BibTeX；key 唯一，DOI 唯一。保留现有条目，不用缩写作者覆盖已核实的完整作者。

- [ ] **Step 2: 更新工作簿**

使用 `@oai/artifact-tool` 导入工作簿，在 `核心文献` 追加新记录并更新概览统计；必要时更新软件矩阵和证据用途。不得使用其他 XLSX 写入库。

- [ ] **Step 3: 更新报告与 README**

将新增证据、修正后的结论、实际 WoS 检索范围和最新条目统计写入调研报告与 README；不写入个人身份、学校访问细节或本机私密路径。

- [ ] **Step 4: 重新渲染预览图**

用 `@oai/artifact-tool` 渲染 `概览` 工作表并保存为现有 PNG 文件；目视检查统计、图表和文字没有裁切。

- [ ] **Step 5: 验证同步一致性**

Expected: Zotero、BibTeX 和工作簿的纳入 DOI 集一致；概览条目数等于核心文献记录数；BibTeX 无重复 key/DOI；工作簿无公式错误。

### Task 5: 修订问卷并执行全文门槛

**Files:**
- Modify: `docs/survey/quantum_chemistry_software_survey_draft_2026-07-19.md`

**Interfaces:**
- Consumes: 已核实全文证据、P0/P1 门槛、问卷构念映射。
- Produces: 12–15 分钟中文问卷或等待补全文的非最终草案。

- [ ] **Step 1: 重排问卷模块**

按背景与实际工作流、可视化行为、痛点与风险、可信度与验证、概念测试、功能与部署、PySCF 次级变量、开放问题的顺序重排。

- [ ] **Step 2: 修订题目**

删除品牌中心和重复问题；补充使用频率、失败后果、元数据最低要求、跨引擎验证行为、现有替代方案和概念采用门槛。每题保留唯一变量、题型、必答和分支逻辑。

- [ ] **Step 3: 应用全文停止门槛**

如果 `tmp/pdf_priority.md` 存在 P0/P1 未补全文，在问卷文件中只保存清晰标记的研究草案，不宣称最终版，并等待用户确认精选文献补全；否则完成最终措辞。

- [ ] **Step 4: 结构验证**

检查题号连续、变量唯一、分支目标存在、选项互斥性合理、预计时长 12–15 分钟，并确保没有隐私内容。

### Task 6: 最终审计与提交

**Files:**
- Verify: `AGENTS.md`
- Verify: `README.md`
- Verify: `data/pyscf_quantum_chemistry_literature_2026-07-19.bib`
- Verify: `data/pyscf_quantum_chemistry_visualization_research_2026-07-19.xlsx`
- Verify: `docs/research/pyscf_quantum_chemistry_visualization_research_2026-07-19.md`
- Verify: `assets/previews/pyscf_research_overview_preview_2026-07-19.png`
- Verify: `docs/survey/quantum_chemistry_software_survey_draft_2026-07-19.md`

**Interfaces:**
- Consumes: Tasks 1–5 的全部产物。
- Produces: 完成证据或明确的 P0/P1 文献阻塞状态。

- [ ] **Step 1: 隐私扫描与 Git 范围检查**

检查 Git 可跟踪文件不存在凭据、学校访问细节、个人身份和私密本机路径；确认 `tmp/` 仍被忽略。

- [ ] **Step 2: 资料一致性检查**

核对 Zotero/工作簿/BibTeX 条目与 DOI 统计、8 个子分类、附件数、WoS 调用次数和问卷结构。

- [ ] **Step 3: 提交公开产物**

仅暂存本计划涉及且已验证的公开文件；不暂存用户无关改动。提交信息：

```powershell
git commit -m "docs: update quantum chemistry visualization survey"
```

- [ ] **Step 4: 目标状态**

若所有 P0/P1 全文齐全且所有验证通过，标记目标 complete；否则保持目标进行中并等待用户补文献。只有同一缺全文条件连续三个目标回合且无法继续时才标记 blocked。

### Task 7: 导出阅读笔记证据库并更新问卷（2026-07-20）

**Files:**
- Read: `data/pyscf_quantum_chemistry_literature_2026-07-20.bib`
- Create: `data/literature-notes/<topic>/<BibTeXKey>__<short-title>.md`
- Modify: `README.md`
- Modify: `docs/research/pyscf_quantum_chemistry_visualization_research_2026-07-19.md`
- Modify: `docs/research/literature_review_cross_cutting_findings_2026-07-19.md`
- Modify: `docs/survey/quantum_chemistry_software_survey_draft_2026-07-19.md`
- Conditional Modify: `data/pyscf_quantum_chemistry_visualization_research_2026-07-19.xlsx`

**Interfaces:**
- Consumes: Zotero 父分类 `IMD6DTWK` 的 61 个条目、每篇唯一阅读笔记、七个主题子分类和 2026-07-20 BibTeX。
- Produces: 61 个单一主归类证据笔记、带逐事实内链的研究文档，以及与证据一致的问卷。

- [ ] **Step 1: 验证 BibTeX**

按花括号层级解析条目，检查 61 篇、key/DOI 唯一、标题和 DOI 必填、摘要可用性，并按 DOI 与 Zotero 父分类双向核对。

- [ ] **Step 2: 导出 Zotero 原笔记**

通过 Zotero MCP 读取每个 regular item 的唯一 child note、元数据和主题成员关系；原始导出只写入被忽略的 `tmp/`。

- [ ] **Step 3: 生成主题化 Markdown**

按 DOI 连接 BibTeX 与 Zotero；为 53 篇单主题文献直接归类，为 8 篇跨主题文献选择一个主主题。文件顶部写 BibTeX key 与摘要，正文保留 Zotero 返回的原始 HTML。

- [ ] **Step 4: 补强研究文档和问卷**

只加入能由笔记回答区、页码、图表或总结支持的事实，并就近链接证据笔记。综合判断显式标为推论；删除或改写无证据的断言和诱导性问卷选项。

- [ ] **Step 5: 同步与验证**

检查 61 个 BibTeX key、DOI 和 Markdown 一一对应，Markdown 内链可解析，题号连续、变量唯一、分支目标存在、预计 12–15 分钟、工作簿（若修改）无公式错误且预览可读，并执行公开文件隐私扫描。

- [ ] **Step 6: 保持未提交**

不暂存、不提交、不推送；向用户展示修改后的文件与验证结果。
