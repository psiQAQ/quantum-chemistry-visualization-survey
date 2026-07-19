# morgante_devil_2020

BibTeX key：`morgante_devil_2020`

## 摘要

Abstract Density functional theory (DFT) has become ubiquitous for chemical applications in research and in education. The exact functional at the foundation of DFT is unfortunately unknown, and issues arise when choosing an approximation for a specific application. With this tutorial review, we tackle the selection problem and many related ones, such as the choices of a basis set and of an integration grid, that are often overlooked by occasional practitioners and by more experienced users as well. We offer a practical approach in the form of a commented notebook containing 12 experiences that can be run on a simple computer in just a few hours. We propose this review as a primary source for those who are willing to include DFT in their everyday research or teaching activities in a way that reflects the research advances of the field in the last couple of decades.

## Zotero 阅读笔记

<div data-schema-version="9"><h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 默认设置、可靠性与可复现</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>哪些被低估的 DFT 设置最常导致结果不可复现，用户是否通常知道自己使用的实际值？</p>
<p><strong>回答：</strong>论文明确识别了三个被低估的技术问题：</p>
<p><strong>(1) 积分网格</strong>（Section 3.6, p.6-7, Figure 3）："The choice of the integration grid can have a significant impact on the quality of electronic energies"。网格不当会导致非物理解释振荡——Figure 3展示了Ar2解离曲线在SG-1和75,302网格上出现的"unphysical artifacts"，而在99,590 (UltraFine)和175,974网格上才趋于平滑。论文明确指出网格不仅影响能量还影响"thermodynamic quantities such as entropies, Gibbs' free energies, and vibrational frequencies"（Section 3.6, p.7）。</p>
<p><strong>(2) 稳定性分析</strong>（Section 3.7, p.7）：SCF可能收敛到激发态或鞍点，特别是对低能隙体系和过渡金属。"some of the most popular quantum chemistry software packages do not use the stability analysis algorithm as a default procedure, while a surprisingly large minority of software even lacks such an algorithm altogether"。</p>
<p><strong>(3) 实验值比较</strong>（Section 3.8, p.7）：计算的总原子化能不能直接与实验生成焓比较。"several corrections that account for such differences must be applied to the calculated data for a meaningful comparison"——ZPVE、热力学校正、相对论校正等。</p>
<p><strong>用户通常不知道实际值：</strong>论文提供了多层面证据：(a) "untrained users often do not change the default setting"（p.6）；(b) "an untrained user is much more blind to this issue than to the more common methodological ones"（p.6）；(c) Section 3.3 (p.5)指出B3LYP/6-31G*已被大量非专业用户当作"DFT的同义词"盲目使用，即使在已知有问题的反应类型中（"documented failures in specialized literature since the mid-2000s"）。论文引用文献[83, 85, 131-137]记录了这些已知失败。</p>
<h3>问题 2</h3>
<p>基组、积分网格、稳定性分析和实验比较中哪些必须进入最低元数据？</p>
<p><strong>回答：</strong></p>
<p><strong>(1) 基组</strong>（Section 3.2, p.4-5）：必须记录基组系列和具体名称。论文详细讨论了BSIE（基组不完备性误差）和BSSE（基组重叠误差），指出Ahlrichs的def2系列和Jensen的pc-n系列是针对DFT优化的首选。基组选择对DFT计算有特殊影响——"basis sets specifically optimized for DFT calculations are in general superior to those optimized for wave function theory ones"。</p>
<p><strong>(2) 积分网格</strong>（Section 3.6, p.7）：论文明确要求："When reporting computed results, it is important to indicate the integration grid used to obtain structures and energy values to make them reproducible"。网格规格必须包含径向点数与角度点数的完整描述（例如99,590网格）。论文指出99,590网格仅是"minimum requirement"。</p>
<p><strong>(3) 稳定性分析</strong>（Section 3.7, p.7）：对含过渡金属和低能隙体系必须检查，多数软件默认不做。建议在报告中注明是否执行了稳定性检查及其结果。</p>
<p><strong>(4) 实验比较</strong>（Section 3.8, p.7）：必须列出所使用的所有校正项（ZPE、热力学校正、相对论校正、DBOC等），参考Karton[163]和Truhlar[164,165]的详细方案。</p>
<p><strong>结论：</strong>四者均必须进入最低元数据，尤其是基组和积分网格——缺少它们会使结果在物理上不可复现。Table 1 (p.3)提供了notebook的完整提纲。</p>
<h3>问题 3</h3>
<p>如何把"依赖默认值"的风险写成行为问题，而不是考察受访者理论知识？</p>
<p><strong>回答：</strong>论文提供了丰富的真实行为场景素材（Section 3.6-3.7, p.6-7; Section 3.3, p.5），可转化为以下行为问题类型：</p>
<p><strong>类型1：网格配置行为</strong>——"您是否曾经更改过您主要使用的量子化学程序中的默认积分网格设置？您知道该程序的默认网格规格是多少吗？"论文指出"an untrained user is much more blind to this issue"（p.6），显示多数用户对网格设置毫无意识。</p>
<p><strong>类型2：稳定性检查行为</strong>——"在什么情况下您会检查SCF稳定性？您的软件是否自动执行此检查？"论文指出许多流行程序默认不做，甚至缺少此功能。</p>
<p><strong>类型3：方法选择行为</strong>——"您是否曾经仅因某个方法/基组流行就使用它，而没有查阅基准文献确认其适用性？"Section 3.3 (p.5)指出B3LYP/6-31G*已成为"DFT计算本身的同义词"，即使在教育文献中也"applied without any formal justification or critical evaluation"。</p>
<p><strong>类型4：网格收敛性检查行为</strong>——基于Figure 3的Ar2解离曲线振荡现象，可设计场景题："如果您使用默认网格计算某个弱相互作用体系的解离曲线，发现能量曲线出现振荡，您首先会怎么做？"</p>
<p><strong>类型5：实验比较行为</strong>——"您是否曾在比较计算值与实验值时，忽略了ZPVE或热力学校正？"Section 3.8 (p.7)指出这种直接比较是常见错误。</p>
<p><strong>核心设计原则：</strong>问"你是否打开过/修改过/检查过X"（行为），而不是"你认为X重要吗"（态度）。论文提供的12个notebook实验（Table 1, p.3）本身就是行为导向的设计模板。</p>
<h2>阅读后回填</h2>
<ul>
<li>
<strong>对问卷题目或选项的影响：</strong>建议在模块B/C中加入关于"默认网格名称和点数"的具体选择题（如：请选择您最常使用的积分网格等级/点数），以及在模块B中加入"是否曾因默认设置发现问题"的行为选择题。Figure 3的Ar2解离曲线可作为认知测试题的视觉材料。
</li>
<li>
<strong>可转化为访谈追问的内容：</strong>追问用户如果现在知道99,590只是最低要求，是否愿意检查已有计算结果的网格收敛性；是否曾因网格问题放弃或重复过计算。
</li>
<li>
<strong>关键证据页码/图表：</strong>Figure 3 (p.7)——Ar2解离曲线在不同网格上的振荡；Table 1 (p.3)——12个notebook实验提纲；Section 3.6 (p.6-7)——网格详细讨论；Section 3.7 (p.7)——稳定性分析；Section 3.2 (p.4-5)——基组BSIE/BSSE；Section 3.3 (p.5)——B3LYP/6-31G*盲目使用问题。
</li>
<li>
<strong>仍未解决的问题：</strong>论文推荐了网格但未讨论网格缺陷对可视化场（电子密度、轨道、ESP）的具体影响——这是本项目可以补充的空白。论文仅涉及能量和频率，不讨论可视化相关的数值问题。
</li>
</ul>
<h2>论文总结</h2>
<p><strong>论文核心发现：</strong>本文是一篇tutorial review，系统指出DFT计算中三个被低估的技术陷阱——积分网格精度、SCF稳定性分析和实验值比较校正，并通过12个notebook实验（Table 1, p.3）提供实操指南。核心结论：(1) 99,590 (UltraFine)网格仅为最低要求，必须记录在报告中；(2) B3LYP/6-31G*不能盲信——论文引用大量文献记录了其失败案例；(3) 稳定性分析对过渡金属和低能隙体系至关重要，但多数软件默认不做；(4) 实验比较需要多项校正，直接比较是错误的；(5) 300+种泛函中仅约30种真正广泛使用（Figure 1, p.2），用户需借助大型基准数据库选择。</p>
<p><strong>与本项目的关联：</strong>论文强调的"用户通常不知道自己使用的默认网格""不同程序的默认网格不一致"等发现，直接支撑本项目关于跨引擎工作流中元数据标准化（记录网格名称、径向/角向点数）的需求论证。网格缺陷影响能量和梯度，必然也影响三维场的数值精度——可视化不可靠的重要来源。不同程序默认网格不一致意味着跨引擎可视化的场数据可能具有系统偏差。</p>
<p><strong>对问卷设计的具体建议：</strong>(1) 模块B应增加"您是否曾经检查或更改过默认积分网格？"的行为问题；(2) 模块C应包含"您的可视化场计算使用了什么网格等级？"的选项；(3) 模块E可加入网格收敛性检查相关的PySCF次级变量问题；(4) 建议在问卷中嵌入一个简单的场景判断题，让受访者判断哪种设置组合可能导致不可靠的可视化结果；(5) 建议调查用户对B3LYP/6-31G*的依赖程度，作为"习惯驱动型"用户分类的依据。</p>
</div>
