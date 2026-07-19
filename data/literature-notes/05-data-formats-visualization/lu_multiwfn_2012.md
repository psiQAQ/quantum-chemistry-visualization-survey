# lu_multiwfn_2012

BibTeX key：`lu_multiwfn_2012`

## 摘要

Abstract Multiwfn is a multifunctional program for wavefunction analysis. Its main functions are: (1) Calculating and visualizing real space function, such as electrostatic potential and electron localization function at point, in a line, in a plane or in a spatial scope. (2) Population analysis. (3) Bond order analysis. (4) Orbital composition analysis. (5) Plot density‐of‐states and spectrum. (6) Topology analysis for electron density. Some other useful utilities involved in quantum chemistry studies are also provided. The built‐in graph module enables the results of wavefunction analysis to be plotted directly or exported to high‐quality graphic file. The program interface is very user‐friendly and suitable for both research and teaching purpose. The code of Multiwfn is substantially optimized and parallelized. Its efficiency is demonstrated to be significantly higher than related programs with the same functions. Five practical examples involving a wide variety of systems and analysis methods are given to illustrate the usefulness of Multiwfn. The program is free of charge and open‐source. Its precompiled file and source codes are available from http://multiwfn.codeplex.com . © 2011 Wiley Periodicals, Inc. J Comput Chem, 2011

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 波函数分析与场类型</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>Multiwfn 用户最常分析哪些实空间函数、拓扑对象和轨道性质？</p>
<p><strong>回答：</strong>根据论文全文，Multiwfn集成了几乎所有重要的实空间函数分析：① 电子密度 ρ(r) 及其梯度范数 |∇ρ(r)|、拉普拉斯量 ∇²ρ(r)（第581页，公式1-3）；② 静电势 ESP（第581页，公式6）；③ 电子定域化函数 ELF（第582页，公式14-15）；④ 定域化轨道指示函数 LOL（第582页，公式17-18）；⑤ 约化密度梯度 RDG 与 sign(λ₂)ρ（第581页，公式9-10，用于弱相互作用可视化）；⑥ 平均局域电离能 Ī(r)（第582页，公式19）；⑦ 费米空穴与费米相关因子（第583页，公式20-22）。拓扑对象方面：支持电子密度临界点（CP）搜索——(3,-3)核吸引子、(3,-1)键 CP、(3,+1)环 CP、(3,+3)笼 CP，以及梯度路径和分子图（第583页；第587页Figure 1）。轨道性质方面：支持轨道组成分析（Mulliken/SCPA/Stout-Politzer/Hirshfeld分解）、态密度 DOS/PDOS/OPDOS 绘图（第586页）、以 Wiberg/Mayer/Mulliken 键级分析。</p>
<h3>问题 2</h3>
<p>其支持的 WFN/WFX/FCHK 等源格式各自保留或缺失哪些信息？</p>
<p><strong>回答：</strong>论文第585-586页 "Wavefunction types and file formats" 明确列出：① PROAIM 波函数文件（wfn）：最流行的波函数交换格式，Gaussian、GAMESS-US/UK、Firefly、Q-Chem 等支持，适用于需要波函数信息的分析；② AIM 扩展波函数文件（wfx）：Gaussian 09 B.01 起引入，作为 wfn 的扩展格式；③ Gaussian 格式化检查点文件（fch）：对于依赖基组展开的分析（如实空间函数计算）是必需的（mandatory），因为 fch 包含了完整的基组信息和 MO 系数矩阵；④ NBO 程序的 plot 文件：包含自然原子轨道/自然键轨道等信息；⑤ PDB 格式：仅含原子坐标，用于 promolecular 近似下的 RDG 和 sign(λ₂)ρ 计算；⑥ chg 格式（私有）：仅含原子坐标和电荷。关键区别：WFN/WFX 包含波函数信息但不含完整基组展开细节，FCHK 因包含完整基组和系数矩阵而成为实空间函数计算的必需格式。wfx 作为 wfn 的扩展增加了更多元数据。</p>
<h3>问题 3</h3>
<p>哪些分析结果应在 survey 中作为独立场类型，哪些可合并以控制问卷长度？</p>
<p><strong>回答：</strong>基于论文分析：应独立保留的类型——① 电子密度 ρ（最基础的场，不可替代）；② 静电势 ESP（广泛用于反应位点预测，第581页提到 GIPF 系列函数将 ESP 与宏观性质关联）；③ ELF/LOL（电子定域化，合为一个问题，因为二者定性可比，第582页指出 LOL 更清晰但 ELF 更广泛使用）；④ RDG + sign(λ₂)ρ（弱相互作用的彩色等值面分析，已形成标准方法，第581页表1）；⑤ 平均局域电离能 Ī(r)（与电负性、极化率、反应位点相关，第582-583页）。可合并的类型——① ∇ρ、∇²ρ、动能密度 K(r)/G(r) 等电子密度的导数/派生函数可归为"高级场选择"而非独立类型；② 费米空穴（六维函数，第582页指出难以可视化）可在高级部分提及但不必作为独立问题；③ 不同布居分析方法（Mulliken/Löwdin/Hirshfeld/ADCH 等，第583-584页）产生同类原子电荷信息，合并为一个问题。</p>
<h2>阅读后回填</h2>
<ul><li>对问卷题目或选项的影响：问卷模块 E（PySCF 次级变量）应考虑包括 ELF、ESP、电子密度拉普拉斯量作为常用场类型，同时将 RDG/sign(λ₂)ρ 组合作为弱相互作用专项分析提出。</li><li>可转化为访谈追问的内容：用户日常使用是否区分"基于电子密度的函数"（可实验观测）和"基于波函数的函数"（不可实验观测，第581页明确划分）？对 Grid data 精度要求通常多高（论文第588页提到采用指数截断阈值40确保性能）？</li><li>关键证据页码/图表：第581页表1（不同区域的 q、|∇q|、RDG 数值范围）、第587页 Figure 1（拓扑分析界面）、第588页表2（Multiwfn 性能对比显式显示了网格计算的效率优势）。</li><li>仍未解决的问题：Multiwfn 虽支持将网格数据导出为 Gaussian Cube 格式（第586页），但论文未讨论 Cube 格式在跨程序数据交换中的精度损失或元数据缺失问题——这正是本项目需要关注的格式互操作性问题。</li></ul>
<h2>总结</h2>
<p><strong>核心发现：</strong>Multiwfn 作为功能最全面的波函数分析工具，覆盖了从电子密度到 ELF/ESP/RDG 等20余种实空间函数、多种布居分析和键级方法、DOS/光谱模拟以及 QTAIM 拓扑分析。其效率通过 OpenMP 并行化和指数截断优化显著优于同类程序（第588页表2显示四核加速比达3.75-3.82倍）。</p>
<p><strong>与本项目的关联：</strong>Multiwfn 的"空白函数"（userfunc）设计（第586页）允许用户自定义任意实空间函数并计算网格数据——这为 PySCF 与 Blender 工作流提供了"用户自定义场"需求的前例。但 Multiwfn 的内置图形模块（基于 DISLIN）仅适合 2D 曲线/等值面图，无法实现 Blender 级别的 3D 体渲染和动画，这论证了项目定位的必要性。</p>
<p><strong>对问卷的具体建议：</strong>① 模块 C 和 E 应包含电子密度、ESP、ELF 作为"必须支持的场类型"选项；② RDG+sign(λ₂)ρ 应作为弱相互作用的组合场提出；③ 应询问用户对"自定义场函数"（类似于 userfunc 设计）的需求程度；④ 问卷应涉及用户对 WFN/WFX/FCHK 等源格式的熟悉程度，以评估格式转换需求。</p>
