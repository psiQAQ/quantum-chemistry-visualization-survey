# bjornsson_span_2026

BibTeX key：`bjornsson_span_2026`

## 摘要

ABSTRACT We introduce ASH, a multi‐scale, multi‐theory modeling program for quantum mechanics (QM), molecular mechanics (MM), and hybrid calculations, written in the Python programming language. ASH is written in response to the increasingly diverse computational chemistry software landscape that features more QM and MM programs than ever before, and with machine learning interatomic potentials (MLIP) further changing the way modern computational chemistry is being performed. ASH is a Python library that intentionally separates computational chemistry jobs (geometry optimizations, frequency calculations, molecular dynamics, surface scans, reaction paths, etc.) from the QM, MM, or ML method (calculated by the specialized QM or MM programs or ML libraries). By keeping the jobs separate from the Hamiltonian, a highly flexible computational chemistry environment emerges that can be used in workflows involving QM methods (using interfaces to many different QM programs), classical MM methods with multiple force fields (via an interface to the OpenMM library), machine‐learning potentials, or hybrid methods. ASH is especially powerful as a program for performing hybrid simulations: including QM/MM, QM/ML, ML/MM, QM + ML, or ONIOM calculations for proteins, solvated molecules, or molecular crystals. Molecular dynamics and enhanced sampling can be performed using any level of theory allowing for highly flexible free‐energy simulations (such as metadynamics) enabled by interfaces to algorithms in OpenMM and Plumed. There are flexible interfaces to many QM programs such as ORCA, xTB, pySCF, CP2K, MRCC, Turbomole, CFour, and many others.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 多尺度、多理论与组合工作流</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>ASH 如何组合多个理论层级和外部程序，任务依赖与数据传递如何表示？</p>
<p><strong>回答：</strong></p>
<p>ASH 的核心设计哲学是"将计算作业与理论方法分离"："ASH is a Python library that intentionally separates computational chemistry jobs (geometry optimizations, frequency calculations, molecular dynamics, surface scans, reaction paths, etc.) from the QM, MM, or ML method (calculated by the specialized QM or MM programs or ML libraries)"（Abstract, p.1）。</p>
<ul>
<li><strong>作业-方法分离模式</strong>：用户定义一个"作业"（如几何优化、频率计算、MD），然后选择由哪个引擎实现 Hamiltonian——"By keeping the jobs separate from the Hamiltonian, a highly flexible computational chemistry environment emerges"（Abstract, p.1）。论文强调这种设计"gives the user access to both old and new methods through the same interface"（Section 1 区域）。</li>
<li><strong>多引擎接口</strong>：支持 ORCA、xTB、pySCF、CP2K、MRCC、Turbomole、CFour 等 QM 程序（Abstract, p.1），以及 OpenMM 作为 MM 引擎。混合计算（QM/MM、QM/ML、ML/MM、QM+ML、ONIOM）是"especially powerful"的应用场景。</li>
<li><strong>任务依赖和数据传递</strong>：论文在引言部分讨论此架构时提到"a driver program with interfaces to different programs to handle complex workflows"。计算链中，每个作业的输出（如优化后的几何）自动传递给下一个作业作为输入。数据传递通过 Python 层面的接口实现——接口将作业参数转换为外部程序输入，并解析输出结果返回给 ASH 的作业管理器。</li>
</ul>
<p><strong>对本项目的启示</strong>：ASH 的"作业-方法分离"理念与我们的"引擎无关适配器"概念高度一致。我们也需要抽象出"可视化作业"（如等值面渲染、场线追踪、时间序列动画）与"数据源方法"（来自不同 QC 程序的 .cube 文件）之间的分离。</p>
<h3>问题 2</h3>
<p>多尺度结果可视化需要同时保存哪些区域、方法和耦合信息？</p>
<p><strong>回答：</strong></p>
<p>根据 ASH 的混合计算模型，多尺度可视化需要保存的信息包括：</p>
<ul>
<li><strong>区域定义</strong>：QM 区和 MM 区各自的原子坐标、电荷和结构信息。ASH 支持 QM/MM 计算，"including QM/MM, QM/ML, ML/MM, QM+ML, or ONIOM calculations for proteins, solvated molecules, or molecular crystals"（Abstract, p.1）。每个区域可能使用不同的计算方法。</li>
<li><strong>方法/理论层级</strong>：每个区域使用的方法（如 QM 区域的 DFT 泛函/基组、MM 区域的力场类型、ML 区域使用的神经网络势）。</li>
<li><strong>耦合信息</strong>：QM/MM 耦合方式（机械耦合 vs 静电耦合）、边界处理方案（link atoms、边界原子类型）。ASH 支持多种混合计算模式。</li>
<li><strong>分子轨道/密度信息</strong>：QM 区域的波函数、轨道系数（用于电子密度可视化）、静电势（ESP）格点数据。</li>
<li><strong>随时间演化的轨迹</strong>：ASH 支持"Molecular dynamics and enhanced sampling can be performed using any level of theory"，这意味着多尺度 MD 轨迹数据也需要保存每个时间步的上述信息。</li>
</ul>
<p><strong>关键启示</strong>：可视化多尺度结果时，必须将每个区域的电子结构信息与区域边界信息关联存储。我们的 OpenVDB 格式可扩展为支持多区域标签——每个体素可标记属于 QM、MM 或缓冲区域，以便在 Blender 中对不同区域使用不同的可视化渲染方式。</p>
<h3>问题 3</h3>
<p>用户更需要预置流程还是可编程组合，二者面向哪些不同群体？</p>
<p><strong>回答：</strong></p>
<p>ASH 的设计明确偏向<strong>可编程组合</strong>，面向的是有一定 Python 编程能力的用户（比 AQME/WebMO 面向的用户群体更技术型）：</p>
<ul>
<li><strong>Python 库范式</strong>：ASH 是"a Python library"（Abstract, p.1），不是 GUI 或 Web 应用。用户通过 Python 脚本组合作业、方法和引擎。这与 WebMO/"点击即用"模式形成鲜明对比。</li>
<li><strong>灵活性的优势</strong>："a highly flexible computational chemistry environment emerges"（Abstract, p.1）——Python 库方式提供了最大的组合灵活性，允许用户创建任意复杂的多步骤工作流。</li>
<li><strong>面向群体</strong>：ASH 面向的是"需要处理越来越多样化的计算化学软件生态"的研究人员，特别是有跨方法、跨程序需求的专家用户。论文引言描述的计算化学软件生态日益分化——"increased specialization...new codes dedicated to specialized methodology"——应对这种分化需要可编程的组合工具而非固定预置流程。</li>
<li><strong>预置流程的缺失</strong>：ASH 不提供预配置的工作流模板库（如 AQME 的示例工作流或 WebMO 的预置计算类型），这降低了新用户的易用性。</li>
</ul>
<p><strong>跨论文对比</strong>：存在一个从"预置流程"到"可编程组合"的谱系——WebMO（完全预置）→ AQME（半预置半可编程）→ ASH（完全可编程）→ cclib/libxc（底层库）。问卷应测量用户在这个谱系上的位置。</p>
<p><strong>对本项目的启示</strong>：我们的适配器应同时支持两种模式：(1) CLI 一键转换（面向 AQME/WebMO 类型用户）；(2) Python API 可编程集成（面向 ASH 类型用户）。两者的接口应基于同一底层库。</p>
<h2>阅读后回填</h2>
<ul>
<li><strong>对问卷题目或选项的影响：</strong> 模块 D（引擎无关工作流概念）应包含一个"预置流程 vs 可编程 API"的偏好测量；模块 A 应包含工作流工具的使用频率；模块 E（PySCF 次级变量）可包含"你是否在多尺度（QM/MM）计算中使用 PySCF？"的问题。</li>
<li><strong>可转化为访谈追问的内容：</strong> "你使用的是预配置工作流还是编写自己的脚本协调多程序计算？这对你的可视化需求有什么影响？" "如果你需要可视化 QM/MM 结果，你希望同时看到 QM 区的电子密度和 MM 区的原子表示吗？"</li>
<li><strong>关键证据页码/图表：</strong> Abstract p.1（核心设计哲学：jobs-Hamiltonian 分离 & 引擎接口列表）；Abstract p.1 中 pySCF 被列为支持的 QM 程序——这直接验证了整个项目选择 PySCF 作为目标程序的合理性。</li>
<li><strong>仍未解决的问题：</strong> ASH 论文不包含可视化功能——ASH 专注于计算和执行层，不直接处理结果可视化。这意味着可视化必须通过外部工具完成，这与我们的项目目标一致。</li>
</ul>
<h2>总结</h2>
<p>ASH（2026 年发表）是最新、最前沿的多尺度多理论计算框架之一，其核心设计是"作业-方法分离"。对本项目的关键启示：(1) ASH 直接支持 pySCF（Abstract, p.1），与我们选择 PySCF 作为目标程序完全一致，进一步验证了项目的技术方向；(2) ASH 的 Python 库范式 vs AQME 的预置管道 vs WebMO 的 Web 界面形成了完整的用户群体谱系——我们的可视化适配器需要同时服务这三个群体；(3) 多尺度计算（QM/MM/ML）的可视化需要层次化元数据——每个区域的原子组成、方法、密度数据、耦合方式——这对我们的科学元数据模型提出了更高的要求；(4) ASH 支持 QM/ML 和 ML/MM 混合计算（Abstract, p.1），提示未来可视化管道还需支持非 QC 方法（如机器学习势）与 QC 密度场的混合可视化。</p>
