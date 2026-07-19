# seritan_span_2021

BibTeX key：`seritan_span_2021`

## 摘要

Abstract TeraChem was born in 2008 with the goal of providing fast on‐the‐fly electronic structure calculations to facilitate ab initio molecular dynamics studies of large biochemical systems such as photoswitchable proteins and multichromophoric antenna complexes. Originally developed for videogaming applications, graphics processing units (GPUs) offered a low‐cost parallel computer architecture that became more accessible for general‐purpose GPU computing with the release of CUDA in 2007. The evaluation of the electron repulsion integrals (ERIs) is a major bottleneck in electronic structure codes and provides an attractive target for acceleration on GPUs. Thus, highly efficient routines for evaluation of and contractions between the ERIs and density matrices were implemented in TeraChem. Electronic structure methods were developed and implemented to leverage these integral contraction routines, resulting in the first quantum chemistry package designed from the ground up for GPUs. This GPU acceleration makes TeraChem capable of performing large‐scale ground and excited state calculations in the gas and condensed phase. Today, TeraChem's speed forms the basis for a suite of quantum chemistry applications, including optimization and dynamics of proteins, automated and interactive chemical discovery tools, and large‐scale nonadiabatic dynamics simulations. This article is categorized under: Electronic Structure Theory {\textgreater} Ab Initio Electronic Structure Methods Software {\textgreater} Quantum Chemistry Structure and Mechanism {\textgreater} Computational Biochemistry and Biophysics

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> TeraChem GPU 加速与交互式计算</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>TeraChem 的 GPU 加速对可视化数据生成速度和规模有何影响？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>实时/交互式计算</strong>（摘要, p.1）：TeraChem 的速度"forms the basis for automated and interactive chemical discovery tools"——这使得"实时可视化"成为可能，即计算完成后立即可视化。</li>
<li><strong>大规模非绝热动力学</strong>（Section, p.6-7）："several dozen trajectories can be run for hundreds of femtoseconds using a multireference correlation method"——大规模轨迹产生大量需要可视化的数据。</li>
<li><strong>Python socket 接口</strong>（Section, p.3）："a new language-agnostic socket-based interface allows TeraChem to run as a server for single-point calculations, providing access to fast electronic structure from high-level languages like Python"——这意味着可视化工具可以直接通过 socket 从 TeraChem 获取数据，无需文件解析。</li>
<li><strong>GPU 原生设计</strong>：TeraChem 是"the first quantum chemistry package designed from the ground up for GPUs"——GPU 加速使大体系（数百原子）的高精度方法计算成为可能。</li>
</ul>
<h3>问题 2</h3>
<p>TeraChem 的 Molden 输出和 NBO 接口提供了什么文件格式先例？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>Molden 格式</strong>（Section 2, p.4）："TeraChem Molden outputs from spherical basis SCF calculations can be used to run simplified TD-DFT calculations with the sTDA program"——Molden 格式是量子化学中通用的分子轨道/密度格式，被多个可视化和分析工具支持（如 Molden 本身、Gabedit、Avogadro）。</li>
<li><strong>NBO 接口</strong>（Section 2, p.4）："TeraChem is interfaced with NBO 6.0 for more advanced natural bond order (NBO) and natural population analyses"——NBO 分析产生自然轨道、自然键轨道等科学语义，可被可视化。</li>
<li><strong>对引擎无关工具的启示</strong>：Molden 格式已是一个成功"跨程序标准化格式"的案例——它被多个 QC 程序和支持。这为问卷 D 模块提供了"标准化格式可行性"的实证。</li>
</ul>
<h3>问题 3</h3>
<p>socket 接口和"TeraChem as server"模式对引擎无关可视化的架构启示是什么？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>计算-可视化分离</strong>：TeraChem 的 socket 接口将计算引擎与前端分离——这与"引擎无关可视化"的核心架构完全一致。</li>
<li><strong>语言无关性</strong>："language-agnostic socket-based interface"意味着可视化工具可以使用任何语言编写，只需实现 socket 通信协议。</li>
<li><strong>工作流集成</strong>："deploy TeraChem in flexible workflows on modern distributed computing resources"——分布式环境中，计算和可视化可能在不同机器上执行。</li>
<li><strong>对比 PySCF</strong>：PySCF 通过在同一个 Python 进程中调用实现类似"无缝集成"，而 TeraChem 通过 socket 实现进程间通信——两种不同的"引擎无关"架构方案。</li>
</ul>
<h2>阅读后回填</h2>
<ul>
<li>对问卷题目或选项的影响：
  <ul>
  <li>Q16（格式覆盖面）：Molden 格式是必须列出的跨程序格式。</li>
  <li>D 模块（引擎无关）：TeraChem 的 socket 接口是"引擎无关"工作流的典型案例，可作为问题背景介绍。</li>
  </ul>
</li>
<li>可转化为访谈追问的内容：
  <ul>
  <li>用户是否使用 TeraChem 的 socket 接口与 Python 交互？</li>
  <li>用户使用哪些工具可视化 TeraChem 的 Molden 输出？</li>
  <li>GPU 加速是否改变了用户对"可接受等待时间"的预期？</li>
  </ul>
</li>
<li>关键证据页码/图表：
  <ul>
  <li>Section 2 (p.4)：Molden 输出和 NBO 接口。</li>
  <li>Section (p.3)：socket 服务器接口描述。</li>
  <li>Figure 8 (p.7)：大规模 AIMS 轨迹。</li>
  </ul>
</li>
<li>仍未解决的问题：
  <ul>
  <li>TeraChem socket 接口是否支持波函数信息（轨道系数、密度）的传输？</li>
  <li>Molden 格式是否覆盖 TeraChem 支持的所有方法？</li>
  </ul>
</li>
</ul>
<p><strong>总结：</strong> TeraChem (Seritan et al., 2020) 是首个从头 GPU 设计的量子化学软件包。对本项目的关键启示：(1) TeraChem 的 socket 服务器接口（Section 2, p.4）提供了"语言无关的引擎-前端分离"架构——这是引擎无关工作流的一个直接范例，计算引擎和可视化前端通过标准协议通信，而非文件交换；(2) Molden 格式输出（Section 2, p.4）证明了一个跨程序标准化格式（Molden）可以成功地被多个 QC 程序采用（TeraChem、ORCA、MOLPRO 等都支持 Molden 输出），这与 QCSCHEMA 和 JSON 接口一起构成了"标准化格式可行性"的强证据；(3) GPU 加速使大规模 AIMD 和非绝热动力学成为可能，产生大量轨迹和激发态数据需要可视化；(4) 与 PySCF 的纯 Python 路线不同，TeraChem 的 C++/CUDA 核心通过 socket 暴露给 Python 的架构设计，是问卷 D 模块中"计算后端与可视化前端分离"这一概念的具体实证。</p>
