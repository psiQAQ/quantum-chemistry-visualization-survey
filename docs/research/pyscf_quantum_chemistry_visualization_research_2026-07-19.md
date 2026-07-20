# PySCF × ChemBlender 量子化学可视化方向深度调研

检索与整理日期：2026-07-19；阅读笔记证据链接更新：2026-07-20

## 执行摘要

该方向值得继续投入，但建议立即把项目从“PySCF专用 `.cube` → OpenVDB 转换器”升级为：

> 一个引擎无关、科学语义完整、可验证、可复现，并适合大规模稀疏体数据和时间序列的量子化学计算到Blender场景工作流。

静态场显示和 Blender 科研可视化已有直接先例：[Rhorix](../../data/literature-notes/05-data-formats-visualization/mills_rhorix_2017.md)、[QMBlender](../../data/literature-notes/05-data-formats-visualization/figueiras_qmblender_2019.md)、[BlendMol](../../data/literature-notes/05-data-formats-visualization/durrant_blendmol_2019.md)和[Digichem](../../data/literature-notes/04-interoperability-workflows/lee_digichem_2024.md)。因此，本项目把“完整保存计算协议、正确处理单位与网格、量化转换误差、支持多计算后端及可复现场景”作为待问卷验证的差异化假设，而不是已被文献证明的市场结论。

PySCF 直接使用 Python 作为宿主语言，以可组合对象和插件支持方法开发，并同时覆盖分子与周期体系；其方法范围和生态由 2018 年框架论文与 2020 年更新论文系统说明。[Sun 2018](../../data/literature-notes/01-pyscf-core/sun_p_2018.md)、[Sun 2020](../../data/literature-notes/01-pyscf-core/sun_recent_2020.md)。GPU4PySCF、可微 PySCF、PyQMC、周期 RT-TDDFT 和周期 QM/MM 提供了后续同行评议案例。[Li 2025](../../data/literature-notes/01-pyscf-core/li_introducing_2025.md)、[Zhang 2022](../../data/literature-notes/01-pyscf-core/zhang_differentiable_2022.md)、[Wheeler 2023](../../data/literature-notes/01-pyscf-core/wheeler_pyqmc_2023.md)、[Hanasaki 2023](../../data/literature-notes/01-pyscf-core/hanasaki_implementation_2023.md)、[Li 2025, QM/MM](../../data/literature-notes/01-pyscf-core/li_accurate_2025.md)。这些证据支持把 PySCF 作为首个深度后端，但不能单独证明其全球行业采用率。

### ChemBlender 2.1 源码能力边界

对 ChemBlender `snapshot/20260707-current-state` 分支首个独有提交 `78c2d8d` 的只读核查显示，2.1.0 已有能力集中在分子/晶体结构导入与编辑、Geometry Nodes 表示、晶胞/超胞、截面、配位多面体、热椭球、对称操作、快速渲染和结构文件导出。源码未发现 Gaussian Cube、体数据、等值面或 OpenVDB 导入实现；`CH_Atomic Orbital` 是 Geometry Nodes 预设，不能据此认定已经支持计算所得轨道场。[核查范围与问卷设计含义](../superpowers/specs/2026-07-20-reduce-survey-option-fatigue-design.md)

因此，问卷不重复细问已有结构建模功能，而把开发重点放在场文件导入与结构对齐、场与现有分子/晶体及周期工具联动、科学语义、性能、动画、复现和交付方式。

### 本轮 Web of Science 补充结果

本轮使用 Web of Science Starter API 执行 6 个基础查询，并因控制台输出截断对其中 4 个查询做紧凑回读，共消耗 10 次调用，未超过项目设置的 12 次硬上限。筛选重点是量子化学体数据可视化、波函数后处理、公共数据格式和跨程序工作流；最终新增 8 条 DOI 文献。

新增证据修正了两个范围判断：第一，用户需求不能只用“是否需要 `.cube`”表示，还应区分直接波函数表示、规则网格体数据、程序原生周期体数据和派生可视化格式；第二，引擎无关不仅是“能读取多个文件”，还包括标准化变量、计算协议、坐标/单位、任务接口和可追溯元数据。

### 全文精读证据综合

本轮对关键条目读取了可检索全文，而不是只依据题名或摘要。以下表格区分论文直接支持的事实与本项目据此形成的设计判断；后者是综合推论，不冒充原论文结论。

| 证据主题 | 全文直接支持的事实 | 对问卷与产品的设计含义 |
| --- | --- | --- |
| 交互式量子化学场 | [10.1109/tvcg.2009.158](../../data/literature-notes/05-data-formats-visualization/yun_jang_interactive_2009.md) 与 [10.1107/s010876731002636x](../../data/literature-notes/05-data-formats-visualization/jang_interactive_2010.md) 直接讨论从高斯基函数表示评估电子密度、静电势、轨道和自旋密度，并使用切片、传递函数、裁剪及体渲染；预采样规则网格会带来存储、传输和分辨率权衡 | 不能把需求简化为“是否打开 Cube”；需测量直接波函数评估、交互阈值/传递函数、裁剪、等值面与体渲染需求 |
| 波函数与实空间分析 | ORBKIT（[10.1002/jcc.24358](../../data/literature-notes/05-data-formats-visualization/hermann_orbkit_2016.md)）可读取多程序输出并在任意网格重建轨道、密度及导数；Multiwfn（[10.1002/jcc.22885](../../data/literature-notes/05-data-formats-visualization/lu_multiwfn_2012.md)）覆盖 ESP、ELF、密度、轨道和拓扑分析 | 格式题需同时覆盖 Molden、FCHK、WFN/WFX 等源表示与 Cube、OpenDX、XSF 等体网格；场类型不能只列 HOMO/LUMO |
| Blender 科研表达 | Rhorix（[10.1002/jcc.25054](../../data/literature-notes/05-data-formats-visualization/mills_rhorix_2017.md)）将临界点、梯度路径和等值包络映射为 Blender 对象，并指出异构拓扑输出造成转换负担 | Blender 是呈现层而非科学权威；需要保留拓扑语义、对象关系和来源元数据 |
| 稀疏动态体数据 | VDB（[10.1145/2487228.2487235](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md)）面向高分辨率、稀疏、动态拓扑和时间变化体数据，降低稠密包围网格的内存负担 | OpenVDB 适合作为大型、多帧场的派生格式；稀疏化、重采样与误差必须可报告，原始数据仍需保留 |
| 跨程序标准化 | QCDB/QCEngine（[10.1063/5.0059356](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md)）用 QCSchema JSON、验证、单位转换和程序适配器统一输入输出，同时允许程序特定关键字；公共格式论文（[10.1002/qua.21387](../../data/literature-notes/05-data-formats-visualization/angeli_problem_2007.md)）将语义描述与大型数组分别交给 XML/HDF5 | “引擎无关”应包含方法、关键字/默认值、程序版本、单位、数组形状/顺序、原生输入输出及来源关系，不能只看扩展名 |
| 数据可追溯与解析 | QCArchive（[10.1002/wcms.1491](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)）把完整输入、输出和程序/执行来源共同存档；cclib（[10.1002/jcc.20823](../../data/literature-notes/04-interoperability-workflows/oboyle_cclib_2008.md)）说明不同程序、版本和任务的文本输出差异会使解析器脆弱 | 派生场应链接到不可变计算记录；解析层需报告适配器版本、支持字段、警告和失败，且保留原始输出 |
| 自动化与采用门槛 | MDI（[10.1016/j.cpc.2020.107688](../../data/literature-notes/04-interoperability-workflows/barnes_molssi_2021.md)）规定运行时接口的单位、数据类型、顺序和长度；AQME（[10.1002/wcms.1663](../../data/literature-notes/04-interoperability-workflows/alegrerequena_aqme_2023.md)）与 QMflows（[10.1021/acs.jcim.9b00384](../../data/literature-notes/04-interoperability-workflows/zapata_qmflows_2019.md)）覆盖输入准备、调度、结果收集和多程序工作流；交互量化综述（[10.1146/annurev-physchem-061020-053438](../../data/literature-notes/05-data-formats-visualization/raucci_interactive_2023.md)）明确指出领域知识、编程和硬件是进入门槛 | 需求调查必须同时询问 GUI、CLI/Python、HPC/云、批处理、教程和编程门槛；适配器应验证单位、形状、顺序并公开能力边界 |
| 数值可信度 | DFT 教程综述（[10.1002/qua.26332](../../data/literature-notes/03-numerical-reliability/morgante_devil_2020.md)）指出基组和积分网格常被忽略，默认网格可能不足且应在报告中注明；网格误差研究（[10.1021/ct900639j](../../data/literature-notes/03-numerical-reliability/wheeler_integration_2010.md)）报告 M06 系列反应能对粗网格敏感；SG-2/SG-3 论文（[10.1002/jcc.24761](../../data/literature-notes/03-numerical-reliability/dasgupta_standard_2017.md)）给出公开定义并经过验证的标准剪枝网格 | 可视化验证不能替代上游计算验证；元数据和跨程序对照必须记录网格、基组、版本、默认值覆盖与容差 |

这些全文证据共同支持一个边界：问卷可以测量用户是否重视某项功能，但不能凭偏好票数证明数值正确性；科学正确性仍需由公开 fixture、误差指标、元数据和跨代码回归测试建立。

## 一、是否值得深度探究

### 1. 值得继续的部分

1. Blender 已被用于承接量子化学拓扑、波函数动力学和科研场景表达。[Rhorix](../../data/literature-notes/05-data-formats-visualization/mills_rhorix_2017.md)、[QMBlender](../../data/literature-notes/05-data-formats-visualization/figueiras_qmblender_2019.md)、[BlendMol](../../data/literature-notes/05-data-formats-visualization/durrant_blendmol_2019.md)。
2. VDB 面向高分辨率稀疏体数据、随机访问和动态拓扑。[Museth 2013](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md)。
3. PySCF 允许通过 Python 对象和数组访问计算数据，适合与自动化后处理组合。[Sun 2018](../../data/literature-notes/01-pyscf-core/sun_p_2018.md)。
4. 多程序工作流需要输入准备、调度、解析、结果恢复和来源记录；现有框架以适配器和统一模型处理这些环节。[QMflows](../../data/literature-notes/04-interoperability-workflows/zapata_qmflows_2019.md)、[QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)、[cclib](../../data/literature-notes/04-interoperability-workflows/oboyle_cclib_2008.md)。
5. ORBKIT 与 Multiwfn 支持多类轨道、密度、导数和实空间分析，说明场类型不应只限于 HOMO/LUMO。[ORBKIT](../../data/literature-notes/05-data-formats-visualization/hermann_orbkit_2016.md)、[Multiwfn](../../data/literature-notes/05-data-formats-visualization/lu_multiwfn_2012.md)。

### 2. 不足以形成壁垒的部分

- 仅把Gaussian cube读入Blender；
- 仅把dense array写成OpenVDB；
- 仅生成HOMO/LUMO正负等值面；
- 仅提供“一键美化”但不保存科学元数据；
- 只支持PySCF，要求用户迁移既有Gaussian、ORCA、CP2K或VASP工作流。

Rhorix、QMBlender、BlendMol 和 Digichem 已证明 Blender 与科学/量子化学工作流结合的可行性。[Rhorix](../../data/literature-notes/05-data-formats-visualization/mills_rhorix_2017.md)、[QMBlender](../../data/literature-notes/05-data-formats-visualization/figueiras_qmblender_2019.md)、[BlendMol](../../data/literature-notes/05-data-formats-visualization/durrant_blendmol_2019.md)、[Digichem](../../data/literature-notes/04-interoperability-workflows/lee_digichem_2024.md)。因此，“科学完整性、可复现性、跨引擎互操作、大体数据性能和动态场”应作为需要用户研究验证的差异化假设。

## 二、PySCF是否被学界或行业认可

### 1. 可以明确支持的判断

PySCF已经是成熟的研究级开源电子结构平台，而不是未经验证的个人项目。主要证据包括：

- 2018 年框架论文与 2020 年大型更新论文系统说明架构、方法范围和生态。[Sun 2018](../../data/literature-notes/01-pyscf-core/sun_p_2018.md)、[Sun 2020](../../data/literature-notes/01-pyscf-core/sun_recent_2020.md)。
- PyQMC、实时 TDDFT、GPU4PySCF、可微计算和周期 QM/MM 均有同行评议论文。[Wheeler 2023](../../data/literature-notes/01-pyscf-core/wheeler_pyqmc_2023.md)、[Hanasaki 2023](../../data/literature-notes/01-pyscf-core/hanasaki_implementation_2023.md)、[Li 2025](../../data/literature-notes/01-pyscf-core/li_introducing_2025.md)、[Zhang 2022](../../data/literature-notes/01-pyscf-core/zhang_differentiable_2022.md)、[Li 2025, QM/MM](../../data/literature-notes/01-pyscf-core/li_accurate_2025.md)。
- GPU4PySCF 的后续论文报告了性能、成本和应用案例，但这些数字依赖硬件、方法、体系和版本。[Wu 2025](../../data/literature-notes/01-pyscf-core/wu_enhancing_2025.md)。

更准确的表述是：**PySCF在电子结构方法开发、开放科学、Python自动化、嵌入式工作流和部分高性能计算方向具有很强的学术认可和增长潜力。**

### 2. 不能过度推断的部分

- 核心论文引用量是学术影响代理，不是活跃用户数；
- GitHub星标、fork或PyPI下载量不能直接代表工业生产使用；
- GPU论文中的加速和成本数字依赖硬件、方法、体系和实现版本；
- 本轮没有找到具有全球代表性、同行评议且直接报告Gaussian、ORCA、PySCF、Q-Chem等市场份额的综合调查。

因此，后续问卷有实际研究价值，但必须采用分层抽样并公开渠道偏差。

## 三、如何科学证明PySCF的可信度与潜力

建议采用六维证据模型：

| 维度 | 应收集的证据 | 不应单独使用的代理 |
|---|---|---|
| 科学可信度 | 同行评议软件论文、独立跨代码基准、公开测试集复现 | 单个演示结果 |
| 数值可复现 | 固定版本、方法、基组/ECP、网格、收敛、哈希、回归测试 | 只写“B3LYP/某软件” |
| 工程成熟度 | 发布节奏、CI、测试覆盖、文档、错误诊断、长期维护 | GitHub星标 |
| 真实采用 | 过去12个月实际运行、团队标准、工业案例、作业记录 | 引用量或下载量单项 |
| 工作流适配 | Python/API、GPU/HPC、批处理、GUI、互操作 | 功能清单数量 |
| 支持与治理 | 许可证、商业支持、社区响应、合规、平台可用性 | 软件知名度 |

### 推荐的跨代码验证协议

1. 选取小分子、开壳层、过渡金属、弱相互作用、周期体系等分层测试集。
2. 固定几何、总电荷、自旋、基组/ECP、辅助基组、泛函与色散、溶剂、积分网格、冻结核、对称性、SCF初猜和收敛阈值。
3. 比较总能量、相对能、梯度、Hessian/频率、密度、ESP和轨道子空间。
4. 对近简并轨道比较子空间投影或主角，不要只按MO编号逐轨道比较。
5. 对性能只在同硬件、同精度、同并行设置下比较，并分别报告能量、梯度、Hessian和后处理。
6. 发布输入、版本、环境、原始输出、解析代码和容差。

公开文献已表明积分网格、基组、收敛与其他数值设置会影响结果，因此跨程序比较必须先统一协议；性能比较也应限定硬件、方法、体系和实现版本。[Morgante 2020](../../data/literature-notes/03-numerical-reliability/morgante_devil_2020.md)、[Wheeler 2010](../../data/literature-notes/03-numerical-reliability/wheeler_integration_2010.md)、[Karton 2025](../../data/literature-notes/03-numerical-reliability/karton_good_2025.md)。本报告不再依据零散 Issue 给程序做单一快慢或准确度排名。

## 四、PySCF 与其他程序的比较边界

PySCF 论文能够直接支持的定位是：Python 宿主语言、可组合对象、插件式方法开发，以及分子与周期体系的统一框架。[Sun 2018](../../data/literature-notes/01-pyscf-core/sun_p_2018.md)、[Sun 2020](../../data/literature-notes/01-pyscf-core/sun_recent_2020.md)。现有 61 篇笔记没有提供一项统一控制条件下的“PySCF 对 Gaussian”全面比较，因此本文不再列出未经同一证据体系核验的产品对照表，也不声称某一程序天然更准确。

问卷改为测量受访者过去 12 个月的实际程序使用、选择因素、工作流方式与交叉核验行为。这样得到的是样本中的采用和需求数据，而不是由软件功能清单推导的市场结论。

## 五、其他常见量子化学/电子结构程序

| 使用场景 | 建议重点关注的软件 |
|---|---|
| 通用分子与广泛方法覆盖 | [ORCA](../../data/literature-notes/02-quantum-chemistry-software/neese_software_2025.md)、[Q-Chem](../../data/literature-notes/02-quantum-chemistry-software/epifanovsky_software_2021.md) |
| 开源/Python与高通量 | [PySCF](../../data/literature-notes/01-pyscf-core/sun_recent_2020.md)、[Psi4](../../data/literature-notes/02-quantum-chemistry-software/smith_p_2020.md) |
| HPC 与多尺度体系 | [NWChem](../../data/literature-notes/02-quantum-chemistry-software/apra_nwchem_2020.md)、[CP2K](../../data/literature-notes/02-quantum-chemistry-software/kuhne_cp2k_2020.md) |
| 周期电子结构 | [Quantum ESPRESSO](../../data/literature-notes/02-quantum-chemistry-software/giannozzi_advanced_2017.md)、[FHI-aims](../../data/literature-notes/02-quantum-chemistry-software/blum_ab_2009.md) |
| 多参考与激发态 | [OpenMolcas](../../data/literature-notes/02-quantum-chemistry-software/aquilante_modern_2020.md)、[Molpro](../../data/literature-notes/02-quantum-chemistry-software/werner_molpro_2020.md)、[OpenQP](../../data/literature-notes/02-quantum-chemistry-software/mironov_openqp_2024.md) |
| 高阶耦合簇 | [CFOUR](../../data/literature-notes/02-quantum-chemistry-software/matthews_coupled-cluster_2020.md)、[Molpro](../../data/literature-notes/02-quantum-chemistry-software/werner_molpro_2020.md) |
| GPU 原生或 GPU 加速 | [TeraChem](../../data/literature-notes/02-quantum-chemistry-software/seritan_span_2021.md)、[GPU4PySCF](../../data/literature-notes/01-pyscf-core/li_introducing_2025.md) |
| 快速近似 | [GFN2-xTB](../../data/literature-notes/02-quantum-chemistry-software/bannwarth_gfn2-xtbaccurate_2019.md)、[DFTB+](../../data/literature-notes/02-quantum-chemistry-software/hourahine_dftb_2020.md) |

该表只概括各软件论文所说明的主要能力，不代表采用率排行，也不证明用户一定按此组合程序。新版问卷通过 Q03 参与角色、Q04 成果形式和 Q06 信任依据测量工作流需求，不再要求非计算受访者猜测具体程序使用情况。

## 六、建议的产品架构与路线

以下路线是基于文献的**项目建议**，不是论文已经验证的产品需求。

### 阶段1：科学完整性

- 建立`CalculationRecord`：引擎、版本/git hash、方法、基组/ECP、XC库、网格、收敛、环境与原始文件哈希。[QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)、[Karton 2025](../../data/literature-notes/03-numerical-reliability/karton_good_2025.md)。
- 建立`FieldRecord`：物理量类型、单位、有符号性、原点、三个轴向量、网格数、周期性、时间/态/轨道索引。
- 建立`GridTransform`：完整3×3仿射变换，而不是只保存三个体素尺寸。
- 区分无损 VDB 与阈值化、重采样或神经压缩的有损派生数据。[VDB](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md)、[NeuralVDB](../../data/literature-notes/05-data-formats-visualization/kim_neuralvdb_2024.md)。
- 自动生成机器可读验证报告。

### 阶段2：引擎无关

- PySCF采用深度API适配；
- Gaussian、ORCA、Psi4、Q-Chem先通过cube、Molden、fchk或cclib接入；
- CP2K、VASP、Quantum ESPRESSO加入周期晶格与大网格适配；
- 参考 [QCSchema/QCEngine](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md)、[QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)和[MolSSI Driver Interface](../../data/literature-notes/04-interoperability-workflows/barnes_molssi_2021.md)的分层思想。

#### 建议进入问卷的格式范围

| 类型 | 代表格式 | 在问卷中的定位 |
|---|---|---|
| 规则网格体数据 | Gaussian Cube（`.cube/.cub`）、OpenDX（`.dx`）、XSF/AXSF/BXSF | 直接测量过去12个月是否实际处理及首选支持格式 |
| 波函数/轨道数据 | Molden、FCHK、WFN/WFX | 允许工具在任意网格上重建轨道、密度及其导数 |
| 周期程序原生体数据 | VASP `CHGCAR/PARCHG/LOCPOT/ELFCAR` | 覆盖材料、表面、周期电荷密度和势场用户 |
| 通用科学体数据 | CCP4/MRC、VTK/VTI、自定义 NumPy/HDF5 网格 | 识别跨学科与自研工作流，不把选项锁定在单一程序 |
| 派生可视化格式 | OpenVDB | 明确标注为派生格式，不能替代原始输出和计算协议 |

问卷用一题记录过去12个月实际处理或能够从合作者/服务方获得的格式；后续通过功能优先级、开放案例和在线文档持续反馈确定首个适配器顺序，避免再增加一道高负担排序题。

### 阶段3：高级场与可复现场景

- 总密度、自旋密度、ESP、差分密度、NTO/跃迁密度、ELF/LOL、局域轨道；
- 正负轨道叶分离、统一等值面规则、分位数或积分占比阈值；
- 多帧场缓存、轨道相位对齐、结构与体场同步动画；
- 自动创建材质、色标、相机、灯光、单胞/超胞和方法图注；
- 原始计算数据与VDB派生数据分开归档。

### 阶段4：公开基准与用户研究

- 发布小型、可复现的跨代码基准；
- 公开cube/VDB fixture、容差和CI回归；
- 用分层问卷测量真实使用、信任依据与采用障碍；
- 访谈10–20位学术与工业研究者，按任务确定后端优先级。

## 七、`.cube` → OpenVDB → Blender必须验证的科学细节

1. 规则网格必须保留原点、三个轴向量和数组维度，不能只保存三个体素尺寸。[ORBKIT](../../data/literature-notes/05-data-formats-visualization/hermann_orbkit_2016.md)、[QC-ML](../../data/literature-notes/05-data-formats-visualization/angeli_problem_2007.md)。
2. 坐标、网格和物理量必须显式记录单位与数据语义。[QCDB/QCEngine](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md)、[MDI](../../data/literature-notes/04-interoperability-workflows/barnes_molssi_2021.md)。
3. 周期程序的晶格和体数据需要作为独立测试层，不应沿用仅面向分子的假设。[Quantum ESPRESSO](../../data/literature-notes/02-quantum-chemistry-software/giannozzi_advanced_2017.md)、[CP2K](../../data/literature-notes/02-quantum-chemistry-software/kuhne_cp2k_2020.md)。
4. 多帧波函数或密度必须保留时间/态索引，并对跨帧表示一致性单独验证。[QMBlender](../../data/literature-notes/05-data-formats-visualization/figueiras_qmblender_2019.md)、[周期 RT-TDDFT](../../data/literature-notes/01-pyscf-core/hanasaki_implementation_2023.md)。
5. 轨道、密度、静电势和自旋密度是不同物理量，转换器不能仅凭文件形状合并语义。[Jang 2009](../../data/literature-notes/05-data-formats-visualization/yun_jang_interactive_2009.md)、[ORBKIT](../../data/literature-notes/05-data-formats-visualization/hermann_orbkit_2016.md)。
6. 阈值化、重采样和神经压缩会改变派生数据，应报告适合该转换的误差指标。[VDB](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md)、[NeuralVDB](../../data/literature-notes/05-data-formats-visualization/kim_neuralvdb_2024.md)。
7. VDB 应作为派生可视化文件，不替代原始输出和计算协议。[VDB](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md)、[QCArchive](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)。
8. Blender 对象关系和呈现参数应进入可复现场景记录。[Rhorix](../../data/literature-notes/05-data-formats-visualization/mills_rhorix_2017.md)、[BlendMol](../../data/literature-notes/05-data-formats-visualization/durrant_blendmol_2019.md)。

[软件与验证参考](software_and_validation_reference_2026-07-20.md)包含 20 项 P0/P1 测试，覆盖格式、单位、周期性、电子数积分、轨道符号、跨帧相位、无损采样、等值面、跨引擎能量/梯度/Hessian、ESP、元数据、性能和 CI 回归。

## 八、问卷设计原则

不要把主问题写成“PySCF是否被认可”。这种写法容易产生社会期许和框架偏差。建议顺序是：

1. 过去12个月实际运行过哪些程序；
2. 主程序和具体任务；
3. 选择软件时重视哪些因素；
4. 什么证据能够建立信任；
5. PySCF认知、实际使用和交叉验证行为；
6. 用户与非用户的不同障碍；
7. 在严格统一计算协议后是否愿意采用PySCF；
8. 当前三维场可视化工具、痛点和功能需求；
9. 对引擎无关、可验证、可复现场景工作流的价值判断。

抽样至少应分层：学术/工业、分子/材料、方法开发/应用研究、经验年限和地区。招募渠道必须记录；非概率样本的结论应表述为“本样本中的使用与态度”，不能直接表述为全球市场份额。

## 九、关于“reviewer讨论”

公开可获得的匿名审稿意见很少。本调研将三类材料分开使用：

- **同行评议综述与Perspective**：用于评价开放软件、可持续架构、数值细节和未来软件方向；
- **软件论文与方法论文**：用于确认功能、性能和应用；
- **公开维护者/用户Issue讨论**：用于呈现默认网格、准确度、兼容性、收敛和性能的实际争议。

GitHub Issue不是匿名同行评审，也不是受控基准，因此仅作为问题发现和案例证据，不与正式论文同级。

## 十、全文精读证据清单

1. **PySCF: the Python-based simulations of chemistry framework**（2018，WIREs Computational Molecular Science；DOI [10.1002/wcms.1340](../../data/literature-notes/01-pyscf-core/sun_p_2018.md)）
   证据用途：确认PySCF的设计定位、Python原生接口、方法覆盖与扩展性
   对项目的含义：PySCF应被视为研究级电子结构框架，而非单纯命令行程序。

2. **Recent developments in the PySCF program package**（2020，The Journal of Chemical Physics；DOI [10.1063/5.0006074](../../data/literature-notes/01-pyscf-core/sun_recent_2020.md)）
   证据用途：确认分子、周期体系、多参考、耦合簇、量子嵌入等能力
   对项目的含义：这是评估PySCF学术认可度和功能边界的首要引用。

3. **Introducing GPU Acceleration into the Python-Based Simulations of Chemistry Framework**（2025，The Journal of Physical Chemistry A；DOI [10.1021/acs.jpca.4c05876](../../data/literature-notes/01-pyscf-core/li_introducing_2025.md)）
   证据用途：验证PySCF生态的GPU加速路线与相对性能
   对项目的含义：可把交互可视化前处理与GPU计算整合为同一Python工作流。

4. **Enhancing GPU-Acceleration in the Python-Based Simulations of Chemistry Frameworks**（2025，WIREs Computational Molecular Science；DOI [10.1002/wcms.70008](../../data/literature-notes/01-pyscf-core/wu_enhancing_2025.md)）
   证据用途：提供工业利益相关方对GPU4PySCF的采用、成本与工作流证据
   对项目的含义：这是PySCF工业潜力最直接的同行评议证据之一。

5. **Accurate QM/MM Molecular Dynamics for Periodic Systems in GPU4PySCF with Applications to Enzyme Catalysis**（2025，Journal of Chemical Theory and Computation；DOI [10.1021/acs.jctc.4c01698](../../data/literature-notes/01-pyscf-core/li_accurate_2025.md)）
   证据用途：验证周期QM/MM、能量守恒和实际催化应用
   对项目的含义：说明PySCF不仅适合单点能，也可承载复杂、可复现的动力学工作流。

6. **Differentiable quantum chemistry with PySCF for molecules and materials at the mean-field level and beyond**（2022，The Journal of Chemical Physics；DOI [10.1063/5.0118200](../../data/literature-notes/01-pyscf-core/zhang_differentiable_2022.md)）
   证据用途：证明PySCF适合方法开发、自动微分和分子/材料统一工作流
   对项目的含义：为未来交互式参数扫描、逆向设计和可视化敏感性分析提供方向。

7. **Free and open source software for computational chemistry education**（2022，WIREs Computational Molecular Science；DOI [10.1002/wcms.1610](../../data/literature-notes/06-software-sustainability/lehtola_free_2022.md)）
   证据用途：独立讨论FOSS、可检查源码、可修改性、教学与学术/工业使用
   对项目的含义：支持PySCF在透明性和方法开发方面的优势，但不构成市场占有率证明。

8. **A Perspective on Sustainable Computational Chemistry Software Development and Integration**（2023，Journal of Chemical Theory and Computation；DOI [10.1021/acs.jctc.3c00419](../../data/literature-notes/06-software-sustainability/di_felice_perspective_2023.md)）
   证据用途：软件可持续性、模块化、稳定API、测试、文档和标准化
   对项目的含义：项目竞争力应来自可持续且引擎无关的工作流，而非绑定单一后端。

9. **A perspective on the future of quantum chemical software: the example of the ORCA program package**（2024，Faraday Discussions；DOI [10.1039/d4fd00056k](../../data/literature-notes/06-software-sustainability/neese_perspective_2024.md)）
   证据用途：从大型成熟程序视角讨论未来软件架构、算法与用户需求
   对项目的含义：有助于避免把“Python框架”和“集成程序包”放在同一维度粗暴排名。阅读依据见[笔记](../../data/literature-notes/06-software-sustainability/neese_perspective_2024.md)。

10. **The devil in the details: A tutorial review on some underestimated aspects of density functional theory calculations**（2020，International Journal of Quantum Chemistry；DOI [10.1002/qua.26332](../../data/literature-notes/03-numerical-reliability/morgante_devil_2020.md)）
   证据用途：说明基组、积分网格、收敛和默认设置可显著影响结果
   对项目的含义：不能用软件品牌替代计算协议；问卷应询问默认设置与验证习惯。

11. **Good Practices in Database Generation for Benchmarking Density Functional Theory**（2025，WIREs Computational Molecular Science；DOI [10.1002/wcms.1737](../../data/literature-notes/03-numerical-reliability/karton_good_2025.md)）
   证据用途：规范跨程序、跨方法基准数据的构造与报告
   对项目的含义：为PySCF与Gaussian/ORCA对照验证矩阵提供方法学基础。

12. **Gaussian 16, Revision C.01**（2016，Gaussian, Inc.；来源：https://gaussian.com/citation/）
   证据用途：Gaussian的标准软件引用与版本标识
   对项目的含义：Gaussian是成熟集成程序包；它不是与PySCF完全同类的Python库。

13. **Software update: The ORCA program system—Version 5.0**（2022，WIREs Computational Molecular Science；DOI [10.1002/wcms.1606](../../data/literature-notes/02-quantum-chemistry-software/neese_software_2022.md)）
   证据用途：评估ORCA的广泛方法覆盖、易用性和用户采用证据
   对项目的含义：ORCA是问卷中必须列出的主要对照程序。

14. **Psi4 1.4: Open-source software for high-throughput quantum chemistry**（2020，The Journal of Chemical Physics；DOI [10.1063/5.0006002](../../data/literature-notes/02-quantum-chemistry-software/smith_p_2020.md)）
   证据用途：PySCF在Python、高通量、开放生态方向的重要对照
   对项目的含义：引擎抽象层至少应考虑PySCF/Psi4的Python式工作流。

15. **Software for the frontiers of quantum chemistry: An overview of developments in the Q-Chem 5 package**（2021，The Journal of Chemical Physics；DOI [10.1063/5.0055522](../../data/literature-notes/02-quantum-chemistry-software/epifanovsky_software_2021.md)）
   证据用途：商业/团队式软件开发、方法覆盖和社区规模对照
   对项目的含义：Q-Chem代表研究前沿与商业支持并存的另一种模式。

16. **NWChem: Past, present, and future**（2020，The Journal of Chemical Physics；DOI [10.1063/5.0004997](../../data/literature-notes/02-quantum-chemistry-software/apra_nwchem_2020.md)）
   证据用途：HPC、并行和分子/凝聚相多方法平台对照
   对项目的含义：大型体系和集群用户的需求可能不同于桌面Blender工作流。

17. **CP2K: An electronic structure and molecular dynamics software package—Quickstep: Efficient and accurate electronic structure calculations**（2020，The Journal of Chemical Physics；DOI [10.1063/5.0007045](../../data/literature-notes/02-quantum-chemistry-software/kuhne_cp2k_2020.md)）
   证据用途：周期、液体、AIMD、GPW方法和HPC对照
   对项目的含义：若扩展周期场和时间序列，CP2K是高优先级输入后端。

18. **Modern quantum chemistry with [Open]Molcas**（2020，The Journal of Chemical Physics；DOI [10.1063/5.0004835](../../data/literature-notes/02-quantum-chemistry-software/aquilante_modern_2020.md)）
   证据用途：多参考、光化学、磁性和强相关体系对照
   对项目的含义：NTO、态密度、跃迁密度等高级可视化应兼容多参考程序输出。

19. **QCArchive: An open-source platform to compute, organize, and share quantum chemistry data**（2021，WIREs Computational Molecular Science；DOI [10.1002/wcms.1491](../../data/literature-notes/04-interoperability-workflows/smith_span_2021.md)）
   证据用途：标准数据模型、任务编排、结果存档与共享
   对项目的含义：项目应把VDB视为派生展示物，原始计算和元数据需独立归档。

20. **cclib: A library for package-independent computational chemistry algorithms**（2008，Journal of Computational Chemistry；DOI [10.1002/jcc.20823](../../data/literature-notes/04-interoperability-workflows/oboyle_cclib_2008.md)）
   证据用途：跨量子化学程序输出解析与统一内部表示
   对项目的含义：是多引擎导入层的直接先例；可避免只支持PySCF。

21. **Multiwfn: A multifunctional wavefunction analyzer**（2012，Journal of Computational Chemistry；DOI [10.1002/jcc.22885](../../data/literature-notes/05-data-formats-visualization/lu_multiwfn_2012.md)）
   证据用途：电子密度、ESP、ELF、拓扑等实空间分析的事实标准之一
   对项目的含义：后续产品场类型应超越HOMO/LUMO，覆盖差分密度、ESP、ELF/LOL、NTO等。

22. **VDB: High-resolution sparse volumes with dynamic topology**（2013，ACM Transactions on Graphics；DOI [10.1145/2487228.2487235](../../data/literature-notes/05-data-formats-visualization/museth_vdb_2013.md)）
   证据用途：OpenVDB的层次稀疏体表示、快速访问和动态拓扑基础
   对项目的含义：VDB适合高分辨率稀疏场和动画，但不应替代原始科学数据归档。

23. **Rhorix: An interface between quantum chemical topology and Blender**（2017，Journal of Computational Chemistry；DOI [10.1002/jcc.25054](../../data/literature-notes/05-data-formats-visualization/mills_rhorix_2017.md)）
   证据用途：量子化学拓扑结果到Blender的直接先例
   对项目的含义：证明方向有科研需求，也说明必须强调可追溯元数据和分析语义。

### Web of Science 新增精读条目

24. **Interactive Volume Rendering of Functional Representations in Quantum Chemistry**（2009，IEEE TVCG；DOI [10.1109/tvcg.2009.158](../../data/literature-notes/05-data-formats-visualization/yun_jang_interactive_2009.md)）——直接从基函数表示交互评估和体渲染量子化学场，提示不能把需求限定为预采样 `.cube`。
25. **Interactive Visualization of Quantum-Chemistry Data**（2010，Acta Crystallographica A；DOI [10.1107/s010876731002636x](../../data/literature-notes/05-data-formats-visualization/jang_interactive_2010.md)）——讨论传递函数、裁剪和无需重采样的交互探索。
26. **Interactive Quantum Chemistry Enabled by Machine Learning, GPUs, and Cloud Computing**（2023，Annual Review of Physical Chemistry；DOI [10.1146/annurev-physchem-061020-053438](../../data/literature-notes/05-data-formats-visualization/raucci_interactive_2023.md)）——从综述层面连接实时计算、云端、交互界面和扩展现实。
27. **Bringing Chemical Structures to Life with Augmented Reality, Machine Learning, and Quantum Chemistry**（2022，JCP；DOI [10.1063/5.0090482](../../data/literature-notes/05-data-formats-visualization/sakshuwong_bringing_2022.md)）——说明低门槛沉浸式可视化是一类交付形态。
28. **ORBKIT**（2016，Journal of Computational Chemistry；DOI [10.1002/jcc.24358](../../data/literature-notes/05-data-formats-visualization/hermann_orbkit_2016.md)）——从多种程序输出读取波函数，在任意网格计算密度、轨道及导数，并输出多种可视化格式。
29. **QCDB and QCEngine**（2021，JCP；DOI [10.1063/5.0059356](../../data/literature-notes/04-interoperability-workflows/smith_quantum_2021.md)）——公共 API 和标准变量可降低多程序输入差异，支撑引擎无关工作流。
30. **The Problem of Interoperability: A Common Data Format for Quantum Chemistry Codes**（2007，IJQC；DOI [10.1002/qua.21387](../../data/literature-notes/05-data-formats-visualization/angeli_problem_2007.md)）——把可读语义和大型二进制数据分别交给 XML 与 HDF5，说明格式设计不只是扩展名兼容。
31. **QMflows**（2019，JCIM；DOI [10.1021/acs.jcim.9b00384](../../data/literature-notes/04-interoperability-workflows/zapata_qmflows_2019.md)）——展示多程序标准接口、Python 工作流与并行执行的组合。

上述新增条目已写入 Zotero、BibTeX 和 [Markdown 证据目录](../../data/research-evidence-catalog-2026-07-20.md)。61 篇条目当前均有一条完成回填的全文阅读笔记；格式、交互可视化、互操作、数值验证和采用门槛相关事实均可从本地笔记链接回查。逐篇索引见[文献总结引用](../../data/literature-notes/README.md)。

## 十一、交付物说明

- Markdown 证据目录：72 条文献/官方资料；
- 软件与验证参考：18 个软件条目、20 项验证测试；
- BibTeX：`pyscf_quantum_chemistry_literature_2026-07-20.bib` 含 61 条唯一 key 和唯一 DOI；16 条缺摘要但标题与 DOI 完整；
- 文献总结引用：61 个单一主归类 Markdown，正文保留 Zotero 原始笔记格式，顶部使用 2026-07-20 BibTeX key 和可得摘要；
- 问卷Markdown：6–9分钟文献支撑预测试版，覆盖亲自计算、合作、委托代算和结果使用者；17 道封闭题共 118 个选项，仅 Q08、Q10、Q11 和 Q13 保留较细粒度；
- 本报告：用于制定路线图、访谈提纲和研究方案。

正式投稿或公开发布前，所有标记“以DOI元数据为准”或使用“et al.”的记录应通过Zotero、Crossref或出版社页面刷新完整元数据。
