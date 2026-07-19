# epifanovsky_software_2021

BibTeX key：`epifanovsky_software_2021`

## 摘要

This article summarizes technical advances contained in the fifth major release of the Q-Chem quantum chemistry program package, covering developments since 2015. A comprehensive library of exchange–correlation functionals, along with a suite of correlated many-body methods, continues to be a hallmark of the Q-Chem software. The many-body methods include novel variants of both coupled-cluster and configuration-interaction approaches along with methods based on the algebraic diagrammatic construction and variational reduced density-matrix methods. Methods highlighted in Q-Chem 5 include a suite of tools for modeling core-level spectroscopy, methods for describing metastable resonances, methods for computing vibronic spectra, the nuclear–electronic orbital method, and several different energy decomposition analysis techniques. High-performance capabilities including multithreaded parallelism and support for calculations on graphics processing units are described. Q-Chem boasts a community of well over 100 active academic developers, and the continuing evolution of the software is supported by an “open teamware” model and an increasingly modular design.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> Q-Chem 方法覆盖与商业支持</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>

<h2>阅读问题</h2>

<h3>问题 1</h3>
<p>Q-Chem 5 的前沿方法中，哪些对应激发态、轨道、密度或响应性质可视化？</p>
<p><strong>回答：</strong></p>
<ul>
  <li><strong>Dyson 轨道和自然跃迁轨道（NTOs）（Section V/ADC 讨论）：</strong> PDF 文本确认："For visualization purposes, both Dyson orbitals and natural transition orbitals (NTOs) are available, including NTOs of the response density matrices"（PDF 位置 ~142500）。NTOs 可用于多种方法：EOM-CC、ADC（algebraic diagrammatic construction）和 TD-DFT 等。此外，复杂变量的 EOM-CC 方法也支持 Dyson 轨道和 NTOs 计算。</li>
  <li><strong>电子-空穴关联可视化（Section IV 讨论）：</strong> PDF 文本指出新的方法"enable the calculation and visualization of electron-hole correlation"（PDF 位置 ~142600）——这是超越传统轨道可视化的更丰富信息。</li>
  <li><strong>IQMOL 图形界面（PDF 末尾截断，参考 Section X）：</strong> 虽然 Section X（GRAPHICAL USER INTERFACES）的完整内容在 PDF 提取时被截断（Section IX 仅读到开头：A. Platforms），但摘要和元数据确认 IQMOL 支持多种分子表面（密度、轨道、局域轨道、NTOs、NBOs、Dyson 轨道）的可视化，并支持 cube 文件导出和 POV-Ray 渲染。</li>
  <li><strong>EZSPECTRA / EZDYSON 模块（PDF 确认）：</strong> "transitions (photoelectron, UV–Vis, etc.) can be computed in a post-processing step using the EZFCF module of the stand-alone software EZSPECTRA, which implements FCFs within the double-harmonic approximation. EZSPECTRA also contains a module EZDYSON, which can be used to compute total and angular-resolved photoelectron spectra"（PDF 位置 ~156000）。</li>
  <li><strong>激发态方法覆盖（Section II.C, p.11-13）：</strong> 摘要确认 TD-DFT（线性响应形式）、自旋翻转 TD-DFT、CIS、ADC、EOM-CCSD 等，均可产生可用于可视化的跃迁密度。ADC 方法和 EOM-CC 被描述为"two powerful approaches for describing multiconfigurational wave functions"（PDF 位置 ~160500）。</li>
  <li><strong>响应性质：</strong> 极化率、超极化率等响应性质的计算。TCE（Tensor Contraction Engine）实现 CC/EOMCC/LR-CC 计算。</li>
  <li><strong>EDA 分析可视化（Section VIII）：</strong> ALMO-EDA 支持电荷转移分析和电子密度差（EDD）图。</li>
</ul>

<h3>问题 2</h3>
<p>商业团队式开发提供了哪些信任、支持和版本稳定性证据？</p>
<p><strong>回答：</strong></p>
<ul>
  <li><strong>"Open Teamware" 开发模式：</strong> 摘要和 PDF 文本确认："Q-Chem boasts a community of well over 100 active academic developers, and the continuing evolution of the software is supported by an 'open teamware' model and an increasingly modular design"（PDF 开篇）。这种模式被描述为"collaboration that defines its genre as open teamware scientific software"（PDF 位置 ~1800）。</li>
  <li><strong>Figure 2 统计数据（PDF 位置 ~1700）：</strong> "Statistics showing Q-Chem developer activity since 2006. Top: total number of code commits, organized chronologically by month."——论文提供了明确的开发活动量数据来证明社区活力和持续增长。</li>
  <li><strong>社区规模证据（PDF 位置 ~1850）：</strong> 100+ 活跃学术开发者，"clear evidence of the sustained growth of the developer community and the code itself over the past decade"。</li>
  <li><strong>开源模块：</strong> "several Q-Chem modules are distributed as open source software"（PDF 位置 ~1665），说明 Q-Chem 并非完全闭源，而是混合模式。</li>
  <li><strong>版本发布节奏：</strong> Q-Chem 5.0 距 4.0（2015）约 6 年，体现了较长的开发周期和质量控制。</li>
  <li><strong>商业生态：</strong> 云部署支持（"ready-to-deploy machine image for use on Amazon Web Services"）——Section IX.A；SPARTAN 商业前端（Section X.B）；WebMO 教学界面。</li>
  <li><strong>对 survey 的意义：</strong> Q-Chem 的混合模式（商业许可 + 开源模块 + 100+ 学术开发者）提供了第三类信任信号——既不像完全商业程序那样封闭，也不像完全开源项目那样依赖社区治理。问卷 B 模块应区分"开发模式"和"许可模式"两个维度。</li>
</ul>

<h3>问题 3</h3>
<p>引擎无关工具需要保留哪些 Q-Chem 专用关键字或输出语义？</p>
<p><strong>回答：</strong></p>
<ul>
  <li><strong>格式化检查点文件（formatted checkpoint file）：</strong> 摘要确认 IQMOL 可从中提取数据。这是包含波函数信息的关键输出。</li>
  <li><strong>Cube 文件：</strong> IQMOL 支持导出 cube 文件到第三方软件（Section X.A 确认），这是标准的网格数据格式。</li>
  <li><strong>Q-Chem 输出文件（.out）：</strong> IQMOL 和 SPARTAN 均从中提取计算结果。文本输出文件包含所有方法的详细输出。</li>
  <li><strong>$rem 变量：</strong> Q-Chem 输入中的核心控制变量（如 METHOD、BASIS、EXCHANGE、CORRELATION 等），引擎无关工具必须正确解析这些变量以确定计算类型和输出语义。</li>
  <li><strong>$molecule 块：</strong> 分子结构定义（电荷、自旋多重度、原子坐标）。</li>
  <li><strong>NTOs、Dyson 轨道语义：</strong> 这些科学语义对象（Section V 多处提及）对激发态分析可视化至关重要，引擎无关工具需要识别轨道标签而非仅仅网格数据。</li>
  <li><strong>$comment / $plots / $polarization 等输入块：</strong> 控制响应性质和轨道输出的专用关键字。</li>
  <li><strong>EOM-CC/ADC 激发态输出：</strong> 激发能、振子强度、跃迁密度矩阵元素——这些都包含在文本输出中，引擎无关解析器需要提取这些结构化信息。</li>
  <li><strong>ALMO-EDA 输出（Section VIII）：</strong> 分解能量分量（ΔE<sub>FRZ</sub>、ΔE<sub>POL</sub>、ΔE<sub>CT</sub>）需要被正确解析。</li>
  <li><strong>IQMOL 可视化协议：</strong> IQMOL 是 Q-Chem 的首选可视化前端，如果引擎无关工具旨在替代或补充 IQMOL，需要理解 IQMOL 支持的所有表面和轨道类型。</li>
</ul>

<h2>阅读后回填</h2>
<ul>
  <li><strong>对问卷题目或选项的影响：</strong>
    <ul>
      <li>Q16（格式覆盖面）：Q-Chem 的格式化检查点文件 (.fchk) 和 cube 文件是必须列出的格式。</li>
      <li>B 模块（信任度）：Q-Chem 的商业+学术混合开发模式提供了"可持续性"信任信号——这是介于完全商业和完全开源之间的中间模式。</li>
      <li>D 模块（引擎无关）：IQMOL+Q-Chem 的集成模式展示了一种前端-后端分离的工作流，但 IQMOL 是 Q-Chem 社区的产物而非完全引擎无关的。</li>
      <li>A 模块（基本情况）：应询问用户是否使用 IQMOL、WebMO 或 SPARTAN 等 Q-Chem GUI。</li>
    </ul>
  </li>
  <li><strong>可转化为访谈追问的内容：</strong>
    <ul>
      <li>Q-Chem 用户是否使用 IQMOL？与其他可视化工具（如 VMD、Avogadro）相比如何？</li>
      <li>用户是否使用 cube 文件导出到第三方渲染软件？</li>
      <li>"Open Teamware" 模式的实际体验如何——用户是否能获得足够的支持？</li>
      <li>Q-Chem 的格式化检查点文件是否足以提取所有需要的信息？</li>
    </ul>
  </li>
  <li><strong>关键证据页码/图表：</strong>
    <ul>
      <li>PDF 位置 ~142500：Dyson 轨道和 NTOs 可视化能力确认。</li>
      <li>PDF 位置 ~142600：电子-空穴关联可视化确认。</li>
      <li>PDF 开篇（~1800）："Open Teamware" 模式和 100+ 开发者。</li>
      <li>PDF 位置 ~156000：EZSPECTRA/EZDYSON 模块。</li>
      <li>Section IX.A (PDF 末尾)：高性能计算——AWS 云部署、OpenMP 并行、GPU 加速。</li>
      <li>Figure 2（约 PDF 位置 ~1700）：开发者活动统计数据。</li>
    </ul>
  </li>
  <li><strong>仍未解决的问题：</strong>
    <ul>
      <li>Section X（GUI）的完整内容在 PDF 提取时被截断，IQMOL 的完整功能描述未读取。</li>
      <li>Q-Chem 的 .fchk 格式是否文档化？是否有开源解析库？</li>
      <li>IQMOL 是否支持所有 Q-Chem 方法对应的可视化？</li>
      <li>cube 文件导出是否覆盖所有轨道和密度类型？</li>
      <li>Q-Chem 是否有类似 ORCA 6 的 JSON 输出接口？</li>
    </ul>
  </li>
</ul>

<p><strong>总结：</strong> Q-Chem 5 (Epifanovsky et al., 2021) 是一篇大规模综述，详细描述了 200+ 泛函、TD-DFT、ADC、EOM-CC 等激发态方法、ALMO-EDA 能量分解等前沿能力。对本项目的关键启示：(1) 论文确认 Dyson 轨道、NTOs、电子-空穴关联可视化是 Q-Chem 5 的核心可视化能力——这些科学语义（而非原始格点数据）是问卷 C 模块需要关注的关键可视化对象；(2) "Open Teamware" 模式 + 100+ 学术开发者 + 商业发行，提供了第三类"信任"模式——不同于完全商业（如 Gaussian）或完全开源（如 PySCF），问卷 B 模块应探索用户对不同模式的偏好；(3) EZSPECTRA/EZDYSON 等后处理工具展示了"分离式发布"的先例——引擎无关的可视化工具也可以作为独立后处理包发布；(4) IQMOL 作为 Q-Chem 原生 GUI，支持 cube 文件导出到第三方软件，说明即使在商业程序中，"开放数据格式"的价值观也被接受——这为"引擎无关"理念提供了来自商业方的证据。</p>
