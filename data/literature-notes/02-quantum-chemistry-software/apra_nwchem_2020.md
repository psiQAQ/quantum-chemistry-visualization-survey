# apra_nwchem_2020

BibTeX key：`apra_nwchem_2020`

## 摘要

Specialized computational chemistry packages have permanently reshaped the landscape of chemical and materials science by providing tools to support and guide experimental efforts and for the prediction of atomistic and electronic properties. In this regard, electronic structure packages have played a special role by using first-principle-driven methodologies to model complex chemical and materials processes. Over the past few decades, the rapid development of computing technologies and the tremendous increase in computational power have offered a unique chance to study complex transformations using sophisticated and predictive many-body techniques that describe correlated behavior of electrons in molecular and condensed phase systems at different levels of theory. In enabling these simulations, novel parallel algorithms have been able to take advantage of computational resources to address the polynomial scaling of electronic structure methods. In this paper, we briefly review the NWChem computational chemistry suite, including its history, design principles, parallel tools, current capabilities, outreach, and outlook.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> NWChem 大规模并行与开源</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>

<h2>阅读问题</h2>

<h3>问题 1</h3>
<p>NWChem 的主要用户任务和并行规模与桌面量子化学用户有何不同？</p>
<p><strong>回答：</strong></p>
<ul>
  <li><strong>超算定位（Section I, Abstract 及 p.15-17）：</strong> NWChem 从设计之初就是面向"massively parallel processing supercomputers"的电子结构程序。论文强调"novel parallel algorithms have been able to take advantage of computational resources to address the polynomial scaling of electronic structure methods"（Abstract）。</li>
  <li><strong>大规模并行规模（Section VII, p.15-17）：</strong><ul>
    <li>Section VII.C (CCSD(T))：CCSD(T) 实现在 ORNL 的 Jaguar 系统的 223,200 个处理器上达到 1.39 PetaFLOP/s（双精度）的持续性能（p.16）。</li>
    <li>Section VII.D (TCE)：TCE 实现了 CC/EOMCC/LR-CC 对 1000-1300 轨道的计算，在 1000-8000 核上实现满意扩展性。</li>
    <li>Section VII.A (DFT)：C240 分子 PBE0/6-31G*（3600 基函数）并行标度测试在 Cascade 超算上完成。</li>
    <li>Section VII.B (TDDFT)：Au20+80Ne（1840 基函数）LR-TDDFT 计算 100 个激发态。</li>
  </ul></li>
  <li><strong>与桌面 QC 用户的差异：</strong><ul>
    <li>NWChem 用户不是运行单机/桌面程序，而是使用超算批处理系统（PBS/Slurm 等），提交包含数百至数千核的作业。</li>
    <li>典型任务不是单个小分子的单点/优化，而是大体系（数百原子+）的高精度计算或高通量扫描。</li>
    <li>输入通过命令行脚本或作业提交系统，而非图形界面。</li>
    <li>输出分散在超算文件系统中，可视化后处理通常在本地工作站或 web 界面完成。</li>
  </ul></li>
  <li><strong>对 survey 的启示：</strong> 问卷 E 模块（部署）必须包含"超算/集群环境"选项，并且需要评估用户在这种环境下的数据传输模式（scp、Globus、NFS 挂载等）。</li>
</ul>

<h3>问题 2</h3>
<p>大型体系的体场输出、存储和传输在哪一步成为瓶颈？</p>
<p><strong>回答：</strong></p>
<ul>
  <li><strong>Cube 文件生成（Section V, p.16；Section VIII.A, p.18）：</strong> 论文确认 "NWChem is able to export electrostatic potential and charge densities with the Gaussian cube format and can use the Molden format to write or read molecular orbitals"（Section VIII.A, p.18）。对于大体系（如水簇计算），单帧 cube 文件可能达到 GB 量级。</li>
  <li><strong>并行 I/O 架构（Section III, p.6-7；Section IV, p.7-8）：</strong><ul>
    <li>NWChem 使用"disk-resident database"作为模块间共享数据的机制（Section III, p.6："independent modules of NWChem share data only through a disk-resident database"）。</li>
    <li>部分变换积分存储在磁盘上（Section IV 提及 "partially transformed integrals are stored on disk, multi-passing as necessary"）。</li>
    <li>TCE 使用非零平铺（tiles）的哈希表记录存储地址，用于大规模并行计算（Section IV, p.7-8）。</li>
    <li>并行 I/O 能力在 GA（Global Arrays）工具包中实现，但论文未提供详细的 I/O 带宽数据。</li>
  </ul></li>
  <li><strong>存储瓶颈分析：</strong><ul>
    <li><em>计算阶段：</em> 积分存储和中间量可能在超算本地高速文件系统（如 Lustre/GPFS）上，I/O 带宽通常是足够的，但等待时间可能是瓶颈。</li>
    <li><em>后处理阶段：</em> cube 文件通常由后处理步骤（Section VIII.A 列出的工具）生成，写入共享文件系统。对于 AIMD 轨迹，每帧存储一个 cube 文件是不现实的。</li>
    <li><em>传输阶段：</em> 从超算传输 cube 文件到本地可视化工作站可能是最大的实际瓶颈——论文未讨论此问题，但这正是 survey 需要填补的空白。</li>
  </ul></li>
  <li><strong>NWChemEx 的潜在改进（Section VIII.C, p.19）：</strong> NWChemEx 项目正在开发新一代 CC 方法（基于 Cholesky 分解的 CCSD/T、DLPNO-CC、embedding 方法），这些方法有望减少中间数据的存储需求。</li>
  <li><strong>对 survey 的启示：</strong> 问卷应询问用户体场数据的典型大小（MB/GB/TB）和传输瓶颈（"计算太慢"vs"文件太大"vs"网络太慢"）。</li>
</ul>

<h3>问题 3</h3>
<p>HPC 用户对 CLI、批处理、离线部署和可视化后处理的优先级应如何测量？</p>
<p><strong>回答：</strong></p>
<ul>
  <li><strong>CLI 和批处理模式（Section II, p.2-3；Section III, p.4-6）：</strong><ul>
    <li>NWChem 使用 Generic Task Interface 控制执行流："Identify and open the input file → Complete the input processing"（Section II, p.2）。这是纯 CLI/批处理模式，没有内置 GUI。</li>
    <li>输入通过文本输入文件定义（"these independent modules of NWChem share data only through a disk-resident database"——Section III, p.6）。</li>
    <li>重启功能支持长时间作业："Restart capabilities are available for most modules. For example, SCF generated files (run-time database and molecular orbitals) can be used for restart"（PDF 位置 ~24912）。</li>
  </ul></li>
  <li><strong>离线部署需求（Section VIII.A, p.18）：</strong><ul>
    <li>NWChem 本身不需要图形界面，可在无显示器的超算节点上运行。</li>
    <li>外部前端工具（ECCE 等）提供 GUI 能力（Section VIII.A："NWChem initially developed its own graphical user interface called the Extensible Computational Chemistry Environment"），但这些是独立于 NWChem 运行的。</li>
    <li>超算节点通常无互联网连接，可视化工具需要支持离线部署。</li>
  </ul></li>
  <li><strong>可视化后处理的优先级（Section VIII.A, p.18-19）：</strong><ul>
    <li>NWChem 的可视化后处理通过外部工具完成：VMD、ChemCraft、Molden 等被引用（Section VIII.A 及参考文献）。</li>
    <li>"electrostatic potential and charge densities with the Gaussian cube format" 和 "Molden format" 是两种标准输出——这说明用户优先需要 cube/Molden 解析功能。</li>
    <li>论文结尾的参考文献（~156298）引用 ChemCraft 作为"Graphical software for visualization of quantum chemistry computations"，证明了用户依赖第三方可视化工具。</li>
  </ul></li>
  <li><strong>优先级测量建议：</strong>
    <ul>
      <li><strong>第一优先级：</strong> CLI 批处理模式——NWChem 用户需要能够在纯文本/脚本环境中运行可视化工具，无需 GUI。</li>
      <li><strong>第二优先级：</strong> 标准格式支持（cube、Molden）——这些是 NWChem 直接支持的标准格式。</li>
      <li><strong>第三优先级：</strong> 远程后处理——从超算本地文件系统读取 cube/场数据，在本地或 web 界面渲染。</li>
      <li><strong>第四优先级：</strong> 离线部署——可视化工具包应可在无互联网访问的超算节点上安装和运行。</li>
    </ul>
  </li>
</ul>

<h2>阅读后回填</h2>
<ul>
  <li><strong>对问卷题目或选项的影响：</strong>
    <ul>
      <li>Q16（格式覆盖面）：NWChem 的 cube 文件支持和 Molden 格式必须列出。</li>
      <li>B 模块（信任度）：NWChem 的 DOE 背景和 ECL 2.0 许可提供了不同于商业或社区开源的模式——政府支持的学术开源。</li>
      <li>C 模块（可视化任务）：对于 NWChem 用户，"体数据/等值面"（cube 文件）和"能带结构/DOS 图"可能比"分子轨道"更重要。</li>
      <li>E 模块（部署）：必须包含"超算/集群"选项，并细分"CLI 批处理 / Web 界面 / 本地客户端"的使用偏好。</li>
      <li>D 模块：Global Arrays (GA) 工具包被多个 QC 程序共用，是"底层基础设施跨引擎共享"的成功案例。</li>
    </ul>
  </li>
  <li><strong>可转化为访谈追问的内容：</strong>
    <ul>
      <li>NWChem 用户当前使用哪些可视化工具（VMD? ChemCraft? Molden?）？</li>
      <li>从超算传输 cube 文件到本地工作站的典型流程和时间？</li>
      <li>用户是否需要"在超算节点上直接生成可视化输出"的功能？</li>
      <li>ECL 2.0 许可是否影响用户对 NWChem 的采用决策？</li>
      <li>Common Component Architecture (CCA) 的教训——标准化接口曾因太重而被弃用（Section VIII.B, p.19），这对引擎无关工具的设计有何警示？</li>
    </ul>
  </li>
  <li><strong>关键证据页码/图表：</strong>
    <ul>
      <li>Section II (p.2-3)：Generic Task Interface 和模块化设计。</li>
      <li>Section III (p.4-6)：Global Arrays 并行工具包。</li>
      <li>Section V (p.16)：Gaussian cube 文件生成、能带结构和 DOS。</li>
      <li>Section VII (p.15-17)：并行性能数据——C240 DFT、Au20+80Ne TDDFT、(H2O)24 CCSD(T) 1.39 PFLOP/s。</li>
      <li>Section VIII.A (p.18-19)：外部接口、CCA 组件架构、NWChemEx 路线。</li>
      <li>PDF 位置 ~24912：重启功能和检查点机制。</li>
      <li>PDF 位置 ~156298：ChemCraft 等第三方可视化工具引用。</li>
      <li>Fig. 5, 6, 7 (p.15-16)：并行标度图表。</li>
    </ul>
  </li>
  <li><strong>仍未解决的问题：</strong>
    <ul>
      <li>NWChem 是否有结构化的 JSON/XML 输出接口？论文未提及。</li>
      <li>cube 文件生成覆盖哪些方法（仅在 Section V 列出，但未详细说明覆盖范围）？</li>
      <li>GA 工具包是否已有 Python 绑定？论文未涉及 Python 后处理。</li>
      <li>CCA 框架已被弃用——NWChem 是否有其他标准化的输出格式？</li>
      <li>NWChem 的 disk-resident database 格式是否开放文档化？</li>
    </ul>
  </li>
</ul>

<p><strong>总结：</strong> NWChem (Aprà et al., 2020) 是 DOE 国家实验室开发的面向超算并行的电子结构软件。对本项目的关键启示：(1) NWChem 在 223,200 处理器上实现 1.39 PFLOP/s（Section VII.C, p.16）的规模展示了"超算级 QC"与桌面 QC 的根本差异——问卷 C 模块和 E 模块必须区分这两种使用场景；(2) Gaussian cube 格式和 Molden 格式（Section V, p.16；Section VIII.A, p.18）是 NWChem 的可视化输出标准——但论文揭示了一个关键空白：中间数据的 disk-resident database 存储格式与最终 cube 文件之间缺乏灵活的中间格式，导致大体系场数据的处理效率低下；(3) Global Arrays 工具包被多个 QC 程序跨项目复用（Section III, p.4-6），成功证明了"底层基础设施可以引擎无关"的理念——但 CCA（Common Component Architecture）的失败（Section VIII.B, p.19：因过于笨重而被弃用）提醒我们：标准化接口必须轻量化，过重的基础设施反而会阻碍采用；(4) NWChemEx 正在开发基于 Cholesky 分解和 DLPNO-CC 的新一代方法（Section VIII.C, p.19）——这些方法有望改变中间数据的产生和存储方式；(5) 从 ECL 2.0 许可的视角看，NWChem 提供了"政府支持的学术开源"信任模式——与 Q-Chem 的商业+学术模式、PySCF 的纯社区模式形成三足鼎立的对比。</p>
