# werner_molpro_2020

BibTeX key：`werner_molpro_2020`

## 摘要

Molpro is a general purpose quantum chemistry software package with a long development history. It was originally focused on accurate wavefunction calculations for small molecules but now has many additional distinctive capabilities that include, inter alia, local correlation approximations combined with explicit correlation, highly efficient implementations of single-reference correlation methods, robust and efficient multireference methods for large molecules, projection embedding, and anharmonic vibrational spectra. In addition to conventional input-file specification of calculations, Molpro calculations can now be specified and analyzed via a new graphical user interface and through a Python framework.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> Molpro、高精度与多参考工作流</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>Molpro 的优势任务中，哪些输出最需要与其他程序交叉核验或可视化？</p>
<p><strong>回答：</strong></p>
<p>Molpro 的核心优势包括高度精确的波函数方法——CCSD(T)-F12、MRCI-F12、CASPT2-F12（Sec. II），以及局域关联近似 PNO-LCCSD(T)-F12（Sec. II.A, Table V）。这些方法输出的激发能（EOM-CCSD）、振子强度（Sec. II.D）、跃迁密度矩阵（Sec. II.D）、自旋-轨道耦合（Sec. II.H）、SAPT 能量分解（Sec. IV.C）及其非简谐振动谱（Sec. VI）都是需要跨程序验证的关键量。特别是（1）大体系 PNO 局域相关方法的能量和梯度，常需与 Canonical 计算对照验证 Local 近似的误差（Table V 中对比了 PNO-CASPT2 与 Canonical CASPT2 的 ΔE）；(2) 多参考势能面（IC-MRCI, Sec. II.F）的激发态能量和锥形交叉结构；(3) 含频响应性质（Sec. II.C）在界面可视化中需要与实验谱图交叉核验。</p>
<h3>问题 2</h3>
<p>高阶相关与多参考计算的轨道/态数据在格式和元数据上有哪些特殊要求？</p>
<p><strong>回答：</strong></p>
<p>Molpro 采用多种格式管理高阶数据：(1) FCIdump 格式（Sec. IX.A）以分子轨道基下的 Hamiltonian 矩阵元表示，用于连接 MRCC、NECI 等外部程序，支持 model Hamiltonians 和 frozen virtual 近似；(2) molpro-output XML schema（Sec. VIII.D）结构化输出能量、几何、振动频率、基组和轨道信息，支持 pysjef Python 模块进行后处理；(3) CASSCF（Sec. II.F）轨道可继续用于单态或多态 PNO-CASPT2 计算；(4) 激发态密度矩阵和跃迁密度矩阵（Sec. II.D）用于导出偶极矩、振子强度等一阶性质；(5) 多参考计算中需要明确活性空间定义、态平均权重、自旋多重度等元数据；(6) 对 PNO 方法，额外需要 PNO 占据数阈值、局域化方案等参数描述。Molpro 本身不含 Molden/cube 等通用可视化格式的标准导出，但 XML 输出一定程度上弥补了这一缺口。</p>
<h3>问题 3</h3>
<p>商业许可、输入语言和集群运行是否构成采用独立可视化工具的障碍？</p>
<p><strong>回答：</strong></p>
<p>Molpro 是商业软件（许可证不在本文公开范围内），其输入语言为 ASCII 命令式（Sec. VIII.A），支持 xyz 和 z-matrix 格式的几何输入，对标准计算而言较为简洁（Table VII 给出了 DFT 和 CC 的典型输入）。集群运行方面：(1) gmolpro GUI（Sec. VIII.C）仅支持 Linux/macOS，无原生 Windows 版本，限制了部分用户的可视化环境选择；(2) PQSMol 界面支持远程作业提交（Sec. VIII.C），但交互式可视化依赖 PQSMol viewer；(3) SJEF（Simple Job Execution Framework, Sec. VIII.D）提供了 C++/C/Python 绑定及命令行工具，使外部工具可通过 pysjef 接口读取 Molpro 输出，降低了开发独立可视化工具的对接成本。总体而言，商业许可和输入语言本身的壁垒较低，但 XML 解析和 Python 绑定的存在使得独立可视化工具的开发可行。</p>
<h2>阅读后回填</h2>
<ul><li>对问卷题目或选项的影响：Molpro 的 XML 输出和 pysjef/Python 接口为外部可视化工具提供了可行路径，可增补关于"解析标准化输出格式意愿"的题目</li><li>可转化为访谈追问的内容：用户对 gmolpro 的跨平台支持和 PQSMol viewer 协作的满意度</li><li>关键证据页码/图表：Sec. VIII.C (gmolpro), Sec. VIII.D (SJEF/pysjef/XML schema), Sec. IX.A (FCIdump), Table V (PNO-CASPT2 benchmark), Figure 5 (GUI 截图)</li><li>仍未解决的问题：Molpro 的具体许可证价格和条款未在本文披露；XML schema 对可视化工具的通用性有待验证</li></ul>
<h2>综合摘要</h2>
<p>本文（Werner et al., J. Chem. Phys. 152, 144107, 2020）系统综述了 Molpro 量子化学包的核心方法体系：单参考高精度相关方法（CCSD(T)-F12, PNO-LCCSD(T)-F12）、多参考方法（IC-MRCI, CASPT2-F12, CASSCF）、投影嵌入（Sec. IV.B）、SAPT 能量分解（Sec. IV.C）及非简谐振动谱（Sec. VI）。Molpro 的差异化竞争力在于其对大分子的局域相关方法与显式相关（F12）技术的深度融合。在可视化与互操作性方面，Molpro 通过 molpro-output XML 模式和 pysjef Python 模块（Sec. VIII.D）提供了结构化输出的后处理途径，FCIdump 格式（Sec. IX.A）支持多程序间 Hamiltonian 矩阵元交换，同时 gmolpro GUI（Sec. VIII.C）和 PQSMol 界面集成了结构构建与远程提交功能。这些特征表明，Molpro 的 XML+Pysjef 接口为独立外部可视化工具的构建奠定了良好基础。</p>
