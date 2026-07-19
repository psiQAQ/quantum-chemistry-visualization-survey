# sun_recent_2020

BibTeX key：`sun_recent_2020`

## 摘要

PySCF is a Python-based general-purpose electronic structure platform that supports first-principles simulations of molecules and solids as well as accelerates the development of new methodology and complex computational workflows. This paper explains the design and philosophy behind PySCF that enables it to meet these twin objectives. With several case studies, we show how users can easily implement their own methods using PySCF as a development environment. We then summarize the capabilities of PySCF for molecular and solid-state simulations. Finally, we describe the growing ecosystem of projects that use PySCF across the domains of quantum chemistry, materials science, machine learning, and quantum information science.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 基准设计与信任证据</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>

<h2>阅读问题</h2>

<h3>问题 1</h3>
<p>高质量 DFT 基准数据库需要怎样的体系多样性、参考值、几何、基组和统计指标？</p>
<p><strong>回答：</strong>论文从五个关键维度给出了详细指南，并提供了Figure 3的决策流程图（p.6）：</p>
<p><strong>(1) 参考值精度</strong>（Section 3.1, p.5-7）：CCSD(T)是评估rungs 1-4泛函的最低参考标准——"the gold standard of quantum chemistry"。对困难体系需CCSDT(Q)或CCSDTQ5。最经济的策略是将CCSD(T)分为HF、CCSD、(T)三个组分分别处理（Equation 2, p.7）：CCSD(T)/Large ≈ CCSD(T)/Small + MP2/Large - MP2/Small。对多参考特征显著的系统需使用后CCSD(T)或多参考方法。W1-F12理论（Equation 3, p.8）使用cc-pV{D,T}Z-F12基组对各组分分别外推至CBS极限。</p>
<p><strong>(2) 数据集大小与多样性</strong>（Section 3.2, p.7-8）：需平衡体系规模与数据点数量。小型聚焦数据集（AE6/BH6各6个数据点）和大型通用数据库（W4-17含200个原子化能、GDB9含122k原子化能）各有优劣。必须确保各反应子集在代表性上平衡——以pericyclic反应势垒为例，应确保electrocyclic/cycloaddition/sigmatropic等子类数量均衡。论文引用了基于统计学和遗传算法的数据库缩减方法（Lynch & Truhlar开创）。</p>
<p><strong>(3) 参考几何</strong>（Section 3.3, p.8-9）：两个关键考虑：(a) 必须确认是局域极小值或一阶鞍点（通过频率分析）；(b) 推荐B3LYP/def2-TZVPP用于获取参考几何，其对W1-F12原子化能的影响仅约1 kJ/mol。几何精度大致遵循：DFT(rungs 2/3) → DFT(rungs 3.5/4) → DHDFT ≈ CCSD → CCSD(T)。对于大体系，rung 2/3的DFT方法更经济。</p>
<p><strong>(4) 基组</strong>（Section 3.4, p.9）：两种流派——"purist approach"要求四重zeta以逼近CBS极限评估DFT内在精度；"pragmatic approach"使用实际应用中最常用的三重zeta基组。推荐Jensen的pc-n系列和Ahlrichs的def2系列。DHDFT需要比常规DFT至少高一个角动量的基组。</p>
<p><strong>(5) 统计指标</strong>（Section 3.5, p.9-10）：建议同时报告MAD、RMSD、MSD、MAD/RMSD比值（正态分布约0.8，Geary检验，p.10）、最大正/负偏差。MAD≈0.8×RMSD对正态分布成立。MAD/RMSD值远低于0.8提示存在显著异常值。论文警告仅报告MAD而不包含RMSD可能"present the results in a more favorable light"。</p>

<h3>问题 2</h3>
<p>哪些基准证据足以支持跨程序比较，哪些只能说明特定方法在特定数据集上的表现？</p>
<p><strong>回答：</strong></p>
<p><strong>跨程序比较需满足的数据条件</strong>（Section 3.6, p.10）：</p>
<ul>
<li>所有物种的笛卡尔坐标（XYZ格式）；</li>
<li>几何优化所用理论水平；</li>
<li>电荷和多重度；</li>
<li>PES上的具体点（平衡态或过渡态）；</li>
<li>基准参考值及获取方法；</li>
<li>标准化文件格式和命名约定（"standardized file formats and naming conventions to enhance the transferability of the data across different platforms and software packages"）。</li>
</ul>
<p><strong>单数据集结论的局限性</strong>（Section 2.2, p.4-5）：论文分析了30项基准研究中的230种DFT方法，发现：(a) 40%（89种）仅出现在一项研究中；(b) 60%以上仅出现在三项或更少研究中——"this limited coverage underscores the need for more extensive benchmark studies"。论文特别警告Jacob's Ladder不能确保跨rung的系统性改进——以C40/C60富勒烯异构化能为例（引用文献[16, 17]），rung 3方法性能优于rungs 3.5和4方法；PAH异构化能中LDA方法SVWN5优于某些rungs 2-4方法。</p>
<p><strong>推荐：</strong>论文推荐引用GMTKN55（Goerigk et al., 2017）、MGCDB84（Mardirossian & Head-Gordon, 2017）等大型通用数据库的结论作为更可靠的跨程序比较依据。</p>

<h3>问题 3</h3>
<p>survey 的"独立基准"选项是否需要区分公开数据、可复现协议与单一论文结论？</p>
<p><strong>回答：</strong>是的，非常需要。论文Section 3.6 (p.10)明确提出了数据可用性的分层要求：</p>
<p><strong>(1) 公开数据</strong>——"the digital accessibility of benchmark data is essential for updating and conducting larger-scale benchmarking studies"。必须包含六个核心信息：(i) XYZ格式笛卡尔坐标；(ii) 几何优化理论水平；(iii) 电荷和多重度；(iv) PES具体点；(v) 基准参考值；(vi) 参考值获取理论水平。推荐将XYZ文件单独提供而非嵌入PDF。</p>
<p><strong>(2) 可复现协议</strong>——论文提供了Figure 3的决策流程图作为方法论指南，并推荐标准化文件格式和命名约定。额外建议共享的信息包括：各DFT方法与参考值的偏差、各DFT方法的绝对能量、可用于机器学习的输出文件。</p>
<p><strong>(3) 单一论文结论</strong>——Section 2.2 (p.4-5)明确指出60%以上的DFT方法仅在3项或更少研究中出现，单一来源结论可靠性存疑。</p>
<p><strong>对survey的建议：</strong>在"您信赖哪些证据来源"题目中设立三个层级：(a) 收录在通用数据库（GMTKN55/MGCDB84）中的公开基准数据；(b) 提供完整XYZ坐标和计算协议的独立研究（满足Section 3.6六项要求）；(c) 仅引用摘要性结论的单一论文。可追问受访者是否曾尝试复现已发表基准数据以及遇到的困难——这可能揭示数据可用性实践中的差距。</p>

<h2>阅读后回填</h2>
<ul>
<li><strong>对问卷题目或选项的影响：</strong>建议在模块B中增加"您通常如何选择信赖的DFT基准证据"的分层选择题，选项包括通用数据库（GMTKN55/MGCDB84）、提供完整数据的独立研究、仅有摘要结论的论文等。建议设立一个关于"数据可用性标准认知"的问题——询问受访者是否熟悉Section 3.6中列出的六个必要信息项。</li>
<li><strong>可转化为访谈追问的内容：</strong>追问受访者是否曾遇到无法复现已发表基准结果的情况；是否认为当前期刊对数据可用性的要求足够；标准化文件格式和命名约定在实践中是否可行。</li>
<li><strong>关键证据页码/图表：</strong>Figure 2 (p.4)——230种DFT方法分布（40%仅出现一次）；Figure 3 (p.6)——benchmark决策流程图；Section 2.2 (p.4-5)——方法覆盖度分析与Jacob's Ladder例外；Section 3.6 (p.10)——六个必要数据项；Equation 2-3 (p.7-8)——CCSD(T)组分分解与外推方案。</li>
<li><strong>仍未解决的问题：</strong>论文主要关注能量性质（原子化能、反应能、势垒高度），对三维电子密度、轨道、ESP等可视化场量的基准几乎没有涉及——这正是本项目可填补的空白。论文未讨论跨程序电子密度一致性验证，因此引擎无关工作流的密度可比性问题在现有基准文献中缺乏直接指导。</li>
</ul>

<h2>论文总结</h2>
<p><strong>论文核心发现：</strong>本文是一篇关于DFT基准数据库生成的综合性最佳实践综述，系统论述了参考值精度（Section 3.1）、数据集规模与多样性（Section 3.2）、参考几何（Section 3.3）、基组选择（Section 3.4）、统计分析（Section 3.5）和数据可用性（Section 3.6）六大关键维度，并提供了一个概念性决策流程图（Figure 3）。特别值得关注的发现：(1) 约60%的DFT方法在基准研究中出现不超过3次（Section 2.2）；(2) Jacob's Ladder并非可靠的性能预测工具——C40/C60富勒烯和PAH异构化能中存在反例；(3) MAD与RMSD必须同时报告——仅报告MAD可能高估方法性能；(4) 仅六个必要数据项（坐标、理论水平、电荷/多重度、PES点、参考值、参考值方法）即可确保基准数据的可复现性。</p>
<p><strong>与本项目的关联：</strong>论文关于标准化数据格式、提供完整元数据、跨平台可转移性的讨论直接支撑本项目"引擎无关工作流"的核心概念。"comparing apples with apples"原则（Section 3.1, p.5）对跨程序电子密度/轨道/ESP比较同样适用。对可视化而言，基准数据库通常仅报告能量——密度矩阵和轨道系数等关键可视化输入数据很少被纳入标准数据集，这为本项目的格式互操作需求提供了有力论据。论文强调的"data digital accessibility is essential"（Section 3.6, p.10）直接呼应本项目建立开放可复现可视化管线的目标。</p>
<p><strong>对问卷设计的具体建议：</strong>(1) 模块B应增加关于"基准证据可信度层级"的分层选择题；(2) 模块C可询问"您在可视化工作中是否遇到过不同程序预测的同种分子在三维场中存在显著差异的情况"；(3) 模块D/E可引入"您是否希望有一个标准化的格式来记录计算的完整元数据（几何、基组、网格、泛函）以便跨程序交换和验证"的概念测试题；(4) 建议增加一道关于"数据可用性六个必要项"认知度的题目，评估社区对标准化基准数据格式的熟悉程度。</p>
