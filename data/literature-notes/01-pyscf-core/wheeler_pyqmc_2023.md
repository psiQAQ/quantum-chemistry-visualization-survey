# wheeler_pyqmc_2023

BibTeX key：`wheeler_pyqmc_2023`

## 摘要

We describe a new open-source Python-based package for high accuracy correlated electron calculations using quantum Monte Carlo (QMC) in real space: PyQMC. PyQMC implements modern versions of QMC algorithms in an accessible format, enabling algorithmic development and easy implementation of complex workflows. Tight integration with the PySCF environment allows for a simple comparison between QMC calculations and other many-body wave function techniques, as well as access to high accuracy trial wave functions.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 随机方法、结果表达与不确定性</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>PyQMC 输出中哪些量适合可视化，随机误差和采样收敛应如何随图表达？</p>
<p><strong>回答：</strong> PyQMC 产生以下适合可视化的量：(a) 实空间波函数——Multi-Slater Jastrow (MSJ) 波函数 Ψ(R) = e^J(R,α) Σ_k c_k D_k(R, β)（Eq.1, Section II.A, p.2），可在实空间格点上评估并渲染为三维标量场/等值面；(b) 电子密度和密度矩阵——Section I (p.1) 提到 "complex observables, such as energy density and density matrices"，这些是量子化学可视化的核心对象；(c) 能量随 MC 步数的收敛曲线——QMC 的核心输出是带有统计误差的能量期望值，应使用带误差棒的收敛图展示，x 轴为采样步数/等价样本数，y 轴为能量，阴影带表示 ±1σ 或 ±2σ 置信区间；(d) 变分参数优化轨迹——Jastrow 因子参数 α、行列式系数 c_k、轨道参数 β 的优化路径可视为高维空间中的轨迹。统计误差的可视化建议：(i) 自动将误差传播到所有衍生量（如能量差、能垒）；(ii) 使用 trace plot（采样轨迹图）展示 MCMC 链的自相关和混合质量；(iii) 对空间量（密度、波函数）应包含不确定性估计的可视化（如半透明偏差带或动画闪烁）。</p>
<h3>问题 2</h3>
<p>全 Python 集成对批处理、后处理和跨工具交换的实际帮助是什么？</p>
<p><strong>回答：</strong> 论文强调全 Python 设计带来多重实际优势：(a) 与 PySCF 紧密集成——"Tight integration with the PySCF environment allows for a simple comparison between QMC calculations and other many-body wave function techniques, as well as access to high accuracy trial wave functions"（Abstract, p.1），这意味着 QMC 的 trial wave function 可直接从 PySCF 的 HF/DFT/CASSCF 计算生成，无需格式转换；(b) 快速开发与修改——PyQMC 旨在"streamline the development and teaching of new ideas in QMC"（Introduction, p.1），不同于 C++/Fortran 的 QMC 程序（QMCPACK、CASINO、TurboRVB、CHAMP），Python 实现降低了修改和测试新算法的门槛；(c) 模块化设计——"modularity and compatibility with user-modified code"（Introduction, p.2），用户可替换个别组件而不影响整体；(d) 跨平台并行化——"flexibility of parallelization across diverse platforms (traditional desktop, cloud, and high performance computing)" 和 "a unified codebase for running on graphics processing units or central processing units"（Introduction, p.2）；(e) 对后处理的意义：由于所有量都是 Python 对象，可与 NumPy/SciPy/Matplotlib/Blender Python API 直接交互，无需文件解析——这恰好与引擎无关工作流的"科学元数据完整"理念一致。对跨工具交换的具体帮助：PyQMC 的 trial wave function 对象可直接导出为 .cube 网格格式（通过 PySCF 的 cube 生成工具），或通过 HDF5 导出密度矩阵。</p>
<h3>问题 3</h3>
<p>问卷是否需要增加不确定度、采样状态或统计误差元数据需求？</p>
<p><strong>回答：</strong> 是。PyQMC 代表的随机方法（QMC、FCIQMC、DMC 等）与传统确定性方法（HF、DFT、CCSD）在输出特性上有本质区别：(a) 统计不确定性——QMC 能量是随机变量，"energies with statistical errors"（Section I, p.1），每个输出值都必须附带误差估计；(b) 采样收敛——需要报告等价样本数、自相关时间、R-hat 等收敛诊断；(c) 元数据需求——可视化文件（如 .cube 或 OpenVDB）的元数据应扩展以包含：统计误差场（每个格点值的标准差）、采样参数（步数、热平衡步数、步长）、随机种子（确保可复现性）。对问卷设计的建议：(1) Q16（格式覆盖面）可加入 "QMC 统计误差元数据" 作为期望的扩展功能而非独立格式；(2) Q17（首个适配器）的考虑应包含 QMC 输出的误差场处理需求；(3) D 模块（引擎无关工作流）的元数据规范中，应预留 stochastic 字段（含 error_type、confidence_interval、num_samples 等子字段）；(4) 如果受访者群体部分使用 QMC/QMC 类方法，F 模块可追问 "您的可视化工作流如何处理统计误差？"。</p>
<h2>阅读后回填</h2>
<ul><li>对问卷题目或选项的影响：Q16（格式覆盖面）可加入 "QMC 统计误差元数据" 需求；Q17（首个适配器）应考虑误差场可视化优先级；D 模块元数据规范讨论中需包含随机误差字段。</li><li>可转化为访谈追问的内容：是否在可视化中包含误差信息？误差带/误差场对科学沟通是否重要？QMC 用户使用什么工具查看波函数和密度（VMD、Matplotlib 等）？</li><li>关键证据页码/图表：Abstract (p.1) — 与 PySCF 集成；Eq.1 (Section II.A, p.2) — MSJ 波函数形式；Section I (p.1) — 能量密度和密度矩阵引用（Refs.26-28）；Section I (p.2) — 跨平台并行化能力。</li><li>仍未解决的问题：PyQMC 目前是否直接支持 .cube 输出尚不明确；QMC 密度的网格评估性能数据缺乏；误差场的存储格式和可视化策略在论文未讨论。</li></ul>
