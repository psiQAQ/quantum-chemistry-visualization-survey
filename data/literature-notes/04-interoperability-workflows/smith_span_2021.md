# smith_span_2021

BibTeX key：`smith_span_2021`

## 摘要

Abstract The Molecular Sciences Software Institute's (MolSSI) Quantum Chemistry Archive (QCA rchive ) project is an umbrella name that covers both a central server hosted by MolSSI for community data and the Python‐based software infrastructure that powers automated computation and storage of quantum chemistry (QC) results. The MolSSI‐hosted central server provides the computational molecular sciences community a location to freely access tens of millions of QC computations for machine learning, methodology assessment, force‐field fitting, and more through a Python interface. Facile, user‐friendly mining of the centrally archived quantum chemical data also can be achieved through web applications found at https://qcarchive.molssi.org . The software infrastructure can be used as a standalone platform to compute, structure, and distribute hundreds of millions of QC computations for individuals or groups of researchers at any scale. The QCA rchive I nfrastructure is open‐source (BSD‐3C), code repositories can be found at https://github.com/MolSSI , and releases can be downloaded via PyPI and Conda. This article is categorized under: Electronic Structure Theory {\textgreater} Ab Initio Electronic Structure Methods Software {\textgreater} Quantum Chemistry Data Science {\textgreater} Computer Algorithms and Programming

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> QCArchive、数据模型与可追溯</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>QCArchive 如何把输入、输出、程序版本和执行来源组织为可复现记录？</p>
<p><strong>回答：</strong></p>
<p>QCArchive 通过多层架构实现完整的可复现记录：</p>
<ul>
<li><strong>QCSCHEMA 标准化文档</strong>：所有计算输入输出都使用 QCSCHEMA 规范文档（如 AtomicInput 描述原子计算的标准输入），"the input QCSCHEMA completely specifies and documents the calculation that was performed"（Section 3.2.2, p.5）。</li>
<li><strong>同一文档整合输入/来源/输出</strong>："The inputs, provenance, and outputs of a calculation are all stored in the same document, which makes it less likely that metadata will be lost"（Section 3.2.2, p.5）。这是阻止元数据丢失的关键设计。</li>
<li><strong>自动收集来源信息</strong>：QCENGINE 自动记录 "program version, hardware specification, physical resources utilized, and QCENGINE version"（Section 3.2.2, p.5）。</li>
<li><strong>唯一标识符防重复</strong>：QCFRACTAL 数据库中每个对象有唯一标识符，"a single-point computation: molecule, method, basis, program, and a unique keyword set identifier, which together describe a unique computation"（Section 3.2.3, p.6）。</li>
<li><strong>物理常数版本控制</strong>：QCELEMENTAL 收集物理常数并标定上下文（如 CODATA2018），"with a context switch, conversions to older data are reproducible"（Section 3.2.1, p.4）。数值以原子单位存储以"minimize susceptibility to such details"。</li>
<li><strong>软件最佳实践</strong>：整个项目遵循 MolSSI 的软件"Best Practices"指南，月度发布周期自动部署到 PyPI、Conda 和 Docker（Section 3.1, p.3-4）。</li>
</ul>
<p>关键启示：本项目可借鉴"同一文档保存输入/来源/输出"的设计模式，将三维场数据的源程序、程序版本、输入参数、输出轨道信息与元数据捆绑在同一容器中。</p>
<h3>问题 2</h3>
<p>能量/梯度/Hessian 与轨道/场数据应如何关联到同一计算记录？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>引用式关联</strong>：QCFRACTAL 采用关系型数据库（PostgreSQL），"each type of QCELEMENTAL object stored (Molecule, AtomicResult, etc.) has its own table"（Section 3.2.3, p.6）。Composite 对象（如 AtomicResult）"reference the required table instead of storing the object"——例如单个 Molecule 对象可以被多个能量或梯度计算引用，不会被重复存储，且所有相关计算可快速查询（Section 3.2.3, p.6）。</li>
<li><strong>轨道数据的特殊处理</strong>：论文特意指出 "Orbitals and other atomic orbitals quantities radically increase the total size and are stored sparingly"（Footnote 1, p.12）。这表明即使在 QCArchive 中，轨道/场数据的存储策略与标量数据（能量/梯度/Hessian）有本质不同。</li>
<li><strong>支持的数据类型</strong>：MQCAS 存储的数据包括 "geometries, energies, nuclear Hessians, electrostatic potentials, and wavefunctions"（Section 1, p.2）——其中 wavefunctions 和 electrostatic potentials 就是三维场数据的前身。</li>
<li><strong>对本项目的启示</strong>：能量/梯度等小数据可采用 QCArchive 的引用模式（直接关联到同一 Molecule 记录），而轨道系数/波函数/三维场数据应"sparingly"存储，可采用外部文件（如 .cube / OpenVDB）引用而非嵌入数据库记录。QCArchive 的 "unique identifier" 机制可用于将场数据与标量计算记录关联。</li>
</ul>
<h3>问题 3</h3>
<p>用户愿意上传、共享或本地部署哪些数据，隐私和数据规模会造成什么障碍？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>本地部署支持</strong>：QCARCHIVE INFRASTRUCTURE 支持完全私有实例——"Private QCARCHIVE INFRASTRUCTURE instances are fully as capable as MQCAS but are isolated from the public MQCAS"（Section 2, p.3）。用户有完全控制权，仅在用户选择时才共享数据。</li>
<li><strong>成本激励</strong>：论文的核心论点是存储的巨大性价比——"storing QC data is currently 50,000–100,000 times cheaper than regenerating it"（Section 1, p.2）。估计每 TB 可存储 5 亿个计算结果（成本 $200–500），对应约 10 亿美元的计算时间。</li>
<li><strong>多领域专业技能门槛</strong>："A common complaint that MolSSI has heard after interviewing over a hundred groups is that producing useful large-scale quantum chemical research datasets requires considerable specialized expertise in multiple domains (cheminformatics, QC, high-performance computing, database management)"（Section 1, p.2）。</li>
<li><strong>数据审核</strong>：社区提交的数据集"are reviewed by the MQCAS maintainers for integrity and often require comparisons to literature values to ensure consistency before accepting a contribution"（Section 2, p.3）。</li>
<li><strong>轨道数据规模的限制</strong>：虽然标量数据仅 ~2 kB/计算，但 "Orbitals and other atomic orbitals quantities radically increase the total size and are stored sparingly"（Footnote 1, p.12）。对于本项目涉及的三维场数据（cube 文件通常 10-100 MB+），存储策略必须谨慎设计。</li>
</ul>
<h2>阅读后回填</h2>
<ul>
<li><strong>对问卷题目或选项的影响：</strong> 模块 C（三维场文件格式）应包含问题"你是否愿意将轨道/场数据上传到共享仓库（类似 QCArchive）？"；模块 B 应纳入"计算的可复现性记录（程序版本、输入参数、来源信息）是否影响你对结果可信度的评估？"</li>
<li><strong>可转化为访谈追问的内容：</strong> "你的计算数据目前保存在哪里（本地、共享仓库、补充材料）？共享计算数据时你最担心什么（隐私、格式、工作量、版权）？" "你是否愿意为三维场数据建立类似 QCArchive 的共享仓库？"</li>
<li><strong>关键证据页码/图表：</strong> Section 3.2.2 p.5（同一文档存储输入/来源/输出 + 自动来源记录）；Section 3.2.3 p.6（唯一标识符 + 对象引用结构）；Section 1 p.2 + Footnote 1 p.12（标量 vs 轨道数据规模差异的量化证据）；Figure 4 p.7（架构通信图）。</li>
<li><strong>仍未解决的问题：</strong> 论文未详细说明 QCSCHEMA 如何具体描述轨道/wavefunction 数据；也未讨论场数据（如静电势网格、电子密度 cube）的标准表示方式。</li>
</ul>
<h2>总结</h2>
<p>QCArchive 是 FAIR 数据原则在量子化学中的成功实践——其 MQCAS 已包含 1400 万个分子和 2100 万条计算结果记录。对本项目的关键启示：(1) "同一文档存储输入+来源+输出" 的设计模式可直接应用于三维场数据的元数据封装；(2) 对象引用（而非复制）的数据库模式提示场数据应通过唯一标识符与计算记录关联，而非内嵌存储；(3) 轨道/场数据的 "size radically increase" 问题必须在设计之初就考虑——我们的 OpenVDB 方法本质上正是解决大规模稀疏场数据高效存储的方案；(4) 私有部署支持是缓解用户隐私顾虑的关键功能。</p>
