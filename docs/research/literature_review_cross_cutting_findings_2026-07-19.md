# 跨主题文献精读关键发现

更新日期：2026-07-20
证据范围：Zotero“PySCF × ChemBlender 量子化学可视化调研（2026-07-19）”父分类中的 61 篇全文阅读笔记。全部笔记见[文献总结引用索引](../../data/literature-notes/README.md)。

## 一、证据边界

本文把内容分为两类：

- **文献事实**：论文报告的功能、方法、实验条件、结果或作者结论，直接链接到对应阅读笔记。
- **综合推论**：本项目基于多篇文献形成的问卷或产品判断；它不是任何单篇论文的原结论，并列出支撑笔记。

笔记中“对项目的启示”“建议题目”和“仍未解决的问题”本身不作为文献事实；只有回答区、页码/图表定位和论文总结可作为事实依据。

## 二、已有可视化先例与真实缺口

| 文献事实 | 证据笔记 |
| --- | --- |
| Digichem 把输入生成、作业执行、结果解析和三维渲染组织成统一工作流，并可生成轨道与密度图。 | [Lee 2024](../../data/literature-notes/04-interoperability-workflows/lee_digichem_2024.md) |
| Rhorix 把量子化学拓扑中的临界点、梯度路径和等值包络转换为 Blender 对象，同时讨论了异构拓扑输出带来的转换负担。 | [Mills 2017](../../data/literature-notes/05-data-formats-visualization/mills_rhorix_2017.md) |
| QMBlender 使用粒子表示三维量子波函数动力学，并把时间演化结果带入 Blender。 | [Figueiras 2019](../../data/literature-notes/05-data-formats-visualization/figueiras_qmblender_2019.md) |
| BlendMol 展示了 VMD/PyMOL 与 Blender 之间的大分子场景交换和交互模式。 | [Durrant 2019](../../data/literature-notes/05-data-formats-visualization/durrant_blendmol_2019.md) |
| Jang 等直接从高斯基函数表示评估电子密度、静电势、分子轨道和自旋密度，并使用体渲染、切片、裁剪和传递函数进行交互探索。 | [Jang 2009](../../data/literature-notes/05-data-formats-visualization/yun_jang_interactive_2009.md)、[Jang 2010](../../data/literature-notes/05-data-formats-visualization/jang_interactive_2010.md) |
| ORBKIT 可从多种程序输出读取波函数信息，并在用户指定网格上计算轨道、密度及导数。 | [Hermann 2016](../../data/literature-notes/05-data-formats-visualization/hermann_orbkit_2016.md) |
| Multiwfn 覆盖轨道、电子密度、静电势、ELF/LOL 和拓扑等多类实空间分析。 | [Lu 2012](../../data/literature-notes/05-data-formats-visualization/lu_multiwfn_2012.md) |

**综合推论：**“在 Blender 中显示轨道或密度”已有直接先例，单纯增加一种格式转换不足以构成清晰差异。更值得验证的需求是：是否需要在跨程序导入时保留计算协议和场语义、报告转换误差、处理周期边界与有符号轨道，并支持批处理和时间序列。该推论由上述先例以及[QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)、[QCDB/QCEngine](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md)和[VDB](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md)共同支撑。

## 三、格式、语义与互操作

| 文献事实 | 证据笔记 |
| --- | --- |
| QC-ML/Q5cost 将可读的语义描述与大型数组分别交给 XML 和 HDF5，并用 schema 约束数据结构。 | [Angeli 2007](../../data/literature-notes/05-data-formats-visualization/angeli_problem_2007.md) |
| QCArchive 将输入、输出、程序/执行来源和结果组织为可查询、可共享的计算记录。 | [Smith 2021, QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md) |
| QCDB/QCEngine 使用 QCSchema、验证、单位转换和程序适配器统一多程序任务，同时保留程序特定关键字。 | [Smith 2021, QCDB/QCEngine](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md) |
| cclib 通过解析不同程序的文本输出建立包无关的数据访问层；论文也显示程序和版本差异会给解析维护带来负担。 | [O'Boyle 2008](../../data/literature-notes/04-interoperability-workflows/oboyle_cclib_2008.md) |
| MDI 规定运行时命令、数据类型、顺序、长度和单位，用于进程间直接交换，而不是事后解析文件。 | [Barnes 2021](../../data/literature-notes/04-interoperability-workflows/barnes_molssi_2021.md) |
| QMflows 用通用关键字、程序适配器、依赖图、队列执行和结果恢复组织多程序并行工作流，并保留原生结果与可移植结果。 | [Zapata 2019](../../data/literature-notes/04-interoperability-workflows/zapata_qmflows_2019.md) |
| Basis Set Exchange 提供可版本化的基组数据和多程序格式输出；libxc 以统一接口集中实现交换-相关泛函。 | [Pritchard 2019](../../data/literature-notes/04-interoperability-workflows/pritchard_new_2019.md)、[Lehtola 2018](../../data/literature-notes/04-interoperability-workflows/lehtola_recent_2018.md) |

**综合推论：**引擎无关不等于“识别多个扩展名”。最小语义层至少应记录程序和版本、方法、基组/ECP、程序特定设置、单位、坐标/晶格、数组形状与顺序、场/态索引、原始文件哈希和适配器版本；大型体数据可以使用独立的 HDF5/OpenVDB 表示，但必须链接回原始计算记录。[证据：Angeli 2007](../../data/literature-notes/05-data-formats-visualization/angeli_problem_2007.md)、[QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)、[QCDB/QCEngine](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md)。

## 四、数值可信度不能由软件品牌替代

| 文献事实 | 证据笔记 |
| --- | --- |
| DFT 结果会受基组、数值积分网格、SCF 稳定性、几何和实验校正等细节影响；论文强调不能依赖模糊方法标签或未经检查的默认设置。 | [Morgante & Peverati 2020](../../data/literature-notes/03-numerical-reliability/morgante_devil_2020.md) |
| M06 系列反应能对积分网格敏感，粗网格可产生数 kcal/mol 量级误差。 | [Wheeler & Houk 2010](../../data/literature-notes/03-numerical-reliability/wheeler_integration_2010.md) |
| SG-2/SG-3 提供公开定义的标准剪枝网格，便于跨程序复现积分设置。 | [Dasgupta & Herbert 2017](../../data/literature-notes/03-numerical-reliability/dasgupta_standard_2017.md) |
| 高质量 DFT 基准需要明确参考值层级、体系覆盖、几何、基组、统计指标和异常值处理。 | [Karton & Santra 2025](../../data/literature-notes/03-numerical-reliability/karton_good_2025.md) |
| 基组可复现性不仅依赖名称，还涉及收缩方式、元素覆盖、ECP 和数据版本。 | [Hill 2013](../../data/literature-notes/03-numerical-reliability/hill_gaussian_2013.md) |

**综合推论：**可视化管线不能修复上游计算协议错误，但应避免引入新的格式、单位、符号和采样错误。项目验证应分为四层：格式解析、物理量检查、跨程序对照和派生可视化误差；问卷应分别测量用户的上游核验习惯与对转换验证的最低要求。[证据：Morgante 2020](../../data/literature-notes/03-numerical-reliability/morgante_devil_2020.md)、[Karton 2025](../../data/literature-notes/03-numerical-reliability/karton_good_2025.md)、[QCDB/QCEngine](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md)。

## 五、PySCF 的定位与可视化数据来源

| 文献事实 | 证据笔记 |
| --- | --- |
| PySCF 直接使用 Python 作为宿主语言，主要算法以 Python 实现、计算热点使用 C，并通过插件和可组合对象支持方法开发。 | [Sun 2018](../../data/literature-notes/01-pyscf-core/sun_p_2018.md) |
| PySCF 同时覆盖分子和周期体系，并形成量子化学、材料、机器学习和量子信息相关生态。 | [Sun 2020](../../data/literature-notes/01-pyscf-core/sun_recent_2020.md) |
| GPU4PySCF 把多类 HF/DFT 计算移植到 GPU；后续工作继续扩展 GPU 能力和应用范围。 | [Li 2025](../../data/literature-notes/01-pyscf-core/li_introducing_2025.md)、[Wu 2025](../../data/literature-notes/01-pyscf-core/wu_enhancing_2025.md) |
| GPU4PySCF 已用于周期 QM/MM 分子动力学和酶催化案例。 | [Li 2025, QM/MM](../../data/literature-notes/01-pyscf-core/li_accurate_2025.md) |
| PySCF 的可微实现覆盖分子和材料中的能量及导数工作流。 | [Zhang 2022](../../data/literature-notes/01-pyscf-core/zhang_differentiable_2022.md) |
| PyQMC 在 PySCF 生态中提供实空间量子蒙特卡洛工作流。 | [Wheeler 2023](../../data/literature-notes/01-pyscf-core/wheeler_pyqmc_2023.md) |
| 周期 RT-TDDFT 实现产生随时间演化的电子响应数据，属于多帧可视化场景。 | [Hanasaki 2023](../../data/literature-notes/01-pyscf-core/hanasaki_implementation_2023.md) |

**综合推论：**PySCF 适合作为首个深度 API 后端，因为它直接暴露 Python 数据对象并覆盖分子、周期、导数和时间相关场景；但问卷仍应先测量跨引擎需求，再把 PySCF 作为次级分层变量，避免把项目价值等同于单一软件采用率。[证据：Sun 2018](../../data/literature-notes/01-pyscf-core/sun_p_2018.md)、[Sun 2020](../../data/literature-notes/01-pyscf-core/sun_recent_2020.md)、[QCDB/QCEngine](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md)。

## 六、体数据与交互可视化的适用边界

| 文献事实 | 证据笔记 |
| --- | --- |
| VDB 使用层次稀疏数据结构支持高分辨率体数据、随机访问和动态拓扑。 | [Museth 2013](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md) |
| NeuralVDB 用神经表示进一步压缩稀疏体数据，但解码和误差特性与无损 VDB 不同。 | [Kim 2024](../../data/literature-notes/05-data-formats-visualization/kim_neuralvdb_2024.md) |
| 直接从基函数评估场可避免固定预采样网格的存储和分辨率限制，同时需要 GPU 计算与交互渲染支持。 | [Jang 2009](../../data/literature-notes/05-data-formats-visualization/yun_jang_interactive_2009.md)、[Jang 2010](../../data/literature-notes/05-data-formats-visualization/jang_interactive_2010.md) |
| 增强现实与云端/机器学习结合提供了低门槛交互式化学展示的另一种交付方式。 | [Sakshuwong 2022](../../data/literature-notes/05-data-formats-visualization/sakshuwong_bringing_2022.md)、[Raucci 2023](../../data/literature-notes/05-data-formats-visualization/raucci_interactive_2023.md) |

**综合推论：**OpenVDB 应作为适合稀疏大网格和多帧缓存的派生格式，而不是原始量子化学数据或完整计算记录的替代品；任何阈值化、重采样或有损压缩都应报告误差并保留回溯路径。[证据：VDB](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md)、[NeuralVDB](../../data/literature-notes/05-data-formats-visualization/kim_neuralvdb_2024.md)、[QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)。

## 七、采用门槛与软件可持续性

| 文献事实 | 证据笔记 |
| --- | --- |
| 交互式量子化学的普及同时受领域知识、编程、计算资源和界面/工作流复杂度影响。 | [Raucci 2023](../../data/literature-notes/05-data-formats-visualization/raucci_interactive_2023.md) |
| 自由开源软件允许检查、修改和再分发源码，也能支持教学中的本地运行和工作流自动化。 | [Lehtola & Karttunen 2022](../../data/literature-notes/06-software-sustainability/lehtola_free_2022.md) |
| 可持续计算化学软件需要稳定接口、测试、文档、互操作和可持续的人才/社区机制。 | [Di Felice et al. 2023](../../data/literature-notes/06-software-sustainability/di_felice_perspective_2023.md) |
| 面向更广泛用户的软件设计需要同时考虑可获得性、易用性、计算资源和长期维护。 | [Gagliardi et al. 2023](../../data/literature-notes/06-software-sustainability/gagliardi_sustainable_2023.md) |
| ORCA 的软件论文与未来展望体现了大型集成程序在方法覆盖、性能、用户接口和模块化演进之间的权衡。 | [Neese 2022](../../data/literature-notes/02-quantum-chemistry-software/neese_software_2022.md)、[Neese 2024](../../data/literature-notes/06-software-sustainability/neese_perspective_2024.md)、[Neese 2025](../../data/literature-notes/02-quantum-chemistry-software/neese_software_2025.md) |

**综合推论：**采用调查不能只问“是否喜欢某软件”，也不能默认受访者亲自计算。应区分计算执行、共同设置、合作解释、委托代算和结果使用，并分别测量成果交付、信任依据、协作痛点和采用障碍。[证据：Raucci 2023](../../data/literature-notes/05-data-formats-visualization/raucci_interactive_2023.md)、[QMflows](../../data/literature-notes/04-interoperability-workflows/zapata_qmflows_2019.md)、[AQME](../../data/literature-notes/04-interoperability-workflows/alegrerequena_aqme_2023.md)、[WebMO](../../data/literature-notes/04-interoperability-workflows/polik_span_2022.md)。

## 八、问卷构念与证据对应

| 问卷模块 | 由文献支持的测量内容 | 主要证据 |
| --- | --- | --- |
| A 背景与参与 | 工作身份、研究场景以及计算执行、合作、委托和结果使用角色 | [QMflows](../../data/literature-notes/04-interoperability-workflows/zapata_qmflows_2019.md)、[AQME](../../data/literature-notes/04-interoperability-workflows/alegrerequena_aqme_2023.md)、[WebMO](../../data/literature-notes/04-interoperability-workflows/polik_span_2022.md) |
| B 协作与交付 | 收到的成果、协作痛点、来源信息和信任依据 | [Morgante 2020](../../data/literature-notes/03-numerical-reliability/morgante_devil_2020.md)、[Karton 2025](../../data/literature-notes/03-numerical-reliability/karton_good_2025.md)、[QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md) |
| C 场与可视化 | 场类型、交付形态、技术子样本格式、可视化痛点与风险 | [ORBKIT](../../data/literature-notes/05-data-formats-visualization/hermann_orbkit_2016.md)、[Multiwfn](../../data/literature-notes/05-data-formats-visualization/lu_multiwfn_2012.md)、[Jang 2010](../../data/literature-notes/05-data-formats-visualization/jang_interactive_2010.md) |
| D 概念测试 | 概念价值、功能优先级、证据包、交付方式和采用障碍 | [QCDB/QCEngine](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md)、[QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)、[VDB](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md) |
| E 次级变量与反馈 | PySCF 认知和亲自计算、合作或委托经历中的开放案例 | [Sun 2018](../../data/literature-notes/01-pyscf-core/sun_p_2018.md)、[Sun 2020](../../data/literature-notes/01-pyscf-core/sun_recent_2020.md)、[GPU4PySCF](../../data/literature-notes/01-pyscf-core/li_introducing_2025.md) |

## 九、仍需由用户研究回答的问题

以下内容是研究问题，不是现有文献已经证明的事实：

1. 不同参与角色如何接收计算成果，技术子样本实际处理哪些场文件格式？
2. 直接波函数评估、预采样网格、等值面和体渲染各自有多大真实使用频率？
3. 用户愿意为哪些元数据和验证报告增加操作成本？
4. 大网格、多帧和周期体系是否足以形成 OpenVDB 工作流的优先需求？
5. GUI、Python API、CLI、Blender 插件和 HPC 批处理分别对应哪些用户群？
6. PySCF 认知以及亲自计算、合作或委托角色是否会改变对引擎无关工作流的评价？

这些问题应由预测试、正式问卷和后续访谈回答，不能从软件论文的功能描述直接推出。

## 十、文献覆盖

单一主归类后共有 61 篇：PySCF 核心与生态 8 篇、量子化学软件与采用 20 篇、数值可靠性与跨引擎基准 5 篇、互操作/自动化/工作流 11 篇、数据格式与科学可视化 13 篇、科研软件工程与可持续性 4 篇。原 Zotero “用户研究、信任与维护”子分类与其他主题完全交叉，因此作为横向分析维度使用，不复制文件。逐篇标题、DOI 与 BibTeX key 见[文献总结引用索引](../../data/literature-notes/README.md)。
