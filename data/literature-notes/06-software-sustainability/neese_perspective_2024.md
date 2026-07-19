# neese_perspective_2024

BibTeX key：`neese_perspective_2024`

## 摘要

In this contribution, the challenges associated with the long-term development of general-purpose quantum chemical software packages are discussed and illustrated with the example of the ORCA package. , The field of computational chemistry has made an impressive impact on contemporary chemical research. In order to carry out computational studies on actual systems, sophisticated software is required in form of large-scale quantum chemical program packages. Given the enormous diversity and complexity of the methods that need to be implementation in such packages, it is evident that these software pieces are very large (millions of code lines) and extremely complex. Most of the packages in widespread use by the computational chemistry community have had a development history of decades. Given the rapid progress in the hardware and a lack of resources (time, workforce, money), it is not possible to keep redesigning these program packages from scratch in order to keep up with the ever more quickly shifting hardware landscape. In this perspective, some aspects of the multitude of challenges that the developer community faces are discussed. While the task at hand – to ensure that quantum chemical program packages can keep evolving and make best use of the available hardware – is daunting, there are also new evolving opportunities. The problems and potential cures are discussed with the example of the ORCA package that has been developed in our research group.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> ORCA 用户需求与未来软件形态</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>

<h2>阅读问题</h2>

<h3>问题 1</h3>
<p>ORCA 视角下未来量子化学软件最重要的用户需求和架构变化是什么？</p>
<p><strong>回答：</strong></p>
<p><strong>用户需求方面（共四大需求）：</strong></p>
<p><strong>1. 用户友好性</strong>（Section 1, p.295）：原文指出"experimental chemists to make use of quantum chemistry without having to be experts in the underlying theory or experts in the advanced use or programming of computers"——非专家用户应能进行常规计算，这是量子化学广泛采用的基础。</p>
<p><strong>2. 工作流自动化</strong>（Section 5.6, p.309-311）：Compound 脚本语言允许用户定义多步工作流（DFT优化→DLPNO-CCSD(T)单点能量→基组外推→溶剂修正），无需手动串联多个独立计算。论文提供了完整的伪代码示例（Schemes 2-3），展示如何用Compound脚本遍历多种近似电子结构方法。</p>
<p><strong>3. 长期可访问性</strong>（Section 4.2, p.300）：核心关切是"bring the established quantum chemical machinery onto the computers of the future"——确保现有软件能在未来硬件上继续运行。论文警告"legacy curse"：历史上CRAY/CYBER 205等硬件平台过时后，其上实现的程序随之消失。</p>
<p><strong>4. 互操作性</strong>（Section 4.4, p.301-302）："it would be highly desirable for programs to be more interoperable than they currently are"——但不能依赖临时性的程序特定接口（"custom-made interfaces are always vulnerable, short-lived and unreliable"）。</p>

<p><strong>架构变化方面（共五大变革）：</strong></p>
<p><strong>1. 模块化设计</strong>（Section 3, p.298）："Perhaps the most important requirement for a big program is modularity"——将复杂工作流拆解为可消化的独立模块，避免全局数据和交叉依赖。50,000行单一函数接受38个参数的做法是不可接受的。</p>
<p><strong>2. LKC概念</strong>（Section 5.2, p.304-305, Scheme 1）：Loop-Kernel-Consumer——全程序仅有单一积分生成点（IntegralLoop函数），通过虚函数IntegralConsumer::DigestIntegrals分发积分。这一设计使得对称性等功能的实现只需修改一处代码即可全局生效。</p>
<p><strong>3. Shell结构</strong>（Section 5.4, p.307-308, Figure 2）：外层（ORCA核心——用户交互与流程组织）、中层（DRIVERS——任务调度逻辑，负责区分COSX/RI-J/RIJCOSX等积分近似）、内层（SHARK——高性能数值计算核，独立于ORCA主体）。</p>
<p><strong>4. 自动代码生成</strong>（Section 5.5, p.308-309）："deep integration"概念——开发者仅需提交波函数Ansatz，自动翻译为方程、因子化并生成代码。代码生成链（equation generator→factorizer→code generator）本身是源代码仓库的一部分，确保代码的一致性和对新型硬件的快速适配。</p>
<p><strong>5. 算法进展远超硬件进展</strong>（Section 5.1, p.303-304, Figure 1）：比较ORCA 2005-2023版本在Vanc
omycin（176原子）上的性能，算法优化带来20x（def2-SVP）至200x（def2-TZVP）的加速，而纯硬件加速仅约7x。</p>

<h3>问题 2</h3>
<p>集成程序包用户对引擎无关工具最可能担心失去哪些程序特定控制？</p>
<p><strong>回答：</strong>基于论文对ORCA深度集成特性的描述，用户可能担心以下六个方面控制权的丧失：</p>
<p><strong>1. 深度集成的工作流语言</strong>（Section 5.6, p.309-311）：ORCA的Compound脚本语言允许用户在一个输入文件中定义多步计算协议、循环遍历分子或参数、读取和处理property文件中200+种属性。引擎无关工具无法复现这种"深植于计算内核"的脚本能力。</p>
<p><strong>2. 高度优化的积分引擎</strong>（Section 5.2, p.304-305）：SHARK基于矩阵乘法的积分算法利用高度优化的BLAS库，"every present and future computer will be delivered together with a highly optimized BLAS library"——用户可确定特定任务的性能特性。SHARK通过单一IntegralLoop函数消除了传统多重循环结构中的冗余。</p>
<p><strong>3. 统一的属性计算框架</strong>（Section 5.3, p.305-307）：ORCA的property容器+密度容器设计自动对所有电子结构方法（HF、MP2、CCSD(T)等）计算相同属性。orca_prop模块自动识别"eligible"密度并执行属性评估。引擎无关工具可能丢失这种"一次实现，到处可用"的一致性。</p>
<p><strong>4. 自动代码生成链</strong>（Section 5.5, p.308-309）：耦合簇等高精度方法的自动生成代码与手写代码性能相当，且代码生成链作为源代码仓库的一部分确保可维护性。FIC-MRCCSD等方法的实现在没有自动代码生成的情况下几乎不可能完成。</p>
<p><strong>5. 程序特定的优化路径</strong>（Section 4.4, p.301）：论文警告临时性接口"always vulnerable, short-lived and unreliable"，暗示引擎无关工具也可能面临同样的接口稳定性风险。</p>
<p><strong>6. 方法和功能的完整性</strong>（Section 4.4, p.301）："it is necessary and also desirable that there remains a multitude of software solutions"——用户对特定程序（如ORCA）的方法覆盖范围和实现质量建立了信任，引擎无关工具可能仅支持方法的子集。</p>
<p><strong>对问卷的启示：</strong>问卷应测量用户对以下权衡的态度：(a) 引擎无关的便利性 vs 程序特定优化的性能；(b) 通用格式的互操作性 vs 专有格式的完备功能；(c) 使用中间格式可能引入的额外故障点。特别需要询问用户是否曾因接口问题导致工作流失败的经历。</p>

<h3>问题 3</h3>
<p>论文是否提供 GUI、输入语言、HPC、方法覆盖或支持服务方面可转化为问卷选项的证据？</p>
<p><strong>回答：</strong></p>
<p><strong>1. GUI：</strong>论文未讨论图形用户界面——ORCA的设计哲学是输入文件驱动的，但Section 5.6的Compound脚本提供了一种"编程式GUI"替代方案。</p>
<p><strong>2. 输入语言</strong>（Section 5.6, p.309-311）：Compound脚本语言支持for/while/if/else/goto等编程结构，可直接嵌入ORCA输入文件或作为独立脚本调用。这是用户期望在计算包内部拥有工作流定义能力的关键证据。</p>
<p><strong>3. HPC：</strong>三个层次：(a) Section 4.2 (p.300)："legacy curse"——现有程序难以适应快速变化的硬件生态；(b) Section 5.1 (p.303-304, Figure 1)：算法优化（20-200x）远超硬件进展（7x），投资算法比追逐最新硬件更具回报；(c) Section 5.5 (p.308-309)：自动代码生成链的最后一步可针对新型硬件优化。</p>
<p><strong>4. 方法覆盖</strong>（Section 5.3, p.305-306）：综述了ORCA支持的HF、DFT、MP2、CCSD(T)、DLPNO-CC、CASSCF、MR-CI、NEVPT2等方法及NMR/EPR/极化率等属性计算——可转化为问卷的"方法覆盖面"维度。</p>
<p><strong>5. 支持服务：</strong>(a) Section 5 (p.303)：ORCA对学术研究者免费，"nearly 70,000 registered users world-wide"——用户规模是信任信号；(b) Section 4.5 (p.302)：商业公司FAccTs面向工业用户分发；(c) Section 4.1 (p.299-300)：学术开发环境中博士生和博士后是主要开发者，存在人员流动导致的维护风险；(d) Section 4.5 (p.302-303)：引用和认可机制不公正是开发者流失的重要原因。</p>
<p><strong>6. 互操作性接口</strong>（Section 5.7, p.311-312）：ORCA提供property文件（固定ASCII格式，200+属性）、JSON接口（支持MO系数、积分、振幅交换）和插件概念。JSON接口"不是因其简洁或美观，而是因其被年轻一代研究人员和计算机科学家的广泛接受"。</p>
<p><strong>问卷建议维度：</strong>用户对"算法持续优化"的重视程度、对"商业支持"的需求、对"方法覆盖范围"的期望、对"工作流脚本化"的偏好、对"接口稳定性"的容忍度。</p>

<h2>阅读后回填</h2>
<ul>
<li><strong>对问卷题目或选项的影响：</strong>ORCA的Compound脚本语言提供了"集成包内部工作流"的参照基准，问卷模块D（引擎无关工作流）应询问用户是否愿意为跨引擎互操作性放弃程序特定工作流语言。论文对interoperability的务实悲观态度（"all attempts in that direction have failed so far"，Section 4.4, p.301）为问卷模块D的"接口可靠性"问题提供了直接引用。ORCA的JSON接口（Section 5.7, p.312）可作为"理想中间格式需满足哪些技术要求"的参照。</li>
<li><strong>可转化为访谈追问的内容：</strong>(a) 用户对ORCA的Compound脚本是否有使用经验？是否愿意改用外部工作流工具？(b) 用户是否因担心失去程序特定优化而抵制引擎无关工具？(c) 用户如何评估程序特定接口与通用接口的可靠性差异？(d) 用户是否经历过因程序版本升级导致的自定义工作流失效？</li>
<li><strong>关键证据页码/图表：</strong>Figure 1算法vs硬件加速比（p.304）、Shell结构图（p.307, Figure 2）、LKC概念（p.304-305）、Compound脚本示例（p.310-311, Schemes 2-3）、JSON接口能力（p.311-312）、模块化要求（p.298）、遗产诅咒（p.300）、互操作失败历史（p.301-302）、自动代码生成链"deep integration"概念（p.308-309）。</li>
<li><strong>仍未解决的问题：</strong>论文讨论的程序特定性能优化（SHARK积分引擎）与通用中间格式之间的性能差距未被量化。论文未讨论3D可视化或体积数据工作流——引擎无关可视化工具在ORCA场景中属于"未探索领域"。JSON接口对大数据集（如双电子积分）的适用性明确受限（"the size of the files that holds these integrals quickly becomes too large"，p.312）。</li>
</ul>

<h2>总结</h2>
<p>Neese（2024）以ORCA为例深入讨论了大型量子化学程序包的可持续发展挑战。核心发现：(1) 模块化、文档完整性和代码完整性是大型程序长期可维护的三大支柱；(2) 算法进展远超硬件进展（20-200x vs 7x），论证了持续算法投资的价值；(3) 互操作性是"高度期望但长期未能实现"的目标——社区未能就数据格式达成共识，所有临时性接口都"脆弱、短命、不可靠"；(4) ORCA通过Shell结构（Figure 2）、LKC概念（Scheme 1）、自动代码生成（Section 5.5）和Compound脚本（Section 5.6）实现了深度架构优化；(5) ORCA 5之后的完全重写（"complete rewrite in the years following the release of ORCA 5"，p.303）是由于安全职位和慷慨基础资助的奢侈——"is therefore not a generalizable strategy"。</p>
<p><strong>对本项目的关联：</strong>论文对互操作性的务实悲观态度提醒引擎无关工作流项目需要认真对待接口稳定性挑战；ORCA选择的JSON接口和property文件设计为通用中间格式提供了参考。论文未讨论3D可视化，使本项目在ORCA场景中属于"未探索领域"——既是风险也是机遇。</p>
<p><strong>对问卷的贡献：</strong>提供了"集成包用户视角"的负面证据（担忧失去程序特定控制），可作为模块D问题设计的平衡视角。论文中about 70,000注册用户（p.303）说明ORCA是第二大最常用的量子化学程序，是重要的调查对象群体。</p>
