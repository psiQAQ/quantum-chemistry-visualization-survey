# lehtola_free_2022

BibTeX key：`lehtola_free_2022`

## 摘要

Abstract After decades of waiting, computational chemistry for the masses is finally here. Our brief review on free and open source software (FOSS) packages points out the existence of software offering a wide range of functionality, all the way from approximate semiempirical calculations with tight‐binding density functional theory to sophisticated ab initio wave function methods such as coupled‐cluster theory, covering both molecular and solid‐state systems. Combined with the remarkable increase in the computing power of personal devices, which now rivals that of the fastest supercomputers in the world in the 1990s, we demonstrate that a decentralized model for teaching computational chemistry is now possible thanks to FOSS packages, enabling students to perform reasonable modeling on their own computing devices in the bring your own device (BYOD) scheme. FOSS software can be made trivially simple to install and keep up to date, eliminating the need for departmental support, and also enables comprehensive teaching strategies, as various algorithms' actual implementations can be used in teaching. We exemplify what kinds of calculations are feasible with four FOSS electronic structure programs, assuming only extremely modest computational resources, to illustrate how FOSS packages enable decentralized approaches to computational chemistry education within the BYOD scheme. FOSS also has further benefits driving its adoption: the open access to the source code of FOSS packages democratizes the science of computational chemistry, and FOSS packages can be used without limitation also beyond education, in academic and industrial applications, for example. This article is categorized under: Software {\textgreater} Quantum Chemistry

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 教育、许可与低门槛采用</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>FOSS 在教学中的主要优势是许可、可审计、可修改还是可远程访问，哪些应单独测量？</p>
<p><strong>回答：</strong>论文明确指出 FOSS 的三大教学优势（Section 2.3, p.6-7）：(1) <strong>自由再分发/安装简便</strong>（Section 2.3.1, p.6）——软件可通过包管理器"一条命令"或图形界面"点击安装"完成，且包管理器自动更新（"Linux package managers not only handle updates to the Linux operating system kernel, but also all other software"）；(2) <strong>源代码可访问/可审计性</strong>（Section 2.3.2, p.6-7）——可直接用于教学演示算法实现，如 Psi4Numpy 和 PySCF 的 Python 可定制性（"PySCF...makes it easy to override and customize all algorithms, as they are mostly written in Python"）；(3) <strong>复杂工作流与技能培养</strong>（Section 2.3.3, p.7）——包括编程、接口开发、自动化工作流（AiiDA、QCEngine 等），且 FOSS 可自由用于工业界（"FOSS can also be freely used without limitations in industry"）。论文明确区分了"可远程访问"（Section 1, p.2 讨论的 WebMO/Chem Compute 模式）与 FOSS 的自身优势——远程访问并非 FOSS 独有，FOSS 的核心竞争力在于许可自由、代码可审计性和可修改性。特别以"<strong>过冷水之争</strong>"（Section 2.1, p.3-4）说明代码可审计性对科学可重复性的关键作用——Princeton 团队直到获得 Berkeley 竞争对手的源代码才发现其中的粗误差（"found a coarse error therein"）。此外，论文的 <strong>Libxc 案例研究</strong>（Section 2.2.4, p.5）展示了模块化 FOSS 组件如何实现 600+ 泛函的单点实现并被 30+ 电子结构程序共享——这是"许可+可审计+可修改"组合优势的证据。建议在问卷中分别测量：自由许可权、代码可审计性/可重复性、可修改性/可分支性、安装部署简便性（含自动更新）。</p>
<h3>问题 2</h3>
<p>BYOD、机房、Web 服务和本地安装各自带来什么门槛？</p>
<p><strong>回答：</strong>论文详细比较了四种模式的门槛（Section 1, p.2；Section 2.4, p.7-8；Section 2.3.1, p.6）：<br/><br/>
(1) <strong>计算机机房模式</strong>——可扩展性有限（"the number of students is limited by the number of workstations"）、需物理访问（"limited by the requirement of physical access to the computer classroom"）、疫情期间暴露脆弱性（coronavirus pandemic, social distancing）、软件需预装且由专人维护（"Someone also has to maintain the software on the classroom computers"）、许可证费用高昂（"costs for the required software licenses can be unfeasibly high"）。<br/><br/>
(2) <strong>Web 服务模式</strong>（WebMO/Chem Compute）——需专人设置和管理服务器（"still requires someone to set up and administer the WebMO server"），即使云平台亦然；Chem Compute 依赖第三方志愿计算资源"持续可用性无法保证"（"relies on computational resources volunteered by third parties whose continued future availability is not guaranteed"）（Section 1, p.2）。<br/><br/>
(3) <strong>本地安装模式（传统闭源软件）</strong>——编译安装需专业技能，编译器选项不当会导致性能不佳（"Compiling from source takes a lot of time as well as expertise, and can lead to poor performance if the compiler options are not adequately chosen"）（Section 2.3.1, p.6）。<br/><br/>
(4) <strong>BYOD + FOSS 模式</strong>——论文强烈推荐。论据包括：(a) 学生个人设备算力已达 1990 年代顶级超算水平（Figure 1, p.8——Celeron N4000 平价平板与 Core i7-10610U 高端商务笔记本的 GFLOPS 与 TOP500 超算的历史对比）；(b) 包管理器实现"无痛安装和自动更新"（"trivially simple to install and keep up to date"），无需系级支持；(c) 适用于大规模开放在线课程（"ideally suited for massive open online courses (MOOCs)"）；(d) 芬兰赫尔辛基大学理学院的实践经验——"pivoted to such an approach several years ago"（Section 2.4, p.7）。</p>
<h3>问题 3</h3>
<p>学生与研究人员对 GUI、教程、脚本和科学透明度的优先级是否应分别分析？</p>
<p><strong>回答：</strong>是。论文提供了多个维度的证据，隐式区分了不同用户群体的需求：<br/><br/>
<strong>(1) GUI/可视化需求</strong>——Section 3.5.5（p.14）讨论 Jmol、Avogadro、IQmol、PyMol 等前端工具，指出"simplified frontends are often invaluable for initializing, visualizing, and analyzing calculations"。论文也提到"可视化与数据格式互操作的挑战"——"a common file format is needed for various quantum chemistry codes"——这恰好是本项目的切入点。<br/><br/>
<strong>(2) 教程/教学材料需求</strong>——Section 5（p.25）提到 Psi4Education 等开放教学资源，以及论文本身在 Section 4 提供详细的逐步教学示例（包含四种 FOSS 电子结构程序的具体版本号、安装命令、输入文件和输出分析）。论文强调"teaching no longer has to be limited to pen and paper exercises: instead, it can also include real-life demonstrations"（Section 2.3.2, p.7）。<br/><br/>
<strong>(3) 脚本/编程技能需求</strong>——Section 2.3.3（p.7）强调学生可"develop more general technical skills such as programming and interfacing programs with each other"；AiiDA、QCEngine 等工作流工具可自动化复杂任务；PySCF 的 Python 原生 API 使算法修改和扩展成为可能。<br/><br/>
<strong>(4) 科学透明度需求</strong>——最为根本。Section 2.1（p.3-4）以 FOSS 三标准和"过冷水之争"论证代码可审阅性对科学公正性的关键作用。论文指出"anyone who has a copy of the software can redistribute it to others...eliminating the problematic role of the gatekeepers"（p.4），这意味着科学透明度可独立于 GUI 或教程需求而单独测量。<br/><br/>
<strong>(5) 商业可持续性的证据</strong>——论文提到 Kitware Inc.（Section 2.2.2, p.4）成功基于 FOSS 构建商业模式（ParaView、CMake），以及 Red Hat（p.4）年收入超过 30 亿美元，证明 FOSS 在科学计算领域的商业可行性——这种"开发者可持续性"维度也应纳入问卷。<br/><br/>
<strong>问卷建议：</strong>建议在调查中按用户角色分别询问对不同维度的优先级：入门学生（GUI + 教程）、进阶学生（脚本 + 透明度）、研究人员（透明度 + 脚本）、教师/课程设计者（教程 + 安装简便性）。</p>
<h2>阅读后回填</h2>
<ul>
<li>对问卷题目或选项的影响：建议增加"部署与安装方式"作为模块A的区分变量（BYOD/机房/Web服务/本地HPC），增加"代码可审计性"作为"选择信任度"模块B的重要维度；Section 3.5.5(p.14)关于格式互操作性的讨论可直接引用到模块D（引擎无关工作流）的问题设计中。Libxc案例（Section 2.2.4, p.5）可作为问卷中"模块化组件选择"选项设计的示例。</li>
<li>可转化为访谈追问的内容：(a) 用户是否有过因闭源代码不可审计而导致研究争议的经历？(b) 在 BYOD 场景中使用量子化学软件时遇到的最大安装/部署障碍是什么？(c) 用户是否使用过 Open Babel/cclib 等格式转换工具？遇到什么问题？(d) 用户对"包管理器自动更新"的体验如何，是否有因更新导致工作流中断的经历？</li>
<li>关键证据页码/图表：FOSS 三标准定义——Section 2.1 (p.3-4)；BYOD 算力论证——Figure 1 (p.8)；机房 vs Web vs BYOD 门槛对比——Section 1 (p.2)；可视化工具与互操作挑战——Section 3.5.5 (p.14)；工作流案例——Section 4 (p.15-25)；Libxc 模块化成功案例——Section 2.2.4 (p.5)；Kitware/Red Hat 商业模式——Section 2.2.2 (p.4)；自动更新机制——Section 2.3.1 (p.6)</li>
<li>仍未解决的问题：论文未讨论可视化领域的格式标准化进展（如 OpenVDB 等现代体积数据标准），也未涉及 GPU 加速渲染与科学可视化集成的具体路径。论文对"过冷水之争"的描述仅作为 FOSS 可审计性优势的例证，未讨论代码可审计性在不同子领域（如周期性计算 vs 分子计算）中的具体重要性差异。</li>
</ul>
<h2>总结与本项目关联</h2>
<p><strong>核心发现：</strong>(1) FOSS 已使"计算化学大众化"成为可能——BYOD 模式在算力上完全可行（Figure 1 显示平价笔记本已达 1990 年代顶级超算水平），且包管理器和自动更新使安装维护不再是障碍；(2) 格式互操作是计算化学领域未解决的长期难题——Section 3.5.5 (p.14) 明确指出"a common file format is needed for various quantum chemistry codes"，没有普遍接受的标准；(3) 模块化库（如 Libxc，Section 2.2.4）的成功证明可复用组件模式在量子化学软件生态中有效；(4) FOSS 的"gatekeeper elimination"效应（Section 2.1, p.4）对科学可重复性具有根本性意义。<br/><br/>
<strong>与本项目关联：</strong>论文指出的互操作真空正是本项目 .cube → OpenVDB → Blender 工作流的科学依据和市场机会；论文对 FOSS 教学优势（特别是 BYOD 模式的可行性）的全面论证可直接支撑问卷中教育模块的假设设计；论文提到的 Linux 包管理器自动更新机制对可维护性的贡献可指导项目部署策略设计。<br/><br/>
<strong>对问卷设计的具体建议：</strong>提供了"技术人员 vs 终端用户"的分层视角，建议在模块B（选择可信度因素）和模块D（引擎无关工作流）中区分这两类受访者的需求；Libxc 案例可在模块B中作为"可复用组件"的具象化选项；建议增设"部署环境兼容性"作为项目工具的评价维度。</p>
