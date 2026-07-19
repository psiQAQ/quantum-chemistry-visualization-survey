# karton_good_2025

BibTeX key：`karton_good_2025`

## 摘要

ABSTRACT The hundreds of density functional theory (DFT) methods developed over the past three decades are often referred to as the “zoo” of DFT approximations. In line with this terminology, the numerous DFT benchmark studies might be considered the “safari” of DFT evaluation efforts, reflecting their abundance, diversity, and wide range of application and methodological aspects. These benchmarks have played a critical role in establishing DFT as the dominant approach in quantum chemical applications and remain essential for selecting an appropriate DFT method for specific chemical properties (e.g., reaction energy, barrier height, or noncovalent interaction energy) and systems (e.g., organic, inorganic, or organometallic). DFT benchmark studies are a vital tool for both DFT users in method selection and DFT developers in method design and parameterization. This review provides best‐practice guidance on key methodological aspects of DFT benchmarking, such as the quality of benchmark reference values, dataset size, reference geometries, basis sets, statistical analysis, and electronic availability of the benchmark data. Additionally, we present a flowchart to assist users in systematically choosing these methodological aspects, thereby enhancing the reliability and reproducibility of DFT benchmarking studies.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 基准设计与信任证据</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>高质量 DFT 基准数据库需要怎样的体系多样性、参考值、几何、基组和统计指标？</p>
<p><strong>回答：</strong>论文从五个关键维度给出了详细指南：<strong>(1) 参考值精度</strong>（Section 3.1, p.5-7）：CCSD(T) 是评估 rungs 1-4 泛函的最低参考标准（"the gold standard of quantum chemistry"），对困难体系需 CCSDT(Q) 或 CCSDTQ5；使用复合方法（如 G4(MP2)、W1-F12）时需注意校正项的反向扣除（"comparing apples with apples"），如 ZPVE 校正、相对论校正等。<strong>(2) 数据集大小与多样性</strong>（Section 3.2, p.7-8）：需平衡体系规模与数据点数量，小型聚焦数据集（如 AE6/BH6）和大型通用数据库（如 GMTKN55 含 500-5000 数据点、GDB9 含 122k 原子化能）各有优劣；必须确保各反应子集在代表性上的平衡。<strong>(3) 参考几何</strong>（Section 3.3, p.8-9）：需确认是局域极小值或一阶鞍点（通过频率分析），推荐 B3LYP/def2-TZVPP 用于获取参考几何，其对 W1-F12 原子化能的影响仅约 1 kJ/mol。<strong>(4) 基组</strong>（Section 3.4, p.9）：纯 DFT 方法建议四重 zeta 以逼近 CBS 极限，DHDFT 需要更高角动量基组；推荐 Jensen 的 pc-n 和 Ahlrichs 的 def2 系列。<strong>(5) 统计指标</strong>（Section 3.5, p.9-10）：建议同时报告 MAD、RMSD、MSD、MAD/RMSD 比值（正常分布约 0.8）、最大正/负偏差；MAD 与 RMSD 联合使用可避免单一指标带来的偏差。Figure 3 (p.6) 提供了一个完整的决策流程图。</p>
<h3>问题 2</h3>
<p>哪些基准证据足以支持跨程序比较，哪些只能说明特定方法在特定数据集上的表现？</p>
<p><strong>回答：</strong>论文指出：<strong>跨程序比较需要</strong>的数据条件包括（Section 3.6, p.10）：(1) 所有物种的笛卡尔坐标（XYZ 格式）；(2) 几何优化所用理论水平；(3) 电荷和多重度；(4) PES 上的具体点（平衡态或过渡态）；(5) 基准参考值及获取方法；(6) 使用标准化的文件格式和命名约定（"standardized file formats and naming conventions to enhance the transferability of the data across different platforms and software packages"）。<strong>单数据集结论</strong>方面，Section 2.2 (p.4-5) 分析了 30 项基准研究中的 230 种 DFT 方法，发现 40%（89 种）仅出现在一项研究中，60% 以上仅出现在三项或更少研究中。论文强调 Jacob's Ladder 并不能确保跨 rung 的系统性改进（如 C40/C60 富勒烯异构化能中 rung 3 优于 rungs 3.5 和 4，SVWN5 优于某些 rungs 2-4），因此单一基准论文的结论不能泛化。论文推荐引用 GMTKN55、MGCDB84 等大型通用数据库的结论作为更可靠的跨程序比较依据。</p>
<h3>问题 3</h3>
<p>survey 的"独立基准"选项是否需要区分公开数据、可复现协议与单一论文结论？</p>
<p><strong>回答：</strong>是的，非常需要。论文 Section 3.6 (p.10) 明确提出了数据可用性的分层标准：(1) <strong>公开数据</strong>——"the digital accessibility of benchmark data is essential for updating and conducting larger-scale benchmarking studies"，必须包含 XYZ 坐标、理论水平、电荷/多重度等六个必要信息；(2) <strong>可复现协议</strong>——论文提供了 Figure 3 的决策流程图，并推荐使用标准化文件格式和命名约定来增强跨平台可转移性；(3) <strong>单一论文结论</strong>——如 Section 2.2 所述，60% 以上的 DFT 方法仅在 3 项或更少研究中出现，单一来源的结论可靠性存疑。建议 survey 在"您信赖哪些证据来源"题目中设立三个层级：(a) 收录在通用数据库（如 GMTKN55）中的公开基准数据；(b) 提供完整 XYZ 坐标和计算协议的独立研究；(c) 仅引用摘要性结论的单一论文。同时可询问受访者是否曾尝试复现已发表基准数据以及遇到的困难。</p>
<h2>阅读后回填</h2>
<ul><li>对问卷题目或选项的影响：建议在模块 B 中增加"您通常如何选择信赖的 DFT 基准证据"的分层选择题，选项包括通用数据库（GMTKN55/MGCDB84）、提供完整数据的独立研究、仅有摘要结论的论文等。</li><li>可转化为访谈追问的内容：追问受访者是否曾遇到过无法复现已发表基准结果的情况，以及他们认为应该建立什么样的数据共享标准。</li><li>关键证据页码/图表：Figure 2 (p.4) —— 230 种 DFT 方法分布；Figure 3 (p.6) —— benchmark 决策流程图；Section 2.2 (p.4-5) —— 方法覆盖度分析；Section 3.6 (p.10) —— 数据可用性要求。</li><li>仍未解决的问题：论文主要关注能量性质（原子化能、反应能、势垒高度），对于三维电子密度、轨道、ESP 等可视化场量的基准几乎没有涉及，这正是本项目可填补的空白。</li></ul>
<h2>论文总结</h2>
<p><strong>论文核心发现：</strong>本文是一篇关于 DFT 基准数据库生成的综合性最佳实践指南，系统论述了参考值精度、数据集规模与多样性、参考几何、基组选择、统计分析和数据可用性六大关键维度，并提供了一个概念性的决策流程图（Figure 3）。值得特别关注的是：论文发现 60% 以上的 DFT 方法在基准研究中出现不超过 3 次，许多方法的性能信息严重不足；Jacob's Ladder 并非可靠的性能预测工具，一些低 rung 方法在特定体系上可能超越高 rung 方法。</p>
<p><strong>与本项目的关联：</strong>论文关于标准化数据格式、提供完整元数据、跨平台可转移性的讨论直接支持本项目"引擎无关工作流"的核心概念。其强调的"comparison apples with apples"原则对跨程序电子密度/轨道/ESP 比较同样适用。对可视化而言，基准数据库通常仅报告能量，而密度矩阵和轨道系数等关键可视化输入数据很少被纳入标准基准数据集中——这为本项目的格式互操作需求提供了有力论据。</p>
<p><strong>对问卷设计的具体建议：</strong>(1) 模块 B 应增加关于"基准证据可信度层级"的分层选择题；(2) 模块 C 可询问"您在可视化工作中是否遇到过不同程序预测的同种分子在三维场中存在显著差异的情况"；(3) 模块 D/E 可引入"您是否希望有一个标准化的格式来记录计算的完整元数据（几何、基组、网格、泛函）以便跨程序交换和验证"的概念测试题。</p>
