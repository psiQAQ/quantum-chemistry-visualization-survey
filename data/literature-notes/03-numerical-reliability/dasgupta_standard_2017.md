# dasgupta_standard_2017

BibTeX key：`dasgupta_standard_2017`

## 摘要

Density‐functional approximations developed in the past decade necessitate the use of quadrature grids that are far more dense than those required to integrate older generations of functionals. This category of difficult‐to‐integrate functionals includes meta‐generalized gradient approximations, which depend on orbital gradients and/or the Laplacian of the density, as well as functionals based on B97 and the popular “Minnesota” class of functionals, each of which contain complicated and/or oscillatory expressions for the exchange inhomogeneity factor. Following a strategy introduced previously by Gill and co‐workers to develop the relatively sparse “SG‐0” and “SG‐1” standard quadrature grids, we introduce two higher‐quality grids that we designate SG‐2 and SG‐3, obtained by systematically “pruning” medium‐ and high‐quality atom‐centered grids. The pruning procedure affords computational speedups approaching a factor of two for hybrid functionals applied to systems of atoms, without significant loss of accuracy. The grid dependence of several popular density functionals is characterized for various properties. © 2017 Wiley Periodicals, Inc.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 积分网格、精度与性能</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>SG-2/SG-3 如何在剪枝、成本和精度之间取舍，适用元素和泛函边界是什么？</p>
<p><strong>回答：</strong>SG-2 和 SG-3 分别基于 (75,302) 和 (99,590) 网格进行系统剪枝（pruning），在靠近核的球形密度区和远离核的缓慢变化密度区减少角度积分点数（Section III.B, p.6）。SG-2 仅保留 31-39% 的原始点数，SG-3 保留 25-33%（p.3）。剪枝后计算加速约 2 倍（Figures 5-6, p.15），"speedup of 1.5-2.0 in the total SCF time for systems ranging in size from 200-1150 basis functions"。精度方面：原子化能剪枝误差 MUD 为 0.03 (SG-2) / 0.01 (SG-3) kcal/mol（Table II, p.21）；异构化能剪枝误差 0.03 (SG-2) / 0.01 (SG-3) kcal/mol（Table III, p.22）；键长剪枝误差 0.0002 / 0.00006 A（Section IV.C, p.9）。适用元素：论文定义了 H 到 Cl 的 SG-2/SG-3 参数（Table I, p.20），对稀有气体使用未剪枝网格，对 Cl 以外的元素定义为未剪枝的 (75,302)/(99,590) EML 网格（Section III, p.5）。<strong>泛函边界</strong>：SG-2 适用于大多数 meta-GGA 和 B97 系列泛函；SG-3 推荐用于 Minnesota 系列和含 B95 的泛函（Section V, p.16："recommend use of the SG-3 grid for the particularly-sensitive Minnesota functionals, and also for functionals that contain B95"）；GGA 泛函用 SG-1 即可。但 Minnesota 泛函的非共价相互作用势能面即使在 SG-3 上仍可能出现振荡（pp.13-14, Figure 3-4），只有非常稠密的 (250,974) 网格能完全消除。</p>
<h3>问题 2</h3>
<p>不同程序对同名或默认网格的实现是否足以直接比较？</p>
<p><strong>回答：</strong>论文明确说<strong>不足</strong>。理由：(1) 论文引言（p.2）明确指出"Although references to pruned versions of grids denser than SG-1 can be found in the literature, and in the online documentation for the GAMESS and Gaussian programs, <strong>the grid points themselves have not been published</strong>. As such, these cannot constitute standard integration grids that can be used to make comparisons and validations between electronic structure codes, at the level of machine precision." 这段话直接点明了跨程序比较的核心障碍。(2) 论文发现 Q-Chem 中 Gaussian 的 UltraFine (99,590) 与论文定义的 SG-3 在剪枝策略上不同（p.2：SG-3 是基于双指数径向量化 DE2，而 Gaussian 使用 Euler-Maclaurin 方案加不同的剪枝策略）。(3) 论文特别强调"the grid points themselves have not been published"（p.2），这意味着即使两个程序声称使用同名网格（如"UltraFine"），其实际网格点坐标和权重也可能不同。(4) 不同径向积分方案（Euler-Maclaurin vs. 双指数 DE2）进一步加剧了差异（Section II, p.3-4）。结论：仅靠网格名称无法保证跨程序的可比性，必须明确指定径向点数、角度点数、径向量化方案和剪枝策略。</p>
<h3>问题 3</h3>
<p>问卷元数据选项应记录网格名称、径向/角向点数还是程序原生关键字？</p>
<p><strong>回答：</strong>基于论文发现，建议问卷采用<strong>分级记录策略</strong>：(1) <strong>标准网格名称</strong>（如 SG-0, SG-1, SG-2, SG-3）——论文已经明确定义了这些标准网格的完整参数（Table I + 双指数径向量化），因此 SG-2/SG-3 可做为一个跨程序标准；(2) <strong>程序原生关键字</strong>（如 Gaussian 的 UltraFine/SuperFineGrid、Q-Chem 的 SG-2/SG-3）——用户最常见的使用方式；(3) <strong>径向/角向点数组合</strong>（如 (99,590)）——这是跨程序比较的最底层信息，但论文警告仅凭 (Nr, NΩ) 仍不足以完全复现，因为径向方案（Euler-Maclaurin vs. DE2）和剪枝策略不同（p.3-4, p.2）。<strong>建议问卷设计</strong>：在模块 C 中设置选择题，让用户选择他们通常记录网格信息的方式，选项包括(a) "我使用程序默认设置，不知道具体点数"、(b) "我记录程序关键字（如 UltraFine）"、(c) "我记录径向/角向点数"、(d) "我指定标准网格名称（如 SG-x）"。这本身就是考察用户对网格问题意识程度的行为问题。同时建议增加一个"您是否知道您常用程序的默认网格点数是多少？"的行为判断题。</p>
<h2>阅读后回填</h2>
<ul><li>对问卷题目或选项的影响：模块 C 需在"跨引擎比较障碍"题目中纳入网格差异作为独立选项；建议在模块 C/E 中询问用户是否知道 SG-1/SG-2/SG-3 标准网格。</li><li>可转化为访谈追问的内容：追问用户在可视化工作中是否遇到过因网格不足导致的场异常（如势能面振荡、电子密度异常特征）。</li><li>关键证据页码/图表：Figure 1 (p.2) —— Ar2 在 SG-1 和 (75,302) 上的振荡；Figures 3-4 (pp.13-14) —— 苯二聚体势能面在 Minnesota 泛函上的网格依赖；Table I (p.20) —— SG-2/SG-3 的完整参数；Section V (p.16) —— 推荐使用建议；p.2 引言段关于标准网格的声明。</li><li>仍未解决的问题：论文未讨论网格差异对三维可视化场（密度、轨道、ESP 等）的具体影响量级，但提供了充分的间接证据（非共价相互作用势能面的振荡表明密度梯度大但密度值小的区域对网格最敏感，这恰恰是可视化中最具化学意义的区域）。</li></ul>
<h2>论文总结</h2>
<p><strong>论文核心发现：</strong>本文系统开发了 SG-2（基于 75,302）和 SG-3（基于 99,590）两个标准剪枝积分网格，使用双指数径向量化（DE2）和 Lebedev 角向量化。剪枝后点数减少约 2/3，计算加速达 1.5-2 倍，各类分子性质（原子化能、几何、频率、超极化率、非共价相互作用能）的剪枝误差均远低于化学精度要求。关键发现：(1) Minnesota 系列泛函对网格极为敏感，非共价相互作用势能面在中等网格上仍出现振荡假象；(2) B97 系列泛函对网格敏感度较低；(3) 此前文献和程序文档中的剪枝网格参数从未正式发表过，无法作为跨程序比较的标准。</p>
<p><strong>与本项目的关联：</strong>论文关于"grid points themselves have not been published"无法形成跨程序标准的论述，直接支撑本项目引擎无关工作流的必要性。对可视化而言，非共价相互作用区域的网格敏感性直接意味着可视化场（ESP、密度梯度、ELF 等）在这些区域对网格质量高度敏感。推荐使用 SG-3（或至少明确的 (99,590) 未剪枝网格）作为跨程序三维场比较的最低网格标准。</p>
<p><strong>对问卷设计的具体建议：</strong>(1) 模块 B/C 应包含关于网格意识的行为问题（如"您是否知道您常用程序的默认网格点数？"）；(2) 模块 C 应询问"如果您进行跨程序可视化对比，您是否会确保使用相同的积分网格设置？"；(3) 建议加入一个案例判断题：展示一段因网格不足导致的电子密度异常可视化图片（类似 Figure 1 的振荡但可视化版），让受访者判断原因。</p>
