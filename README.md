# PySCF × ChemBlender 量子化学可视化调研

本仓库整理 PySCF、主流量子化学软件、量子化学场/波函数文件、OpenVDB / Blender 可视化工作流，以及软件采用度问卷设计的阶段性调研资料。检索日期为 **2026-07-19**，全文笔记与问卷依据更新至 **2026-07-20**。

> 核心结论：该方向值得继续研究，但项目不宜只定位为“PySCF 专用 `.cube` → OpenVDB 转换器”。更有价值的方向是构建一个引擎无关、物理语义完整、可验证、可复现，并适合大规模稀疏体数据与时间序列的量子化学计算到 Blender 场景工作流。

## 资料概览

| 内容 | 数量 | 说明 |
| --- | ---: | --- |
| 文献与证据条目 | 72 | 同行评议论文、官方资料、公开技术讨论及项目资料 |
| A 级优先证据 | 31 | 直接支撑核心判断或问卷构念的优先精读资料 |
| 全文阅读笔记 | 61 | 与 2026-07-20 BibTeX 及 Zotero 父分类按 DOI 一一对应 |
| 软件矩阵条目 | 18 | 常见量子化学或电子结构软件的定位与适配比较 |
| 问卷题目 | 18 | 预计完成时间 6–9 分钟；覆盖亲自计算、合作、委托代算和结果使用者 |
| 验证测试 | 20 | 覆盖格式、数值、跨引擎、可视化和回归验证 |

### 证据主题分布

| 主题 | 条目数 |
| --- | ---: |
| PySCF 核心与生态 | 8 |
| 科研软件工程与可持续性 | 4 |
| 数值可靠性与基准 | 5 |
| 主流量子化学软件 | 20 |
| 互操作与工作流 | 11 |
| 数据格式与可视化 | 13 |

> 以上是 61 篇阅读笔记的单一主归类。Zotero 中“用户研究、信任与维护”与其他主题交叉，作为横向分析维度使用，不复制文件。

## 文档导航

### 研究结论

| 文档 | 内容 | 建议用途 |
| --- | --- | --- |
| [量子化学可视化方向深度调研](docs/research/pyscf_quantum_chemistry_visualization_research_2026-07-19.md) | 结论、证据边界、产品方向和验证协议 | 首先阅读，确认项目定位与路线 |
| [跨主题文献精读关键发现](docs/research/literature_review_cross_cutting_findings_2026-07-19.md) | 61 篇文献的事实、综合推论、证据链接和问卷构念映射 | 查看全文精读后的横向综合结果 |

### 问卷

| 文档 | 内容 | 建议用途 |
| --- | --- | --- |
| [量子化学结果交付与三维场可视化需求调查](docs/survey/quantum_chemistry_software_survey_draft_2026-07-19.md) | 18 道文献支撑的预测试题目，按参与角色分流，明确单选和多选 | 认知访谈、预测试和正式投放前校准 |

### 数据与参考文献

| 文件 | 内容 | 建议用途 |
| --- | --- | --- |
| [研究证据目录](data/research-evidence-catalog-2026-07-20.md) | 72 条核心文献、官方资料和公开证据的 Markdown 目录 | 按优先级、主题和证据用途筛选资料 |
| [软件与验证参考](docs/research/software_and_validation_reference_2026-07-20.md) | 18 个软件条目和 20 项验证测试的 Markdown 转写 | 比较软件定位并追踪验证任务 |
| [文献总结引用索引](data/literature-notes/README.md) | 按六个主主题列出 61 篇原始 Zotero 阅读笔记 | 从研究结论回查逐篇回答、页码和图表依据 |
| [当前 BibTeX 文献库](data/pyscf_quantum_chemistry_literature_2026-07-20.bib) | 61 个唯一 key、61 个唯一 DOI；45 条含摘要 | 与阅读笔记对应或用于引用管理 |
| [前一版 BibTeX 文献库](data/pyscf_quantum_chemistry_literature_2026-07-19.bib) | 61 条紧凑导入记录 | 保留旧 key 对照，不再作为笔记命名源 |

### 项目方法与维护

| 文档 | 内容 | 建议用途 |
| --- | --- | --- |
| [WoS、Zotero 与问卷调研设计](docs/project/2026-07-19-wos-zotero-survey-design.md) | 检索、筛选、隐私和全文门槛设计 | 了解研究方法和范围控制 |
| [WoS、Zotero 与问卷执行计划](docs/project/2026-07-19-wos-zotero-survey.md) | 分阶段执行步骤与验证要求 | 追溯本轮调研工作流 |
| [问卷受众扩展设计](docs/superpowers/specs/2026-07-20-broaden-survey-audience-design.md) | 18 题角色分流方案、保留项和分析口径 | 了解本轮问卷压缩依据 |
| [问卷受众扩展实施计划](docs/superpowers/plans/2026-07-20-broaden-survey-audience.md) | Markdown 改版、旧工作簿退役和验证步骤 | 追溯本轮改版过程 |
| [项目协作规则](AGENTS.md) | 隐私、文献同步、全文门槛和验证约束 | 后续维护与协作时遵循 |

### 附属文件

- `tmp/`：本地全文提取、Zotero 原始导出和其他可能含隐私的工作材料；该目录已被 Git 忽略，不属于公开交付物。

## 建议阅读顺序

1. 阅读[深度调研报告](docs/research/pyscf_quantum_chemistry_visualization_research_2026-07-19.md)，确认方向、证据边界和产品路线。
2. 阅读[跨主题精读发现](docs/research/literature_review_cross_cutting_findings_2026-07-19.md)，区分论文事实与项目综合推论。
3. 通过[文献总结引用索引](data/literature-notes/README.md)回查每项结论对应的 Zotero 阅读笔记。
4. 在[研究证据目录](data/research-evidence-catalog-2026-07-20.md)中按优先级、证据等级和主题筛选资料，并通过[软件与验证参考](docs/research/software_and_validation_reference_2026-07-20.md)查看软件矩阵和测试清单。
5. 使用[问卷预测试版](docs/survey/quantum_chemistry_software_survey_draft_2026-07-19.md)进行 10–15 人认知访谈。

## 主要判断

- **PySCF 适合作为首个深度集成后端。** 其 Python 宿主语言、可组合对象、插件式开发和分子—周期覆盖由[框架论文](data/literature-notes/01-pyscf-core/sun_p_2018.md)与[生态更新论文](data/literature-notes/01-pyscf-core/sun_recent_2020.md)直接说明；“首个后端”是本项目据此作出的工程选择。
- **现有证据不支持给 PySCF 与 Gaussian 做单一准确度或采用率排名。** 数值结果依赖方法、基组、积分网格和收敛等协议细节。[DFT 数值细节笔记](data/literature-notes/03-numerical-reliability/morgante_devil_2020.md)、[基准实践笔记](data/literature-notes/03-numerical-reliability/karton_good_2025.md)。
- **Blender 量子化学/科学可视化已有直接先例。** Rhorix、QMBlender、BlendMol 和 Digichem 分别覆盖拓扑对象、动态波函数、大分子场景和集成渲染工作流。[Rhorix](data/literature-notes/05-data-formats-visualization/mills_rhorix_2017.md)、[QMBlender](data/literature-notes/05-data-formats-visualization/figueiras_qmblender_2019.md)、[BlendMol](data/literature-notes/05-data-formats-visualization/durrant_blendmol_2019.md)、[Digichem](data/literature-notes/04-interoperability-workflows/lee_digichem_2024.md)。因此，科学元数据、误差量化和跨后端可追溯性是待问卷验证的差异化假设。
- **格式需求必须独立于计算库测量。** ORBKIT 和 Multiwfn 表明源波函数与实空间分析不能只用 Cube 表示；VDB 适合稀疏体数据但属于派生表示。[ORBKIT](data/literature-notes/05-data-formats-visualization/hermann_orbkit_2016.md)、[Multiwfn](data/literature-notes/05-data-formats-visualization/lu_multiwfn_2012.md)、[VDB](data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md)。
- **“行业认可度”需要组合证据。** 引用量、下载量或 GitHub 指标均不能单独代表软件的科学可信度或真实采用。

## “行业认可度”六维证据模型

| 维度 | 应收集的证据 | 不应单独使用的代理 |
| --- | --- | --- |
| 科学可信度 | 同行评议软件论文、独立跨代码基准、已知测试集复现 | 单次示例结果 |
| 数值可复现 | 固定版本、方法、基组/ECP、网格、收敛、哈希与回归测试 | 只写“B3LYP/某软件” |
| 工程成熟度 | 发布节奏、CI、测试、文档、错误诊断、长期维护 | GitHub 星标 |
| 真实采用 | 过去 12 个月实际使用、团队标准、工业案例、可审计作业记录 | 引用量或下载量单项 |
| 工作流适配 | Python/API、HPC/GPU、格式、批处理、GUI、互操作 | 功能清单数量 |
| 支持与治理 | 许可证、商业支持、社区响应、合规、平台可用性 | 知名度印象 |

## 验证重点

1. **格式验证**：检查 `.cube` 原点、三个轴向量、非正交晶胞、Bohr / Å 单位、周期端点和原子—体场对齐。
2. **物理验证**：检查电子密度积分、轨道正负符号、静电势单位和核附近处理。
3. **跨引擎验证**：在统一方法、基组、网格和收敛设置后，对比 PySCF、Gaussian、ORCA、Psi4 等程序的能量、梯度、Hessian、密度和轨道子空间。
4. **可视化验证**：量化 dense cube 与 VDB 的场误差、积分误差、等值面差异、文件体积、内存和 Blender 导入时间。

OpenVDB 应被视为派生可视化数据，不能替代原始 `.cube`、checkpoint、程序输出和计算协议。

## 数据口径与限制

- 本资料是 2026-07-19 检索、2026-07-20 完成笔记链接整理的阶段性结果，不是持续自动更新的数据库。
- Markdown 证据目录收录 72 条证据；BibTeX 与 Zotero 父分类均收录其中 61 条带 DOI 的文献，未覆盖部分官网、仓库和公开讨论。
- 2026-07-20 BibTeX 已通过 61 条目、key/DOI 唯一性、标题/DOI 必填检查；其中 16 条没有摘要。正式投稿或公开引用前，仍应通过 Zotero、Crossref 或出版社页面复核作者和出版字段。
- 项目方用户数、注册用户数和性能数据应按来源属性与实验条件解读，不能直接外推为全球市场份额。
- GitHub Issue 可用于发现真实工程问题，但不等同于匿名同行评审或受控基准。
- 问卷若使用便利抽样，结果只能解释为“本样本中的使用与态度”，不能代表全球量子化学软件市场。

## 当前状态

本仓库是**调研资料包**，不包含 `.cube` → OpenVDB 转换器、Blender 插件或可执行代码。关键可视化、格式互操作、软件采用门槛和数值验证文献已完成全文核验，18 题问卷已进入**文献支撑的预测试版**；正式投放前仍需用 10–15 人认知访谈覆盖亲自计算、合作、委托代算和结果使用者，检查角色分流、题目理解和实际完成时间。
