# ChemBlender 量子化学可视化：定向入门调研与开发学习计划

**检索截止：2026-09-20｜适用对象：具有物理/光学博士基础、能使用基础 Python、希望针对性开发 ChemBlender_2_x 的学习者。**

## 0. 结论与使用方式

建议走 **“物理量定义 → 基组与密度矩阵 → 数值场及误差 → 数据语义 → Blender 表示 → 可重复验证”** 的路线，不按量子化学专业完整课程重学一遍。

第一轮只准备一部英文主教材、一部中文术语桥梁、两篇针对性文章，以及项目后端的公开文档：**BK01、BK03、RV01、RV02、DT01–DT04**。SW01–SW03 是随后连接到真实代码的关键参考，不要求先读完所有材料才能开始。BK02 用于推导卡点，BK04/BK05 用于查阅。完整书目及合法获取入口见第12节与 `reading_manifest.json`。

本方案包含 **4小时起步训练** 和 **建议的8周、64小时开发学习路径**。4小时的目标是获得最小概念框架、识别明显的科学语义错误并完成一个小型验证；不是承诺4小时达到独立量子化学研究或全部功能开发水平。64小时同样是可调整的预算假设，不是用户指定的截止期，也不是能力达成保证。

**最终成果不是读书笔记数量，而是：一个基于现有架构的、具有科学验证证据的局部开发改进，以及在新输入上独立解释和验证它的能力。**

### 本次调研的证据边界

| 项目 | 已完成 | 未声称完成 |
|---|---|---|
| 文献 | 核对选定教材书目/目录、文章类型/年份/DOI；阅读摘要和可获得的相关HTML内容 | 通读所有教材、精读所有论文全文、穷尽全领域最新研究 |
| 项目 | 读取指定远程分支快照的有关架构、波函数/网格计划和当前SOP | 克隆并全面审计源码、运行测试、复现实验或验证本地HEAD |
| 教学skill | 读取personal-tutor的SKILL.md及learning-patterns.md，并据此构造契约、诊断、练习与验收 | 已在用户电脑安装skill、已经开展真实交互训练、学习者已掌握这些能力 |
| 交付 | 调研报告、资料清单、训练题、状态模板和Codex交接入口 | 教材/论文全文、第三方skill副本、已验证的工程补丁 |

教材以选定版次为准；不把后续印刷年份或数字平台上线日期当作新版年份。文章分为Review、Perspective、Software Focus、软件研究论文和原始方法论文，不能全部笼统写成“综述”。

## 1. 与项目实际边界的对应关系

### 1.1 本次锚定的项目快照

本次参考分支为 `feat/2.5-real-user-tutorials`，提交 `1279313033320ec92d9d7981593d5919997c5f5b`，提交时间2026-09-13，提交说明为 `docs(tutorials): close T06 technical gate`。它是本次学习设计的远程参考快照，不代表你的本地工作区，也不代表整个仓库所有分支的最新状态。[PR01]

项目的稳定方向是：**Blender作为原生展示前端；解析、科学语义和大型数组处理在不依赖bpy的core/外部处理程序中完成。** 波函数/网格计划已经把IOData、GBasis、MO、密度矩阵、自旋密度、ESP、非正交网格和field-on-surface放入同一条数据链。[PR02–PR03]

当前2.5工作流入口为 `docs/user/zh-CN/blender-workflow.md`。`docs/quantum-visualization/scientific-visualization/README.md` 已注明冻结在2026-09-09，属于归档资格证据，**不能拿它的旧按钮位置代替当前UI教程**。归档文件可用于了解真实输入来源和科学语义，但其中“已完成”的记录也不是本报告独立运行的结果。[PR04–PR05]

### 1.2 应学习什么，才能修改哪些部分

| 能力模块 | 必须理解的内容 | 项目对应位置/责任 | 应形成的验证证据 |
|---|---|---|---|
| 波函数表示 | AO/MO、Gaussian contraction、归一化、球谐/笛卡尔排列 | IOData输入、BasisSet/OrbitalSet及GBasis适配 | 同一波函数在约定转换前后的点值与范数一致 |
| 密度与自旋 | 占据数、非正交基、1-RDM、restricted/unrestricted | DensityMatrix、总密度、自旋密度与派生场 | Tr(PS)、空间积分和alpha/beta守恒检查 |
| 物理量语义 | MO振幅、电子密度、ESP、差分/跃迁密度不是同一种量 | GridSemantic、dataset标识和单位 | 不根据后缀或正负值静默猜测场类型 |
| 数值空间 | 仿射网格、完整step vectors、采样与插值、误差 | Grid3D、Cube、缓存与表面采样 | 斜网格、多dataset、单位和往返测试 |
| 科学表示 | 等值面、体渲染、切片、曲线、表面上的另一场 | View、Surface、Volume、颜色/图例 | 几何场与着色场分开追踪；同点数值可核对 |
| 局域分析 | NCI/RDG、ELF/LOL、IGMH与拓扑量的适用边界 | 外部分析接口、critic2或其他已有后端 | 输出定义、导数来源、fragment与参数可追溯 |
| 光谱与激发态 | 模态、跃迁、NTO、强度、展宽与轴单位 | Spectrum、state/mode引用和动画 | 不把视觉动画或展宽曲线冒充实验测量 |
| 生命周期 | 源数据、派生数据、显示缓存与项目引用 | CBQ、blend、hash与重建 | 冷重开、移动、删除派生缓存后可恢复 |

表中是学习能力映射，不是新增架构提案。具体类名、字段及模块路径须由本地Codex对照当前代码定位；不要为了学习再实现一个与现有模型重复的框架。[PR02–PR04]

### 1.3 初期明确不做的事

**现在必须学：** 实数分子轨道、闭壳层与简单开壳层、Gaussian基组与1-RDM、密度/ESP、三维网格语义、数值验证与可恢复展示。

**暂时忽略：** 从头编写SCF/DFT求解器、全部两电子积分推导、耦合簇算法、多参考全理论、GPU优化、全套量子化学文件格式、完整周期电子结构课程。它们不是实现可信MO/密度/ESP展示的必要前置。

**以后按需求学：** 复轨道与规范、周期边界与势零点、ECP/赝势、NTO跟踪、QTAIM积分、ELF/LOL不同约定、时间分辨光谱。基础能力未过关时，不用“学习更前沿的方法”代替修复单位、网格或矩阵错误。

## 2. 教材选择：一本主线，一本桥梁，两本查阅

### 2.1 英文主教材：BK01

**Introduction to Computational Chemistry，第3版，2017，Wiley，ISBN 9781118825990。** 推荐作为主线，因为选定版次把HF、基组、DFT、波函数分析和分子性质放在同一框架中。[BK01]

| 阅读位置 | 本轮问题 | 读完后应能产出的内容 |
|---|---|---|
| 第3章：尤其3.1、3.5、3.7、3.8 | 固定核近似、基组展开、restricted/unrestricted和SCF各意味着什么？ | 为一个真实输入说明几何、电子态与计算方法 |
| 第5章：5.1–5.4；ECP部分按需 | 为什么有primitive/contracted Gaussian、不同基组和不同角动量表示？ | 核对一个BasisSet的字段与规范化责任 |
| 第6章：6.2、6.7–6.9、6.11 | KS轨道与HF轨道有何区别？积分网格与显示网格如何区分？ | 给方法和数值设置建立不同的误差检查 |
| 第10章：Wave Function Analysis | 原子电荷、局域轨道、自然轨道分别从哪里来？ | 不把原子着色、ESP和电子密度当作同一种“电荷图” |
| 第11章：分子性质相关部分 | 外场响应、核坐标变化与光谱性质如何连接？ | 给Spectrum或振动展示写清物理定义 |
| 第12章：相关收敛与振动实例 | 怎样证明数值变化不是显示参数造成的？ | 设计改变一个变量的验证实验 |

不必从第1页读到最后一页。第2章力场、全部相关方法与大规模动力学不是首轮主线。表中章节已按所选第三版目录核对；本地电子书的PDF页序与书内印刷页码仍需重新映射。[BK01]

### 2.2 中文桥梁：BK03

**《结构化学基础》第5版，2017，北京大学出版社，ISBN 9787301283073。** 定位不是补基础量子力学，而是补“物理公式如何被化学研究者使用和描述”的语义层。[BK03]

重点读“波函数和电子云的图形”、分子轨道、成键/反键、σ/π、孤对电子及必要的对称性。每读一小段，就把概念映射到一张图的说明文字，例如：这张图展示的是MO振幅的正负等值面，不是电子沿某条路径运动，也不是正负电荷分布。前者服务化学沟通，后者服务开发时的标签、帮助信息和错误提示。

### 2.3 推导卡住时：BK02；中文严谨查阅：BK04

**Modern Quantum Chemistry，Dover 1996再版本，ISBN 9780486691862**，选读第2、3章，解决多电子态、HF、非正交基组与密度矩阵的推导问题。它是经典理论参考，不应成为进入项目之前的长篇阅读障碍。[BK02]

**《量子化学——基本原理和从头计算法（中）（第二版）》2009，科学出版社，ISBN 9787030220394**，作为中文查阅材料，优先定位Gaussian基函数、分子自洽场与DFT主题。没有必要先购齐并读完三册。目录页码以你实际取得的分册为准。[BK04]

### 2.4 高级参考：BK05

**Molecular Electronic-Structure Theory，2000，Wiley，ISBN 9780471967552**，适合遇到基组变换、归一化或RDM约定问题时深入查阅，不列入64小时计划的通读任务。[BK05]

**教材的分工：BK03负责中文术语与化学图像，BK01负责完整方法框架，BK02负责关键矩阵推导，BK04/BK05负责疑难查阅。** 这是本报告针对你的背景提出的使用建议，不是对教材普遍优劣的排名。

## 3. 近期综述与观点文章：按任务相关性，而不是按年份排队

### 3.1 首要专题主线：RV01，2025

**Visualization Analysis of Covalent and Noncovalent Interactions in Real Space**，Angewandte Chemie International Edition，64，e202504895，DOI **10.1002/anie.202504895**。出版社标注为**Review**，覆盖NCI、IGMH、IRI、ESP、ELF、deformation density等实空间可视化方法。这是本次筛选中与项目场类型最直接相连的近期综述，也是国内研究者发表于国际期刊的英文资料。[RV01]

读它不是为了记住更多缩写，而是为每一种方法填写同一张卡片：**输入是什么；定义是什么；哪一个场决定几何；哪一个场决定颜色；哪些结论不能从图上直接推出。** 初读顺序建议ESP/差分密度→NCI→IGMH/IRI→ELF/LOL。章节编号未从全文核对，不预设页码。

### 3.2 方法选择与可信输入：RV02，2022

**Best-Practice DFT Protocols for Basic Molecular Computational Chemistry**，DOI **10.1002/anie.202205735**。正式类型为**Scientific Perspective**，开放获取。它提供模型与计算协议的决策思路，并讨论准确性、稳健性和成本。[RV02]

针对项目，应提取“为什么选这种计算”的依据，而不是背诵一张泛函排行榜。特别要分开：模型/电子态是否适用、基组是否足够、SCF数值设置是否可靠、最终用于显示的网格是否足够。该文并不是覆盖全部激发态和波函数分析的教程，热化学经验也不能直接当作每一种密度或光谱性质的误差保证。

### 3.3 DFT的解释边界：RV03，2022，按需

**DFT exchange: sharing perspectives on the workhorse of quantum chemistry and materials science**，PCCP，24，28700–28781，DOI **10.1039/D2CP02827A**。它是多观点的**Perspective**，适合查阅密度质量、KS轨道解释及方法局限，不适合初学者逐页通读。[RV03]

本轮只输出一个成果：列出三条“不能仅凭HOMO/LUMO图片或轨道能量差直接得出的实验结论”，并写出缺失的前提。

### 3.4 与你的光学背景连接：RV04，2026

**Color-Pure Organic Luminophores: Characteristics, Definitions, Physical Basis and Fundamental Design Principles**，Angewandte Chemie International Edition，65，e9990265，DOI **10.1002/anie.9990265**，出版社首发日期2026-06-11，**Review，开放获取**。[RV04]

优先读§2 Spectral Characteristics与§3 Physical Basis of Spectral Width。它适合帮助设计光谱展示：明确谱峰、线宽和横轴定义，区分振动/环境带来的物理展宽与绘图软件人为加入的展宽。具体发光分子设计不列入P0；这篇较新文章是光谱方向的桥梁，而不是MO/密度求值的前置教材。

### 3.5 远期选读：RV05，2025

**Computation of Time-Resolved Nonlinear Electronic Spectra From Classical Trajectories**，WIREs Computational Molecular Science，15，e70012，DOI **10.1002/wcms.70012**，**Advanced Review，开放获取**。[RV05]

只有在项目真正进入时间分辨光谱或轨迹耦合展示时再读。当前不为了“追新”而提前学习整套非线性光谱与动力学算法。

## 4. 真正帮助写代码的文章和官方文档

这些资料很重要，但应与纯综述分开标注。

| ID | 文献/文档 | 类型与年份 | 直接用于哪一层 |
|---|---|---|---|
| SW01 | GBasis；DOI 10.1063/5.0216776 | 软件研究论文，2024 | Gaussian函数、MO、密度、ESP及导数的求值 |
| SW02 | IOData；DOI 10.1002/jcc.26468 | 软件研究论文，2021 | 读取、保存和规范化量化数据 |
| SW03 | Multiwfn总体介绍；DOI 10.1063/5.0216272 | 软件研究论文，2024 | 科学分析方法和外部参考结果 |
| SW04 | Recent developments in the PySCF program package；DOI 10.1063/5.0006074 | 软件研究论文，2020 | 受控计算、测试输入和独立数值对照 |
| SW05 | libwfa；DOI 10.1002/wcms.1595 | Software Focus，2022 | 开壳层、state/transition RDM、NTO及电子—空穴分析 |
| DT01 | IOData Basis set conventions | 官方技术文档 | AO排列、primitive归一化、Cartesian/pure约定 |
| DT02–DT03 | PySCF cubegen源码与DFT文档 | 官方源码/文档 | 数组、采样、单位和SCF积分网格 |
| DT04 | GBasis Introduction | 官方技术文档 | 后端责任与后续API阅读入口 |

**本轮不改变后端选型。** 项目快照的计划指定 `qc-gbasis==0.1.0`、Python导入名为 `gbasis`；GBasis不是SCF求解器、文件解析器或Blender渲染器。学习PySCF是为了理解生成数据和建立参照，不是把现有GBasis/IOData路径替换掉。在线文档可能对应不同版本，安装和开发必须优先服从本地项目锁文件与兼容性说明。[PR03]

IOData文档特别说明：它记录基组的排列和primitive归一化约定，但**不强制收缩函数归一化**。因此“读入成功”不能替代对适配结果的验证。必须查清原始文件、parser和求值后端分别负责哪一步，不能再统一乘一个猜测的归一化因子。[DT01]

原始方法补充只保留三篇：**FD01 NCI（2010）**、**FD02 IGMH（2022）**、**FD03 NTO（2003）**。它们为实现定义提供依据，不是近期综述；优先由RV01/SW05定位问题后再读。[FD01–FD03]

## 5. 开发必须掌握的最小概念骨架

### 5.1 从光学直觉出发，但给类比划边界

MO的正负振幅可以用“实数光学模场的符号和节点”帮助理解；电子密度则更接近对占据轨道贡献的汇总。这个类比只用于理解**振幅与平方模不同**，不意味着多电子态就是经典电磁场。完整多电子波函数定义在多粒子构型空间，普通三维MO图不是把完整多电子波函数直接画了出来。[BK01–BK02]

类似地，ESP-on-density可以理解为“用一个标量场定义地形表面，再用另一个场给表面着色”：密度决定表面在哪里，ESP决定各位置显示什么颜色。两者不能因为最后成为同一个Mesh就丢失各自的来源与单位。[PR03]

### 5.2 一电子表示、密度矩阵与电子数

先把范围限制为**实数AO/MO、非相对论分子**。设AO列向量为χ，MO系数矩阵C的第i列对应第i个轨道：

\[
\phi_i(\mathbf r)=\sum_\mu C_{\mu i}\chi_\mu(\mathbf r),\qquad
P=C\,\mathrm{diag}(n_i)\,C^T,
\]
\[
\rho(\mathbf r)=\sum_{\mu\nu}P_{\mu\nu}\chi_\mu(\mathbf r)\chi_\nu(\mathbf r),\qquad
S_{\mu\nu}=\int\chi_\mu\chi_\nu\,d^3r,
\]
\[
C^TSC=I,\qquad N=\int\rho\,d^3r=\mathrm{Tr}(PS).
\]

AO通常不是正交基，所以一般不能用Tr(P)代替电子数。对unrestricted表示，分别保存Pα和Pβ：总密度由两者相加得到，自旋数密度由两者相减得到；不要对两个通道各自再机械乘2。[BK02；SW01–SW02]

这里的P构造式对应所给轨道与占据数；相关方法的密度应使用相应的1-RDM，或与其一致的自然轨道和占据数。不能读取一个post-SCF文件后，把SCF轨道占据平方和自动宣称为该相关态密度。复杂轨道、spinor及程序特定指标顺序需另行明确共轭和基组约定，不直接套用本节实数公式。[BK01–BK02；SW05]

### 5.3 每一种“云图”需要自己的数据合同

| 科学量 | 定义/角色 | 常见量纲 | 最容易写错的语义 |
|---|---|---|---|
| 实数MO φ | 单电子轨道振幅 | a₀⁻³ᐟ² | 正负不是正负电荷；不能当密度 |
| 单轨道平方模 \|φ\|² | 归一化轨道的空间概率密度 | a₀⁻³ | 不等于多电子体系总密度 |
| 总电子数密度ρ | 对适当1-RDM求值得到 | 电子数/a₀³ | 电荷密度还要包含电荷符号与单位约定 |
| 自旋数密度ρɑ−ρβ | 两自旋通道数密度差 | a₀⁻³ | 可正可负，不是负概率；未必已乘磁矩常数 |
| 差分密度Δρ | 两个明确定义的态/计算结果之差 | a₀⁻³ | 积分只在两者电子数相同等条件下应为零 |
| ESP V | 对单位正试探电荷的静电势 | Eₕ/e，或明确的等价单位 | 不是电子密度，也不是自动定义的相互作用能 |
| RDG、ELF、LOL | 各自定义下的派生指标 | 通常无量纲 | 无量纲不代表可以互换，也不等于概率 |
| transition density / NTO | 态间跃迁信息及紧凑轨道表示 | 取决于所存对象和归一化 | 不是简单的激发态密度减基态密度 |

a₀为Bohr长度，Eₕ为Hartree能量。只写“atomic units”仍不足以区分轨道、密度和ESP；需要同时保存quantity、unit和定义来源。[BK01–BK02；RV01；SW05]

**整体轨道变号不改变其平方模；同占据数子空间内的酉变换也可保持总密度。** 因此跨程序比较时，轨道正负颜色交换不一定是错误，退简并子空间内的形状变化也不能直接认定为求值错误；必须按轨道重叠或子空间比较。另一方面，同一个线性组合内部的相对符号不能随意改变。[BK02]

### 5.4 ESP、差分与表面关联

对孤立、全电子体系，原子单位下：

\[
V(\mathbf r)=\sum_A\frac{Z_A}{|\mathbf r-\mathbf R_A|}
-\int\frac{\rho(\mathbf r')}{|\mathbf r-\mathbf r'|}\,d^3r'.
\]

核位置的奇点需要明确处理，不能把inf/NaN静默填成0再当作物理ESP。ECP/赝势和周期体系的核项、势零点及边界条件不由上式自动解决。[DT02；BK01]

差分密度必须写明Δρ=ρA−ρB、两者几何/坐标关系、电子数、计算层级、RDM种类及采样网格。其积分目标是N_A−N_B，而不是永远强制为0。为了研究固定几何下的电子重排，通常应固定几何并对齐网格；否则差图还包含几何改变的影响。禁止通过归一化或平移等后处理把一个不满足前提的比较“修成好看”。[BK01；SW05]

“在ρ=ρ₀的表面上显示V”需要保留两个dataset引用，并在同一物理坐标下采样。仅仅令两个数组shape相等，不能证明它们位置一致。[PR03]

### 5.5 NCI、ELF与拓扑：图像有解释条件

常用NCI约化密度梯度为：

\[
s=\frac{1}{2(3\pi^2)^{1/3}}\frac{|\nabla\rho|}{\rho^{4/3}}.
\]

低s等值面给出可视几何，颜色常来自sign(λ₂)ρ，λ₂为密度Hessian按从小到大排序后的第二个特征值。需要记录密度来源、导数算法、低密度截断、阈值和色标；一个数值不稳定的低密度尾部可能产生看似复杂的图案。[FD01]

IGMH不能仅用“NCI的另一个名字”替代：它使用特定密度分区，fragment的定义也参与输出解释。ELF/LOL是各自约定下的局域化指标，不是电子出现在某点的概率。QTAIM的临界点/路径是密度拓扑对象，也不应在UI中无条件翻译成“证明存在化学键”。高级量应先接入已有可信外部后端和出处，再讨论显示美化。[RV01；FD02]

### 5.6 光谱与动画的最低要求

振动模式是围绕参考结构的模式信息；可视化中的夸张振幅和播放速率是展示参数，不自动等于实际温度下的振幅或真实时间轨迹。不要把Raman activity无条件标成Raman intensity；需要对应条件与转换。电子激发也不能统一写成一个HOMO→LUMO箭头，应保留真正的state与transition信息。[BK01；SW05]

区分**离散跃迁线、施加指定展宽核后的模拟谱、实验光谱**。横轴由能量变为波长时，FWHM数值与曲线形状的解释需要重新核对；若纵轴是“每单位能量的谱密度”，还涉及变量变换的Jacobian，但若纵轴是作为光子能量函数的截面等量，不能机械套用同一个Jacobian。先定义纵轴代表什么，再决定如何变换。[RV04；基本变量变换推导]

## 6. 数值可视化不是单纯渲染

### 6.1 仿射网格是优先级最高的数学基础

令A的三列是完整step vectors，o为origin，整数索引u=(i,j,k)^T：

\[
\mathbf r=\mathbf o+A\mathbf u,\qquad \Delta V=|\det A|.
\]

若网格斜交，不能用三个对角元素之积代替体积，也不能只保留每个轴的长度。对于在索引坐标中算出的导数，常量A下：

\[
\nabla_{\mathbf r}\rho=A^{-T}\nabla_{\mathbf u}\rho,\qquad
H_{\mathbf r}=A^{-T}H_{\mathbf u}A^{-1}.
\]

这给出一个非常有价值的学习练习：先对解析线性/二次标量场验证网格坐标、梯度和Hessian，再将相同采样流程应用于分子数据。它比对着一张复杂云图判断“像不像”更能定位错误。这些关系由仿射坐标变换和链式法则推导；项目对完整step vectors的要求见PR03。

### 6.2 分开处理四类误差

| 误差层 | 改变什么 | 必须保持什么 | 不能用什么替代 |
|---|---|---|---|
| 电子结构模型 | HF/DFT、泛函、电子态等 | 目标问题与可比较条件 | 渲染更清晰 |
| 基组和SCF数值 | 基组、积分网格、收敛设置 | 几何与被比较的性质 | 仅显示网格变密 |
| 空间采样/积分 | padding、step、插值/求积方案 | 同一个波函数/密度矩阵 | 重新跑一个不同SCF解 |
| 显示离散化 | 等值面提取、网格简化、体渲染采样 | 权威科学数组与物理阈值 | 改原始数据以获得理想图形 |

padding扩大检查尾部截断，step缩小检查离散采样，两者应分别改变。求积还须明确采样是体素中心还是含端点网格，以及所用权重；简单地给每个含端点样本相同权重不是在所有边界条件下都精确。积分和图形边界都要做收敛检查。[BK01；DT02–DT03]

单位换算与艺术性场景缩放是不同操作。把Bohr坐标换成Å时，需根据所表示的量同步处理数值单位；把Blender对象放大以便构图，不应改变源密度矩阵、数组或电子数。[PR03–PR04；量纲推导]

### 6.3 一个真实的源码阅读案例

检索时，PySCF官方在线 `cubegen.orbital` 源码将AO值与系数直接相乘生成MO值，但输出注释包含 `1/Bohr^3`。对归一化轨道振幅，量纲推导得到的是Bohr⁻³ᐟ²；两者不一致。[DT02]

这不是宣称所有版本都存在同样问题，也不是已经提交或验证过的修复。本地学习时应重新检查安装版本。它说明：**知名上游软件的自由文本注释也不能取代物理量合同。** 正确做法是保留原始证据、报告冲突并采用有来源的语义规则，不是静默修改输入或根据注释盲目改数组。

## 7. 学习契约与能力分级

本方案依据personal-tutor的实际SKILL.md与learning-patterns.md构建，参考提交为 `d01ac82f511bdbdf82192c9ba9923f0e34f3137b`。[PT01–PT02]

| 契约项 | 本轮设定 |
|---|---|
| Target | 能针对现有ChemBlender链路定义、实现或修复一个科学场可视化功能，并给出数值证据 |
| Baseline | 物理/光学博士基础与基础Python来自学习者自述；量子化学专项能力尚未通过本轮诊断 |
| Time Budget | 起步4小时；建议总计64小时，分8周执行；首个4小时包含在64小时内 |
| Constraints | 优先中文解释、原术语保留；复用已有输入/后端/测试；不重构整套系统、不覆盖已有工作 |
| Final Performance | 在已有H2O输入上完成科学场验证与展示闭环；迁移到另一个体系或边界条件；提交一个有范围限制的代码/测试/文档改进 |
| Deadline | 尚未由用户指定；实际开始时再记录日期，不伪造日历截止期 |

成功标准限制为五项：**物理定义正确；数组与元数据可追溯；数值误差有证据；既有架构/生命周期可工作；能独立处理一个新变体。** 任一核心物理语义错误不能被渲染质量或总体分数抵消。

技能等级采用0–5：0未接触，1识别，2能解释，3提示下完成，4独立完成典型任务，5迁移到新场景。初始状态统一是“未验证”，而不是自动评为0；已有能力由诊断决定。核心能力至少到4，并有独立产出，才能报告本轮目标基本达成。[PT01]

Codex写出的代码可以作为示例和工具，但**不能单凭Codex执行成功就认定学习者已掌握**。至少要求学习者预测结果、解释关键决策，并独立修改一个未见过的小变体。

## 8. 学习路线

### 8.1 首个4小时：先建立一个小闭环

前提是已有可用的项目环境或可读取的fixture；若依赖安装阻塞，不把安装调试硬塞进“概念已掌握”，而改为先做纯NumPy与源文件语义练习，明确标注真实后端部分未验证。

| 时间块 | 结束时能做什么 | 动作与产出 | 通过标准 |
|---|---|---|---|
| 0–15分钟 | 暴露当前语义模型 | 完成LX00中的第一个案例，先独立回答 | 能区分确定、推断和未知；记录真实断点 |
| 15–45分钟 | 区分MO、密度、ESP | BK03相关图像概念＋本报告第5节；填写三张quantity卡片 | 每张卡有定义、单位、输入、积分/符号规则 |
| 45–90分钟 | 把矩阵与场联系起来 | 读取既有H2O元数据；纯NumPy或现成后端验证C、P、S关系 | 说明Tr(PS)和空间积分分别验证什么 |
| 90–150分钟 | 定位可视化科学错误 | 从LX01–LX03选择一个：单位、轴序或缺失语义；只处理一个主错误 | 给出最小反例和修复前后的同一验证 |
| 150–180分钟 | 补足当前断点 | 按错误选读BK01/BK02/DT01，而不是继续讲全部理论 | 在相近变体上不再犯同类错误 |
| 180–210分钟 | 完成最小整合 | 为一份MO或密度生成带来源的场说明；环境可用时进入现有展示链 | 定义、单位、坐标与源文件都可定位 |
| 210–230分钟 | 迁移判断 | 改变整体轨道符号或网格基向量，预测并验证结果 | 不依赖重复原步骤而能解释变化 |
| 230–240分钟 | 明确掌握与未掌握 | 更新learning_state.json，安排24小时复习 | 不把缺少证据的项目标记为完成 |

### 8.2 8周、64小时的建议主线

每周约8小时，可按“2小时定向阅读＋4小时操作/开发＋1小时验证记录＋1小时复习”安排。全部指定章节是查阅范围，不是16小时内必须逐页读完的任务。关键能力未通过时用下一周预算补训，而不是为了赶进度硬推进。

| 周次 | 主目标与阅读 | 代表性任务 | 可交付物与通过门槛 |
|---|---|---|---|
| W1，8h | 建立科学量语义；BK03、BK01第3章相关部分、DT02 | 首个4小时训练；解释既有H2O文件与MO/密度/ESP | quantity卡片＋来源表；不再混淆振幅、密度、势 |
| W2，8h | AO/MO、基组约定、1-RDM；BK01第5章、BK02选读、DT01/DT04 | 对同一波函数检查C/P/S；验证一个归一化或排列边界 | 范数/电子数检查＋一个约定转换回归测试 |
| W3，8h | HF/DFT与计算条件；BK01第6章、RV02、DT03 | 固定几何分别检查方法/基组/积分设置；不把不同SCF解直接逐点当等价 | 方法说明＋受控比较表；分清模型与数值误差 |
| W4，8h | Grid3D/Cube/仿射网格；DT02、项目当前规范 | 解析场斜网格、轴序、多dataset、单位/往返测试 | 纯数据层测试；矩阵行列约定与索引次序明确 |
| W5，8h | 密度面上的ESP、切片、恢复；RV01相关部分、当前SOP | 使用现有处理程序和CBQ/View路径建立H2O展示闭环 | 参数/来源齐全的场景；冷重开和派生缓存重建记录 |
| W6，8h | 开壳层与一个局域分析；RV01、SW03、FD01或FD02 | CH3总/自旋密度；只选一个小体系NCI或IGMH案例 | 通道/积分验证；几何场与着色场分离；输入定义可追溯 |
| W7，8h | 光谱与激发态语义；BK01第11章、SW05、RV04 | 一个Spectrum语义案例；环境支持时解释一组NTO；不新增整套激发态求解器 | 谱线/展宽/单位说明；不混淆差分与跃迁密度。P0未过关则改为补训 |
| W8，8h | 最终整合与独立迁移 | 完成Keystone、注入一个边界条件、形成范围明确的改进 | 可审查的代码/测试/文档变更＋五项最终能力证据 |

W6的额外水二聚体或其他体系输入须合法生成或引用并补全manifest；本报告没有宣称它已存在于仓库。W7可以交付经过验证的语义和测试案例，而不强制实现尚未具备后端条件的新功能。

### 8.3 完成基础后再选方向

| 可选方向 | 建议追加预算 | 前置门槛 | 实际成果 |
|---|---|---|---|
| NTO/电子—空穴与光谱 | 8–16h | RDM和state引用已通过 | 一项具有重构/权重验证的激发态表示 |
| NCI/IGMH/ELF/QTAIM深化 | 8–16h | 导数、场语义、外部后端已通过 | 某一种分析方法的参数化、来源和回归测试 |
| 周期/复轨道/ECP | 16–24h起 | 分子体系全部核心门槛通过 | 单个明确体系的边界条件与单位合同；不承诺一次覆盖全部 |

追加预算是学习规划建议，不是性能基准或工程工时承诺。

## 9. Keystone Exercise：把已有水分子数据变成可验证的科学展示

### 9.1 优先复用的输入

归档资料列出可作为学习起点的输入：

- `examples/scientific-visualization/inputs/wavefunction/water_sto3g_hf_g03.fchk`：记录为RHF/STO-3G；文件名不证明精确程序版本。
- `examples/scientific-visualization/inputs/wavefunction/ch3_hf_sto3g.fchk`：记录为UHF/STO-3G，适合自旋通道迁移。
- `examples/scientific-visualization/input-manifest.json`：优先核对输入身份、来源及hash。

本地Codex必须先确认路径和manifest仍存在，不能把本报告中的旧路径当作无条件事实。STO-3G在这里是小型诊断fixture，不是精确定量分析的通用推荐设置。[PR05]

### 9.2 整合任务

通过项目**现有**读取和外部求值路径，建立一个H2O的MO正负等值面、总密度、密度表面ESP着色以及一个有已知物理坐标的切片/剖面。保留方法、基组、电子态、占据数、RDM来源、源hash、backend版本、单位、origin、完整step vectors、shape、dataset、阈值和色标。[PR03–PR04]

先对源数据与数值场验证，再生成图。对于跨软件对照，只有**同一个波函数/密度矩阵、相同基组约定与采样点**才能直接比较求值；两个独立SCF计算不是自动相同的输入。不能把parser和renderer两边使用同一个错误中间量产生的一致结果当作独立验证。

随后保存相邻的blend/CBQ，冷重开；删除可重建的渲染缓存再重建，检查权威数组与源身份未改变。最后迁移到CH3或另一份明确定义的输入，处理alpha/beta、整体轨道符号变化或斜网格中的至少一个变体。

开发改进只选一项：例如完善quantity/单位校验、增加斜网格回归测试、完善field-on-surface元数据检查或修复某个已复现的错误。若对应功能已经正确实现，就提交缺少的测试或经过验证的使用说明，不强行制造重构需求。

### 9.3 建议的训练验收阈值

**以下是本报告提出的初始训练门槛，不是文献公认标准，更不是本次已经得到的实验结果。** 应根据源文件精度、方法、积分方案和项目既有测试校准；不得为了过门槛而重归一化或截断真实数据。

| 检查 | 建议起点/判断 | 条件与限制 |
|---|---|---|
| MO重叠矩阵 | max\|CᵀSC−I\| ≤ 10⁻⁸ | 充分精度的实数、归一化输入；低精度源文件需说明更宽容差 |
| 代数电子数 | \|Tr(PS)−N\| ≤ 10⁻⁸ | 与源RDM/占据数约定相符；不是对所有格式强制相同精度 |
| 网格电子数 | 相对误差初步目标≤0.5%；继续细化变化目标≤0.2% | 必须同时有padding和step检查，核附近与尾部可能需要更细网格 |
| 单MO空间范数 | 积分接近1，并报告细化变化 | 使用平方模；不能由显示阈值截断后的表面估算 |
| 自旋/差分积分 | 分别趋近Nα−Nβ、N_A−N_B | 不使用相对误差除以可能为零的目标值 |
| 同输入双后端点值 | 初步混合容差：绝对容差＋10⁻⁶量级相对项 | 绝对容差按量纲和源精度设置；节点附近不使用纯相对误差 |
| 仿射坐标与解析场 | 先做到接近float64舍入误差，再测插值/求导误差 | 必须验证矩阵行列约定；不要求不同阶数数值导数同精度 |
| 数组有限性/ESP奇点 | 非有限值必须有明确原因与mask/错误状态 | 核奇点不能填0伪装成有限物理势 |
| 往返与恢复 | 无损数组可比hash；文本格式按其输出精度比较 | 不对固定小数位Cube要求bitwise相同；UI重建不等于重新计算科学数组 |

“电子数对了”仍不能证明密度处处正确；多个错误可能互相抵消。至少组合代数不变量、空间积分、局部点值和一个解析/独立参照，不只看截图。

## 10. 如何让本地Codex真正带你学习

`CODEX_HANDOFF.md`是可直接作为首次任务的交接文本；`exercises/EXERCISES.md`包含LX00–LX08练习；`learning_state.json`保存未验证能力和证据引用。**不需要创建新的全局AGENTS.md，也不应该覆盖项目已有指令。**

每次学习按一个小闭环进行：先让学习者预测/解释/操作，再执行必要验证；错误时定位断点，连续两次仍错才给最小解释并换一个变体。每轮只给一个主要挑战。教材和论文只读取当前挑战需要的部分，引用实际版次、章节以及必要的PDF/印刷页码，不补造没有读到的内容。[PT01–PT02]

学习者取得资料后，在manifest填写实际路径与sha256。Codex先核对文件题名、版次和DOI，再建立页码对应；无法读取的材料标为blocked或not_available。扫描件需要识别时先检查可提取文本，不把低质量OCR内容直接作为公式真值。

每次结束只更新当前能力、证据路径、重复错误、剩余预算和下一项挑战。24小时后主动回忆，7天后做无提示迁移。没有真实作答/操作证据就保持unverified；一次抄对不等于级别4。

## 11. 下载与启动顺序

**第一次准备：** BK01、BK03、RV01、RV02；保存DT01–DT04，取得项目现有输入和可用环境。不用一次下载所有高级书和论文。

**进入开发：** 加入SW01–SW03；矩阵推导卡住时查BK02，中文推导需要时查BK04。进入NCI/IGMH再取得FD01/FD02；进入激发态/光谱再取得SW05/RV04。

**以后再准备：** BK05、RV03、RV05、FD03及其他专题。未检出全文OA的项目保留出版社/机构/作者授权渠道；软件免费与论文免费不是同一件事，不能混为一谈。

完整资料登记共22项：5部教材、5篇综述/观点文章、5篇软件类文章、3篇原始方法论文、4份官方技术文档。后面的数量统计指清单条目，不表示已下载或读完这些全文。

## 12. 书目、入口与逐项阅读范围

条目中的建议、练习和优先级为本报告制定；题名、年卷页、DOI/ISBN及文献类型来自列出的来源。每一项的实际检索阅读深度都单独标注。完整作者请通过出版社引文导出补全，不将“et al.”当作完整作者表。

### BK01｜Introduction to Computational Chemistry, 3rd edition

**P0｜textbook｜2017**。Wiley。ISBN 9781118825990。作者：Frank Jensen

入口：<https://www.wiley-vch.de/de/fachgebiete/naturwissenschaften/introduction-to-computational-chemistry-978-1-118-82599-0>

**选读：**Ch. 3 Hartree–Fock Theory：3.1、3.5、3.7、3.8；Ch. 5 Basis Sets：5.1–5.4；5.12按需；Ch. 6 Density Functional Methods：6.2、6.7–6.9、6.11；Ch. 10 Wave Function Analysis：10.1–10.5；Ch. 11 Molecular Properties：11.7、11.9、11.10；Ch. 12 Illustrating Concepts：收敛与振动实例。

**学习产出：**整理方法/基组/性质卡片；解释一个场的来源与限制，并设计独立收敛检查。

**获取状态：**商业教材；出版社、图书馆或合法电子书。样章不等于全书开放。

**本次阅读深度：**出版社书目信息与目录；未通读全书。

**注意：**指定第三版以便对齐章节；不宣称这是检索日最新版本。

### BK02｜Modern Quantum Chemistry: Introduction to Advanced Electronic Structure Theory

**P1｜textbook｜1996**。Dover reprint。ISBN 9780486691862。作者：Attila Szabo；Neil S. Ostlund

入口：<https://store.doverpublications.com/products/9780486691862>

其他核对/获取入口：<https://www.harvard.com/book/9780486691862>

**选读：**Ch. 2 多电子波函数、Slater行列式及相关记号；Ch. 3 Hartree–Fock、基组表示、密度矩阵与SCF；Ch. 1 仅补诊断暴露的线性代数缺口。

**学习产出：**独立解释并验证 C^T S C=I、N=Tr(PS)，说明为什么不能用Tr(P)。

**获取状态：**商业教材；Dover或图书馆。

**本次阅读深度：**出版社书目信息及目录资料；未通读。

**注意：**1996是所选Dover再版本年份，不是把经典内容误称为1996年首创。

### BK03｜结构化学基础（第5版）

**P0｜textbook｜2017**。北京大学出版社。ISBN 9787301283073。作者：周公度；段连运

入口：<https://metalib.nefu.edu.cn/mspace/searchDetailLocal/m6d4efecb2f8527b43bd2190a39412123>

其他核对/获取入口：<https://chem.cqnu.edu.cn/info/1268/10205.htm>；<https://www.bookschina.com/7511063.htm>

**选读：**第2章中“波函数和电子云的图形”；双原子与多原子分子的分子轨道、成键/反键、σ/π与孤对电子；对称性只学判断节点和轨道标签所需内容。

**学习产出：**画出概念对照：轨道、电子云、电子密度、化学键；解释H2O和一个π体系的图。

**获取状态：**商业教材；出版社/高校图书馆/授权电子书。馆藏链接不保证个人具有访问权限。

**本次阅读深度：**高校馆藏与教学书目核对，目录辅助核查；未通读。

**注意：**选定2017年第5版；数字平台上线年份不当作出版年份；不是习题解答本。

### BK04｜量子化学——基本原理和从头计算法（中）（第二版）

**P1｜textbook｜2009**。科学出版社。ISBN 9787030220394。作者：徐光宪；黎乐民；王德民

入口：<https://www.chem.pku.edu.cn/xyxw/171359.htm>

其他核对/获取入口：<https://tl.zxhsd.com/kgsm/ts/big5/2026/04/03/5898435.shtml>；<https://www.sohu.com/a/403555250_488198>

**选读：**高斯型基函数及相关积分的概念部分；分子自洽场理论；密度泛函理论；电子相关方法只读概览。

**学习产出：**解释primitive与contracted Gaussian的区别，定位归一化和矩阵表示问题。

**获取状态：**商业教材；科学出版社、图书馆或授权电子书。

**本次阅读深度：**高校来源核对系列及作者；书目/目录资料核对所选分册ISBN与年份。

**注意：**出版书目采用2009版次；书商页面更新或印刷年份不作为新版年份。章节页码由本地实际版本确认。

### BK05｜Molecular Electronic-Structure Theory

**P2｜textbook｜2000**。Wiley。ISBN 9780471967552。作者：Trygve Helgaker；Poul Jørgensen；Jeppe Olsen

入口：<https://www.wiley-vch.de/de/fachgebiete/naturwissenschaften/molecular-electronic-structure-theory-978-0-471-96755-2>

**选读：**Gaussian basis、归一化、Cartesian/pure变换；HF与约化密度矩阵相关章节；按问题查阅。

**学习产出：**写出一个跨软件约定转换的数值等价性测试。

**获取状态：**商业教材；出版社/图书馆。

**本次阅读深度：**出版社书目信息与主题范围；未通读。

**注意：**不安排全书阅读，也不把两电子积分引擎、耦合簇求解器作为本轮开发目标。

### RV01｜Visualization Analysis of Covalent and Noncovalent Interactions in Real Space

**P0｜review｜2025**。Angewandte Chemie International Edition 64(29), e202504895。DOI 10.1002/anie.202504895。作者：Tian Lu

入口：<https://onlinelibrary.wiley.com/doi/10.1002/anie.202504895>

**选读：**ESP与deformation density；NCI、IGMH、IRI的定义与差别；ELF、LOL及图像解释边界；应用部分选择一个小分子案例。

**学习产出：**每种方法一张“输入—定义—几何场—着色场—解释边界”卡片。

**获取状态：**已核验摘要/书目；未确认全文OA。经出版社、机构订阅或作者授权获取。

**本次阅读深度：**出版社摘要、文献类型、书目信息与参考文献入口；未精读全文。

### RV02｜Best-Practice DFT Protocols for Basic Molecular Computational Chemistry

**P0｜perspective｜2022**。Angewandte Chemie International Edition 61(42), e202205735。DOI 10.1002/anie.202205735。作者：Markus Bursch；Jan-Michael Mewes；Andreas Hansen；Stefan Grimme

入口：<https://onlinelibrary.wiley.com/doi/10.1002/anie.202205735>

其他核对/获取入口：<https://doi.org/10.26434/chemrxiv-2022-n304h>

**选读：**§2.1决策流程、§2.2电子结构、§2.5泛函；基组与计算设置相关部分；选择一个与小分子输入准备相关的实例，不读完所有热化学案例。

**学习产出：**为一个固定几何案例写方法选择说明，分开检查模型误差、基组误差与展示网格误差。

**获取状态：**出版社标注Open Access；可从页面获取合法全文。

**本次阅读深度：**出版社HTML摘要及部分正文，类型与OA状态已核对。

**注意：**正式类型是Scientific Perspective，不是把它改称纯Review；重点并非全面的激发态或实空间分析教程。

### RV03｜DFT exchange: sharing perspectives on the workhorse of quantum chemistry and materials science

**P2｜perspective｜2022**。Physical Chemistry Chemical Physics 24, 28700–28781。DOI 10.1039/D2CP02827A。作者：A. M. Teale et al.

入口：<https://doi.org/10.1039/D2CP02827A>

**选读：**密度质量、Kohn–Sham轨道解释、DFT限制的讨论；选读相关观点，不按全篇长度制定通读任务。

**学习产出：**列出三个不能从HOMO/LUMO截图直接推出的结论及原因。

**获取状态：**期刊开放获取入口；按出版社页面下载。

**本次阅读深度：**出版信息与相关HTML讨论；未逐段精读整篇。

**注意：**多观点Perspective；不能把不同作者观点拼成一致结论。

### RV04｜Color-Pure Organic Luminophores: Characteristics, Definitions, Physical Basis and Fundamental Design Principles

**P1｜review｜2026**。Angewandte Chemie International Edition 65(30), e9990265。DOI 10.1002/anie.9990265。作者：Johannes Gierschner et al.

入口：<https://onlinelibrary.wiley.com/doi/full/10.1002/anie.9990265>

**选读：**§2 Spectral Characteristics；§3 Physical Basis of Spectral Width；分子设计案例初期略读。

**学习产出：**设计光谱数据契约，区分跃迁线、人为展宽谱与实验光谱，明确横纵轴定义。

**获取状态：**出版社标注Open Access；首发2026-06-11。

**本次阅读深度：**出版社摘要及相关HTML正文，类型、日期和DOI已核对。

**注意：**作者列表请以出版社导出的完整引文为准；本清单不补造完整作者。

### RV05｜Computation of Time-Resolved Nonlinear Electronic Spectra From Classical Trajectories

**P2｜review｜2025**。WIREs Computational Molecular Science 15(3), e70012。DOI 10.1002/wcms.70012。作者：完整作者/维护者请见官方引文或文档。

入口：<https://doi.org/10.1002/wcms.70012>

**选读：**电子态计算—轨迹—时间分辨光谱的概念联系；完成基础光谱模块后按需选读。

**学习产出：**只在明确扩展任务后提交一个输入/输出/适用近似说明。

**获取状态：**出版社标注Open Access。

**本次阅读深度：**出版社摘要与书目信息；未精读全文。

**注意：**Advanced Review；不因发表新就提升为P0。完整作者用DOI从出版社导出。

### SW01｜GBasis: A Python Library for Evaluating Functions, Functionals, and Integrals Expressed with Gaussian Basis Functions

**P0｜software_article｜2024**。The Journal of Chemical Physics 161, 042503。DOI 10.1063/5.0216776。作者：Taewon David Kim et al.

入口：<https://doi.org/10.1063/5.0216776>

其他核对/获取入口：<https://gbasis.qcdevs.org/intro.html>

**选读：**Gaussian函数/收缩/求值接口；MO、密度、ESP与导数的输入约定；结合项目锁定版本文档。

**学习产出：**给同一组基函数/系数/点构造求值和约定转换回归测试。

**获取状态：**项目文档公开；论文全文许可通过出版社核对，不预设OA。

**本次阅读深度：**官方项目说明与引文信息；未精读论文全文。

**注意：**项目快照固定qc-gbasis==0.1.0，import名gbasis；不要据在线文档默认升级。

### SW02｜IOData: A python library for reading, writing, and converting computational chemistry file formats and generating input files

**P0｜software_article｜2021**。Journal of Computational Chemistry 42(6), 458–464。DOI 10.1002/jcc.26468。作者：Toon Verstraelen et al.

入口：<https://doi.org/10.1002/jcc.26468>

其他核对/获取入口：<https://iodata.readthedocs.io/en/latest/>；<https://iodata.readthedocs.io/en/latest/how_to_cite.html>

**选读：**文件表示与转换边界；必须配合DT01 Basis set conventions。

**学习产出：**整理一个真实FCHK/Molden的读取能力与缺失字段表。

**获取状态：**官方文档公开；论文全文许可通过出版社核对。

**本次阅读深度：**官方How to Cite核对DOI/卷期页，官方文档相关章节已读。

### SW03｜A comprehensive electron wavefunction analysis toolbox for chemists, Multiwfn

**P0｜software_article｜2024**。The Journal of Chemical Physics 161, 082503。DOI 10.1063/5.0216272。作者：Tian Lu

入口：<https://doi.org/10.1063/5.0216272>

其他核对/获取入口：<https://sobereva.com/multiwfn/>

**选读：**与所选物理量相关的分析框架；实际操作查对应手册；本轮只选ESP或NCI的一项作交叉参照。

**学习产出：**生成与GBasis/项目后端相同定义的参考量，记录输入和参数差异。

**获取状态：**出版社显示购买入口；不能把软件免费下载等同于论文OA。官方手册另行获取。

**本次阅读深度：**出版社元数据/摘要及官方说明；未精读全文。

**注意：**官方网站本次访问有不稳定情况；保留官方入口，不保证自动下载成功。

### SW04｜Recent developments in the PySCF program package

**P1｜software_article｜2020**。The Journal of Chemical Physics 153, 024109。DOI 10.1063/5.0006074。作者：Qiming Sun et al.

入口：<https://doi.org/10.1063/5.0006074>

其他核对/获取入口：<https://arxiv.org/abs/2002.12531>；<https://pyscf.org/>

**选读：**程序整体架构与分子HF/DFT相关部分；具体调用查本地版本与官方文档。

**学习产出：**固定几何、方法和基组生成可追溯小分子fixture，并区分重新SCF和对同一波函数求值。

**获取状态：**出版社版本访问权单独核对；作者预印本arXiv:2002.12531可合法获取。

**本次阅读深度：**出版信息、摘要及预印本入口；未精读全文。

### SW05｜libwfa: Wavefunction analysis tools for excited and open-shell electronic states

**P1｜software_focus｜2022**。WIREs Computational Molecular Science 12(4), e1595。DOI 10.1002/wcms.1595。作者：Felix Plasser；Anna I. Krylov；Andreas Dreuw

入口：<https://wires.onlinelibrary.wiley.com/doi/10.1002/wcms.1595>

**选读：**state/transition density matrices；NTO与电子—空穴分析；开壳层只读当前目标所需部分。

**学习产出：**写出state density、difference density、transition density、NTO之间的区别。

**获取状态：**出版社标注Open Access；个别入口本次发生访问错误，可用DOI解析。

**本次阅读深度：**期刊摘要与元数据核对；未精读全文。

**注意：**正式类型Software Focus，不与纯Review混列。

### FD01｜Revealing Noncovalent Interactions

**P1｜method_article｜2010**。Journal of the American Chemical Society 132(18), 6498–6506。DOI 10.1021/ja100936w。作者：Erin R. Johnson et al.

入口：<https://pubs.acs.org/doi/abs/10.1021/ja100936w>

**选读：**RDG定义、密度Hessian第二特征值与颜色映射；真实密度与近似密度输入的区别。

**学习产出：**把低RDG几何面与sign(lambda2)*rho着色场分开保存、校验。

**获取状态：**全文通过出版社/机构；补充资料可另行获取，不能由此推断全文OA。

**本次阅读深度：**出版信息、摘要与方法定义核对；未全面精读全文。

### FD02｜Independent gradient model based on Hirshfeld partition: A new method for visual study of interactions in chemical systems

**P1｜method_article｜2022**。Journal of Computational Chemistry 43(8), 539–555。DOI 10.1002/jcc.26812。作者：Tian Lu；Qinxue Chen

入口：<https://onlinelibrary.wiley.com/doi/10.1002/jcc.26812>

其他核对/获取入口：<https://www.cambridge.org/engage/chemrxiv/article-details/61aa3e9763557cde10956907>

**选读：**IGMH与IGM的密度来源和分区差别；fragment定义与分子间/分子内作用显示。

**学习产出：**解释一次改fragment划分为何会改变输出，并保留partition provenance。

**获取状态：**正式版权限按出版社；ChemRxiv作者预印本与教程附件可合法获取。

**本次阅读深度：**出版信息、摘要和作者预印本/教程入口核对。

**注意：**正式版与预印本版本分别记录；不把Research Article写成Review。

### FD03｜Natural transition orbitals

**P2｜method_article｜2003**。The Journal of Chemical Physics 118(11), 4775–4777。DOI 10.1063/1.1558471。作者：Richard L. Martin

入口：<https://doi.org/10.1063/1.1558471>

其他核对/获取入口：<https://cir.nii.ac.jp/crid/1362262945225389568>

**选读：**跃迁密度矩阵的紧凑轨道表示；配合SW05理解。

**学习产出：**对正交化后跃迁矩阵作SVD，检查重构与权重定义。

**获取状态：**出版社/机构；未核实全文OA。

**本次阅读深度：**书目信息与方法摘要核对；未精读全文。

### DT01｜IOData — Basis set conventions

**P0｜documentation｜动态文档**。IOData official documentation。Live documentation。作者：完整作者/维护者请见官方引文或文档。

入口：<https://iodata.readthedocs.io/en/latest/basis.html>

**选读：**Gaussian basis functions；conventions / primitive_normalization；Cartesian to pure functions。

**学习产出：**检查primitive与contraction归一化分别由谁负责。

**获取状态：**公开HTML；保存使用版本及日期。

**本次阅读深度：**相关官方HTML正文已读。

### DT02｜PySCF — pyscf.tools.cubegen source

**P0｜documentation｜动态文档**。PySCF official source documentation。Live documentation。作者：完整作者/维护者请见官方引文或文档。

入口：<https://pyscf.org/_modules/pyscf/tools/cubegen.html>

**选读：**orbital；density；mep；Cube.get_coords及写出网格坐标。

**学习产出：**核对生成MO的实际表达式与输出注释，写出正确物理量合同。

**获取状态：**公开HTML；与本地安装版本比对。

**本次阅读深度：**相关源码已读。

**注意：**检索时orbital写入的注释为1/Bohr^3，但数值由AO乘系数直接得到；这与归一化MO幅度量纲不一致。不要静默篡改用户源文件，应保存来源并报告冲突。

### DT03｜PySCF — Density functional theory

**P0｜documentation｜动态文档**。PySCF official documentation。Live documentation。作者：完整作者/维护者请见官方引文或文档。

入口：<https://pyscf.org/user/dft.html>

**选读：**DFT计算与数值积分网格相关部分。

**学习产出：**设计两类网格分别改变的受控实验。

**获取状态：**公开HTML；API以本地版本为准。

**本次阅读深度：**相关官方文档已读。

### DT04｜GBasis — Introduction

**P0｜documentation｜动态文档**。GBasis official documentation。Live documentation。作者：完整作者/维护者请见官方引文或文档。

入口：<https://gbasis.qcdevs.org/intro.html>

**选读：**Overview与Gaussian函数求值相关入口。

**学习产出：**说明GBasis与SCF solver、parser、renderer的不同职责。

**获取状态：**公开HTML；安装服从ChemBlender锁文件。

**本次阅读深度：**官方介绍与引文核对。

## 13. 项目与skill来源索引

| ID | 来源及状态 | 固定入口 |
|---|---|---|
| PR01 | 参考分支提交；不等于本地HEAD | <https://github.com/psiQAQ/ChemBlender_2_x/commit/1279313033320ec92d9d7981593d5919997c5f5b> |
| PR02 | 量子化学可视化开发入口；已阅读 | <https://github.com/psiQAQ/ChemBlender_2_x/blob/1279313033320ec92d9d7981593d5919997c5f5b/docs/quantum-visualization/README.md> |
| PR03 | 波函数、网格与表面计划；已阅读 | <https://github.com/psiQAQ/ChemBlender_2_x/blob/1279313033320ec92d9d7981593d5919997c5f5b/docs/quantum-visualization/plans/wavefunction-and-grids.md> |
| PR04 | 当前2.5中文Blender联动SOP；已阅读 | <https://github.com/psiQAQ/ChemBlender_2_x/blob/1279313033320ec92d9d7981593d5919997c5f5b/docs/user/zh-CN/blender-workflow.md> |
| PR05 | 科学量可视化归档手册；已阅读相关前部及输入说明，不宣称全文精读 | <https://github.com/psiQAQ/ChemBlender_2_x/blob/1279313033320ec92d9d7981593d5919997c5f5b/docs/quantum-visualization/scientific-visualization/README.md> |
| PR06 | 历史Phase 0数据边界议程；用于理解约束，不作为当前进度依据 | <https://github.com/psiQAQ/ChemBlender_2_x/blob/1279313033320ec92d9d7981593d5919997c5f5b/docs/quantum-visualization/architecture/data-boundary.md> |
| PT01 | personal-tutor主skill；已完整读取 | <https://github.com/psiQAQ/personal-tutor/blob/d01ac82f511bdbdf82192c9ba9923f0e34f3137b/.agents/skills/personal-tutor/SKILL.md> |
| PT02 | 教学模式参考；已完整读取 | <https://github.com/psiQAQ/personal-tutor/blob/d01ac82f511bdbdf82192c9ba9923f0e34f3137b/.agents/skills/personal-tutor/references/learning-patterns.md> |

## 14. 本地第一题

一份文件被标为“电子密度”，其数组含正负值。开发者为了让体渲染更好看，准备先取绝对值，再用min–max归一化，并在图例写“电子云”。

**先不要改代码：你需要取得哪些最少证据，才能判断该操作在物理上是否成立？哪些信息即使文件可以成功渲染也仍然未知？**

将你的首次独立回答保存在LX00记录中。Codex根据这个回答定位第一个断点，再决定下一步读哪一小节；不要先展示整套标准答案。
