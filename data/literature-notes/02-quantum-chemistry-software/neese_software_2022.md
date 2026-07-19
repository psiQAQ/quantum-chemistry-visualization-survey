# neese_software_2022

BibTeX key：`neese_software_2022`

## 摘要

Abstract Version 5.0 of the ORCA quantum chemistry program suite was released in July 2021. ORCA 5.0 represents a major improvement over all previous versions of ORCA and features (1) highly improved performance, (2) increased numerical robustness, (3) a host of new functionality, and (4) greatly improved user friendliness. The article describes the most salient features of the program. This article is categorized under: Electronic Structure Theory {\textgreater} Ab Initio Electronic Structure Methods Data Science {\textgreater} Computer Algorithms and Programming Software {\textgreater} Quantum Chemistry

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> ORCA 5 能力与采用</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>

<h2>阅读问题</h2>

<h3>问题 1</h3>
<p>ORCA 5 的哪些能力最影响用户选择主程序，哪些会产生特殊可视化输出？</p>
<p><strong>回答：</strong></p>
<p><strong>影响用户选择主程序的能力（五大核心卖点）：</strong></p>
<p><strong>1. SHARK全新积分引擎</strong>（Section 2, p.2-3）：HF/DFT加速10-30%，解析Hessian、TD-DFT、NMR/EPR性质加速5-10倍。论文提供了大量基准数据：Table 1 (p.7)显示Vancomycin/PBE在ORCA 5中Fock矩阵构建时间从1,797s降至79-89s（def2-SVP）；Table 2 (p.8)显示Penicillin/B3LYP解析Hessian从17,925s降至1,008s（def2-TZVP）；Table 4-6 (p.8-9)显示极化率、NMR、EPR的类似加速。SHARK支持segmented、一般收缩和部分一般收缩基集三类，对ANO基组加速尤为显著。</p>
<p><strong>2. 无与伦比的方法覆盖面</strong>（Section 4-8, p.3-5）：通过LibXC 5.1.0支持588种泛函（Section 4, p.3）；DLPNO-CC系列近线性标度（Section 7, p.4）；CASPT2-K（无经验参数、无入侵态）、NEVPT2（Section 8, p.4-5）；ICE选态CI（Section 8, p.5, 最多约80个活性轨道）；FIC-MRCCSD（自动代码生成）。</p>
<p><strong>3. 原生多尺度能力</strong>（Section 10, p.5）：QM/MM（自动边界处理、电荷移位补偿、CHARMM/AMBER/OPENFF力场）、ONIOM（最多三层、QM/QM2/MM组合）、Crystal-QMMM（从.cif/.xyz文件直接生成输入）。</p>
<p><strong>4. 高度稳健的SCF和优化</strong>（Section 5, p.3）：TRAH二阶收敛器几乎保证SCF收敛，自动检测难收敛案例；NEB过渡态搜索（仅需反应物和产物结构）。</p>
<p><strong>5. 用户友好生态</strong>（Section 1, p.1; Section 13, p.6）：>40,000学术用户、>1300页手册、活跃论坛、Compound脚本语言。ORCA 5后代码重写删除了"hundreds of thousands of lines of legacy code"（Section 2, p.3）。</p>
<p><strong>产生特殊可视化需求的能力和输出：</strong></p>
<p><strong>1. Compound脚本+Property文件</strong>（Section 13, p.6）：直接访问250+种ASCII属性——能量、梯度、频率、激发态信息等均可直接读取用于可视化后处理。GitHub上已有Compound Scripts仓库（github.com/ORCAQuantumChemistry/CompoundScripts）供社区交换协议。</p>
<p><strong>2. DLPNO局域轨道信息</strong>（Section 7, p.4）：DLPNO-CCSD(T)的PNO轨道、HF-LD方法（Section 7, p.5, 参考文献[84]）提供色散相互作用的精确可视化分析——DNA片段中色散相互作用等大分子拓扑可视化有特殊需求。</p>
<p><strong>3. 多参考方法输出</strong>（Section 8, p.4-5）：AILFT（ab initio ligand field theory）扩展至双壳层处理，orca_lft程序独立计算过渡金属配合物X射线吸收/光学/磁谱——这些谱图模拟结果需要特殊的可视化呈现。</p>
<p><strong>4. EPR/NMR性质可视化</strong>（Section 12, p.6）：NMR化学位移（Table 5, p.9：Amylose 2的555个自旋-自旋耦合张量计算从92,280s→14,057s）、EPR g-张量（Table 6, p.9：Chlorophyll A radical从17,636s→7,944s）。自旋密度、化学位移等量有天然可视化需求。</p>
<p><strong>5. 网格自适应性</strong>（Section 3, p.2-3）：DefGrid1-3系列具有"self-adapting"特性——自动识别陡峭函数、弥散函数和核心极化函数并调整积分精度。这种自适应网格对可视化场精度的影响需要特别关注。</p>

<h3>问题 2</h3>
<p>论文体现了怎样的输入、并行、方法覆盖和用户支持模式？</p>
<p><strong>回答：</strong></p>
<p><strong>输入模式：</strong>简单的文本输入文件（Section 1, p.1）。Compound脚本语言（Section 13, p.6）提供变量、if/for/while/goto/系统调用和内联格式化打印，可定义标准化协议。Compound脚本中可使用"with"语句覆盖变量（"with" statement, p.6）。提供Scheme 2-3的完整伪代码示例。Compound Job Library托管于GitHub支持社区交换。</p>
<p><strong>并行模式：</strong>共享内存消除内存瓶颈（Section 2, p.2）、OpenMPI 4.1（参考文献[28]）、OpenBLAS。16核并行效率14-15x、32核约25x（Section 14, p.7, Table 1附注）。代码几乎完全转换为64位整数（Section 2, p.3）。</p>
<p><strong>方法覆盖：</strong>半经验HF、DFT（588种泛函via LibXC 5.1.0）、DLPNO-CCSD(T)及F12、CASPT2-K（无参数/无入侵态）、NEVPT2、ICE选态CI（约80活性轨道）、FIC-MRCCSD、TD-DFT（含自旋翻转/非绝热耦合/锥形交叉优化）、NEB过渡态、Born-Oppenheimer分子动力学（Section 11, p.5-6，含meta-dynamics和umbrella sampling）、EPR/NMR性质（Section 12, p.6）。</p>
<p><strong>用户支持模式：</strong>>40,000注册学术用户（Section 1, p.1）、forum.kofo.mpg.de活跃论坛、>1300页PDF手册（"extensively documented"）、FAccTs教程站点（orcasoftware.de/tutorials_orca）、ORCA Input Library、Compound Script GitHub仓库。商业分发通过FAccTs（faccts.de）对工业用户，学术使用免费。Section 1 (p.1)引用的引用统计表明ORCA"presently the second most used quantum chemistry program world-wide with a steep upward slope"。</p>

<h3>问题 3</h3>
<p>ORCA 输出接入引擎无关工具时，哪些原生文件或程序特定选项不可丢失？</p>
<p><strong>回答：</strong></p>
<p><strong>必须保留的原生文件（共5类）：</strong></p>
<ul>
<li><strong>.gbw（二进制波函数文件）</strong>——包含轨道系数、占据数等完整波函数信息。Section 2提到SHARK使用"ORCA infrastructure"并生成各种中间文件。虽未在本文细述，但.gbw是所有后处理的根本输入。</li>
<li><strong>.prop（属性文件，ASCII）</strong>——Compound脚本直接访问250+种属性（Section 13, p.6）。"fixed format human as well as machine readable file"——这是ORCA设计为长期稳定接口的输出文件。</li>
<li><strong>.hess</strong>（Hessian矩阵，Table 2, p.8）、<strong>.engrad</strong>（能量+梯度）、<strong>.xyz</strong>（几何）——标准结构/能量/频率输出。</li>
<li><strong>.cis</strong>（激发态信息文件）——含CIS/TD-DFT激发矢量，Section 6 (p.3-4)提及TD-DFT功能。</li>
<li><strong>Cube文件</strong>——轨道/密度三维网格（论文未直接讨论，是ORCA标准输出选项）。</li>
</ul>
<p><strong>不可丢失的程序特定选项（共5项）：</strong></p>
<ul>
<li><strong>RIJCOSX近似</strong>（Section 2, p.2-3; Tables 1-2附注）——ORCA 5默认为hybrid DFT启用，数值精度与全解析"almost indistinguishable"（p.7）。论文指出ORCA 4.2.1中不使用RIJCOSX时ORCA 5仍更快约2倍（p.7）。</li>
<li><strong>DefGrid1-3数值积分网格</strong>（Section 3, p.2-3）——机器学习技术重新设计（参考文献[29]），"self-adapting"行为识别陡峭/弥散/核心极化函数。典型网格不完备性误差在相对能量中仅0.01(0.05) kcal/mol。DefGrid2为默认。</li>
<li><strong>TRAH收敛器</strong>（Section 5, p.3）——trust-radius augmented Hessian二阶方法，几乎保证SCF收敛。"automatically detect slowly- or nonconvergent cases"（p.3）。</li>
<li><strong>DLPNO截断阈值</strong>（Section 7, p.4）——TightPNO/NormalPNO/LoosePNO控制局域相关方法精度-成本权衡。论文提及"proximity check that ensures domain completeness"（p.4）和"extrapolation protocol to the complete PNO limit"（参考文献[106]）。</li>
<li><strong>Compound脚本中的"with"语句</strong>（Section 13, p.6）——允许在输入文件中覆盖变量。这是ORCA工作流灵活性的关键语法特征。</li>
</ul>
<p><strong>额外注意：</strong>ORCA 5之后进行了"complete rewrite"（Neese, 2024, p.303），意味着输入语法和输出格式可能在未来版本中发生变化。引擎无关工具需要关注这种不稳定性。</p>

<h2>阅读后回填</h2>
<ul>
<li><strong>对问卷题目或选项的影响：</strong>(a) Q16（格式覆盖面）：确认ORCA的.gbw、.prop、.hess、.cube为需列出的关键格式；(b) Q17（首个输入适配器）：ORCA的Compound脚本+属性文件提供了"内嵌后处理逻辑"的独特模式，与PySCF的纯Python后处理形成对比；(c) B模块（可信度）：>40,000用户、活跃论坛以及FAccTs商业支持提供了"社区信任"证据。</li>
<li><strong>可转化为访谈追问的内容：</strong>(a) ORCA用户是否实际使用Compound脚本做后处理？还是更倾向Python脚本？(b) 用户是否知道.gbw文件可以直接输出Cube文件用于可视化？使用频率如何？(c) RIJCOSX作为默认带来的性能-精度权衡是否为用户所感知？(d) 用户是否关注ORCA 5重写后输入格式的变化？</li>
<li><strong>关键证据页码/图表：</strong>Table 1 (p.7)：Vancomycin/PBE Fock矩阵构建计时（1,797s→79s, def2-SVP）；Table 2 (p.8)：解析Hessian计时——B3LYP/Def2-QZVP从326,856s→7,410s（44x加速）；Table 3 (p.8)：TD-DFT 25 roots计时——Pheophytin A/B3LYP/cc-pVTZ从4,981s→1,699s；Table 5-6 (p.9)：NMR自旋-自旋耦合从92,280s→14,057s；Section 13 (p.6)：Compound脚本语言——直接与property file交互访问250+属性；Section 3 (p.2-3)：DefGrid自适应性；Figure 1 (p.7)：基准分子结构。</li>
<li><strong>仍未解决的问题：</strong>(a) ORCA 5的cube文件输出是否支持所有方法（尤其是DLPNO和多参考方法）？(b) 二进制.gbw文件的格式是否公开/可解析？是否有开源解析器？(c) Compound脚本是否能触发cube文件生成？(d) 论文未讨论.cube以外的可视化输出格式。</li>
</ul>

<h2>总结</h2>
<p>ORCA 5论文（Neese, 2022）是一篇软件更新综述，重点描述了SHARK全新积分引擎（Section 2）带来的5-44x性能提升（Tables 1-6）、588种泛函覆盖（via LibXC 5.1.0, Section 4）、DLPNO系列近线性标度相关方法（Section 7）以及原生多尺度能力（QM/MM、ONIOM、Crystal-QMMM，Section 10）。另外值得关注的是：(1) DefGrid1-3网格使用机器学习技术重新设计，具有自适应性（Section 3）；(2) TRAH二阶收敛器几乎保证SCF收敛（Section 5）；(3) Compound脚本语言可访问250+种属性（Section 13）——与Neese 2024展望论文中描述的JSON接口和插件概念形成技术演进脉络。</p>
<p><strong>对本项目的关键启示：</strong>(1) ORCA的Compound脚本+属性文件模式提供了"自包含后处理"范式，与引擎无关工具的"外部后处理"思路形成互补；(2) ORCA用户规模（>40,000）和商业分发（FAccTs）证明了独立于PySCF的广泛用户群——问卷必须覆盖ORCA用户的格式互操作需求；(3) 论文未讨论.cube以外的可视化输出格式，但DLPNO局域轨道（Section 7）、CASSCF活性空间轨道（Section 8）、自旋密度（Section 12）等科学语义信息是非常值得保留的可视化内容；(4) Tables 1-6的大量基准计时数据对问卷B模块（信任度与失败风险）有支撑价值——用户选择程序时性能数据是关键信任依据；(5) ORCA 5之后的"complete rewrite"（Neese 2024, p.303）意味着接口稳定性是用户关注的核心问题。</p>
