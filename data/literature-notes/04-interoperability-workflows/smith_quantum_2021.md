# smith_quantum_2021

BibTeX key：`smith_quantum_2021`

## 摘要

Community efforts in the computational molecular sciences (CMS) are evolving toward modular, open, and interoperable interfaces that work with existing community codes to provide more functionality and composability than could be achieved with a single program. The Quantum Chemistry Common Driver and Databases (QCDB) project provides such capability through an application programming interface (API) that facilitates interoperability across multiple quantum chemistry software packages. In tandem with the Molecular Sciences Software Institute and their Quantum Chemistry Archive ecosystem, the unique functionalities of several CMS programs are integrated, including CFOUR, GAMESS, NWChem, OpenMM, Psi4, Qcore, TeraChem, and Turbomole, to provide common computational functions, i.e., energy, gradient, and Hessian computations as well as molecular properties such as atomic charges and vibrational frequency analysis. Both standard users and power users benefit from adopting these APIs as they lower the language barrier of input styles and enable a standard layout of variables and data. These designs allow end-to-end interoperable programming of complex computations and provide best practices options by default.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> QCSchema、程序适配器与来源记录</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>QCDB/QCEngine 分别承担模式定义、验证、执行和程序适配中的哪些职责？</p>
<p><strong>回答：</strong></p>
<p>QCDB 和 QCEngine 是 QCArchive 生态系统的两个核心组件，职责明确分离（与 QCArchive/Smith 2021 论文互补）：</p>
<ul>
<li><strong>QCEngine（执行层）</strong>：提供统一 API 的执行运行时——"QCEngine is designed to provide this uniform API and is an I/O runner"（Section II A 区域）。职责包括：(1) 通过 JSON Schema（QCSchema）通信——"communicates through a JavaScript Object Notation (JSON) Schema, denoted QCSchema, thus automatically generating program input files from a consistent description"；(2) 适配各程序的核心单点计算能力——"individual CMS codes' core single-point capabilities"；(3) 从二进制、结构化或文本输出中提取标准化 QCSchema 字段——"harvesting binary, structured, or text output into standardized QCSchema fields"。</li>
<li><strong>QCDB（通用驱动层）</strong>：提供更高层的协同能力——"provides such capability through an application programming interface (API) that facilitates interoperability across multiple quantum chemistry software packages"（Abstract, p.1）。职责包括：(1) 提供通用计算函数——"common computational functions, i.e., energy, gradient, and Hessian computations as well as molecular properties such as atomic charges and vibrational frequency analysis"（Abstract, p.1）；(2) 降低输入语言障碍——"lower the language barrier of input styles and enable a standard layout of variables and data"（Abstract, p.1）。</li>
<li><strong>QCSchema（数据模式）</strong>：JSON Schema 定义的数据交换格式——"a QCSchema for quantum chemistry information exchange"（Section II A 区域）。两者都使用 QCSchema 进行输入输出定义。</li>
<li><strong>验证职责</strong>：QCSchema 层提供基本的 schema 验证；QCElemental（同一生态系统的组成部分）提供"validation to layout (e.g., 3*Natoms coordinates) and physics (protons − electrons = charge) beyond what JSON Schema can specify"（来自 QCArchive 论文）。</li>
</ul>
<p><strong>对本项目的启示</strong>：QCEngine 的"适配器"模式可以直接借鉴——每个 QC 程序有一个适配器，将程序的特定输入/输出格式转换为标准 QCSchema。对于三维场数据，我们可以设计类似的 field schema（如 .cube → 统一场表示）和相应的程序适配器。</p>
<h3>问题 2</h3>
<p>标准接口如何同时服务普通用户并保留高级用户的程序特定关键字？</p>
<p><strong>回答：</strong></p>
<p>从 QCDB/QCEngine 的设计看，分层抽象是关键：</p>
<ul>
<li><strong>在 QCSchema 中保留程序特定关键词</strong>：论文指出需要"a light hand in developing the QCSchema translation and common driver API so that existing expertise in direct program usage is not obsoleted" ——意思是在设计标准接口时要轻量化，不废弃高级用户已有的程序特定知识。标准化的 QCSchema 字段涵盖了通用计算需求，同时 schema 中允许传递 program-specific keywords。</li>
<li><strong>"Both standard users and power users benefit"</strong>（Abstract, p.1）——标准用户获得统一接口的好处（不必学习多个程序的 DSL），高级用户仍可在标准化框架内控制程序特定选项。</li>
<li><strong>输入可预测性</strong>："both QCEngine and QCDB have striven to make their input predictable from uniformity at the input, output, and cross-program layers"——无论使用哪个后端程序，输入格式保持一致，但通过额外字段暴露各程序特有功能。</li>
<li>这与 QCElemental（来自 QCArchive 论文）的"模型增强"设计一致——在基础 QCSchema 之上提供"convenience functions"但保留底层访问。</li>
</ul>
<p><strong>跨论文验证</strong>：WebMO（Section 1.3）采取了"Reasonable defaults for novices, full access for advanced users"的相同策略。Digichem（p.1700）的"约 24 万预置方法 + 可编辑 YAML 方法"也是类似的分层设计。</p>
<h3>问题 3</h3>
<p>哪些原生输入、输出、单位转换和版本来源必须与派生场共同保存？</p>
<p><strong>回答：</strong></p>
<p>基于 QCDB/QCEngine/QCArchive 的设计原则，与派生场（如电子密度 cube 文件、静电势网格）共同保存的元数据应包括：</p>
<ol>
<li><strong>程序版本和来源</strong>：计算使用的程序名称、版本号、QCEngine/QCDB 版本。"All computations that are evaluated with QCENGINE return provenance information including program version, hardware specification, physical resources utilized, and QCENGINE version"（来自 QCArchive 论文，Section 3.2.2）。</li>
<li><strong>输入参数</strong>：用于生成该场的完整计算参数——method、basis、solvation model、SCF 收敛标准等。QCSchema 的 AtomicInput 类型提供了标准化输入描述。</li>
<li><strong>单位系统</strong>：QCElemental 的核心设计之一是"units conversion tools"和"atomic units to minimize susceptibility such details"（来自 QCArchive 论文，Section 3.2.1）。所有网格数据的单位（如 Bohr⁻³ 对于电子密度）必须明确记录。</li>
<li><strong>物理常数版本</strong>：使用的物理常数版本（如 CODATA2018），"with a context switch, conversions to older data are reproducible"（QCArchive 论文，Section 3.2.1）。</li>
<li><strong>分子坐标和取向</strong>：计算使用的原子坐标和分子取向（对于场数据特别重要，因为相同的轨道在不同分子取向下的网格表示不同）。</li>
<li><strong>基组和泛函选择</strong>：Basis Set Exchange 的版本标识 + libxc functional ID/版本。</li>
</ol>
<p><strong>关键启示</strong>：QCSchema 的"同时保存输入、来源和输出在同一文档"的做法是确保场数据可复现的最佳实践。我们的元数据格式应包含完整的 QCSchema 记录（或等效信息），以便用户后续可以追溯"这个 .cube 文件是从哪个计算来的、用的什么程序什么版本什么参数"。</p>
<h2>阅读后回填</h2>
<ul>
<li><strong>对问卷题目或选项的影响：</strong> 模块 C（三维场文件格式）应包含问题"你是否需要场数据文件携带完整的计算来源信息（程序版本、输入参数等）？"；模块 D（引擎无关工作流概念）可以引用 QCSchema 作为标准化数据模式的例子。</li>
<li><strong>可转化为访谈追问的内容：</strong> "如果你拿到一个 .cube 文件但没有计算来源信息，你还能信任这个场数据吗？" "QCSchema 的 JSON 格式适合描述三维场数据的元数据吗？有什么不足？"</li>
<li><strong>关键证据页码/图表：</strong> Abstract p.1（QCDB/QCEngine 职责、支持的程序列表、通用计算函数）；Section II A 区域（QCSchema 和 QCEngine 的 JSON Schema 通信、输入可预测性设计、"a light hand" 不废弃已有专业知识的原则）。</li>
<li><strong>仍未解决的问题：</strong> QCDB/QCEngine 论文未直接处理三维场数据（轨道/密度/静电势网格）的 QCSchema 定义——QCSchema 主要关注标量结果（能量、梯度、Hessian）和原子性质（电荷、频率）。三维场数据的 schema 扩展仍在开发中。</li>
</ul>
<h2>总结</h2>
<p>QCDB/QCEngine 是 MolSSI QCArchive 生态系统的核心执行层，提供了标准化 API 和程序适配器模式。对本项目的五个关键启示：(1) QCEngine 的"I/O runner + JSON Schema"模式是我们字段适配器最直接的参考架构——建立一个"Field Engine"层，将各程序的场输出（.cube、.grid、.molden）转换为标准化场表示；(2) QCSchema 的分层设计（不废弃程序特定知识）提示我们的场 schema 也应为各程序特有的网格属性（如不同的立方网格约定）保留扩展字段；(3) 统一输入/输出的理念可直接应用于可视化管道，使得"无论计算来自哪个程序，可视化接口保持一致"；(4) "同时保存输入+来源+输出"的文档设计是场数据可复现性的基础；(5) QCDB/QCEngine 支持的 CFOUR、GAMESS、NWChem、Psi4、TeraChem、Turbomole 等程序列表（Abstract, p.1）也提示了未来需要支持其 cube/场输出的程序优先级。</p>
