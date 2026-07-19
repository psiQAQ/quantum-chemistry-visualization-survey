# alegrerequena_aqme_2023

BibTeX key：`alegrerequena_aqme_2023`

## 摘要

Abstract AQME, automated quantum mechanical environments, is a free and open‐source Python package for the rapid deployment of automated workflows using cheminformatics and quantum chemistry. AQME workflows integrate tasks performed across multiple computational chemistry packages and data formats, preserving all computational protocols, data, and metadata for machine and human users to access and reuse. AQME has a modular structure of independent modules that can be implemented in any sequence, allowing the users to use all or only the desired parts of the program. The code has been developed for researchers with basic familiarity with the Python programming language. The CSEARCH module interfaces to molecular mechanics and semi‐empirical QM (SQM) conformer generation tools (e.g., RDKit and Conformer–Rotamer Ensemble Sampling Tool, CREST) starting from various initial structure formats. The CMIN module enables geometry refinement with SQM and neural network potentials, such as ANI. The QPREP module interfaces with multiple QM programs, such as Gaussian, ORCA, and PySCF. The QCORR module processes QM results, storing structural, energetic, and property data while also enabling automated error handling (i.e., convergence errors, wrong number of imaginary frequencies, isomerization, etc.) and job resubmission. The QDESCP module provides easy access to QM ensemble‐averaged molecular descriptors and computed properties, such as NMR spectra. Overall, AQME provides automated, transparent, and reproducible workflows to produce, analyze and archive computational chemistry results. SMILES inputs can be used, and many aspects of tedious human manipulation can be avoided. Installation and execution on Windows, macOS, and Linux platforms have been tested, and the code has been developed to support access through Jupyter Notebooks, the command line, and job submission (e.g., Slurm) scripts. Examples of pre‐configured workflows are available in various formats, and hands‐on video tutorials illustrate their use. This article is categorized under: Data Science {\textgreater} Chemoinformatics Data Science {\textgreater} Computer Algorithms and Programming Software {\textgreater} Quantum Chemistry

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 自动化、可复现与用户门槛</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>AQME 自动化了哪些准备、计算、分析和质量控制步骤？</p>
<p><strong>回答：</strong></p>
<p>AQME 通过 5 个独立模块覆盖端到端工作流的所有环节（Figure 1, p.2）：</p>
<ul>
<li><strong>CSEARCH（构象搜索）</strong>：自动从 SMILES 或 3D 格式生成构象。接口到 RDKit（MM）和 CREST（SQM/xTB），并内置 SUMM（系统扭转扫描）和 MCMM（蒙特卡洛）算法。自动处理带金属和异常杂化原子的构象（如使用 square-planar 模板），失败时自动降级协议（如从 MMFF 切换到 UFF，Section 3.1, p.3-4）。</li>
<li><strong>CMIN（几何精炼）</strong>：使用 xTB 或 ANI（机器学习势）精炼构象能量和几何，剔除重复构象（Section 3.1, p.4, Figure 2a, p.4）。</li>
<li><strong>QPREP（QM 输入准备）</strong>：自动将多种 3D 格式（SDF、XYZ、PDB、JSON、LOG/OUT）转换为 Gaussian、ORCA、PySCF 的输入文件。自动生成 GenECP 部分（例如为 Pd 原子使用 def2-SVP，为 C/H/N 使用 6-31+G(d,p)，Figure 2b, p.4）。"This automated protocol avoids the tedious manual setup of all the types of atoms in the GenECP part"（Section 3.2, p.5）。</li>
<li><strong>QCORR（后处理与质量控制）</strong>：基于 cclib 检测和分析 QM 输出文件。自动处理收敛错误、虚频、异构化、自旋污染（S² 差异超过 10% 则过滤，Section 3.3, p.5）。生成校正后的输入文件并自动重新提交。论文提供了量化基准证据："In the first round, 24% of the RDKit-generated conformers converged to duplicated QM conformers... After the second round... 98% of the outputs had no issues"（Section 3.3, p.5）。数据以 JSON 格式存储。</li>
<li><strong>QDESCP（Boltzmann 加权描述符）</strong>：基于 xTB 生成原子级（电荷、Fukui 指数、弥散系数）和分子级（偶极矩、HOMO-LUMO gap、极化率）描述符，进行 Boltzmann 平均。还支持 DFT NMR 化学位移的 Boltzmann 加权平均与 TMS 标度（Section 3.4, p.5-6, Figure 2d, p.4）。</li>
</ul>
<p>整体上，AQME 实现了从 SMILES 输入到 Boltzmann 加权性质输出的完整自动化管道，中间不需要人工操作或电子表格。</p>
<h3>问题 2</h3>
<p>面向研究者与教育用户时，Python 基础、教程和默认协议分别造成什么门槛？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>Python 基础门槛</strong>：AQME 明确定位于"researchers with basic familiarity with the Python programming language"（Abstract, p.1）。这对教育用户可能构成入门障碍——虽然 Python 比纯文本输入更现代化，但完全不熟悉编程的用户仍需要学习基本语法。不过，AQME 同时支持 Jupyter Notebook、命令行和 SLURM 脚本三种使用模式（Abstract, p.1），Jupyter 交互式环境降低了编程门槛。</li>
<li><strong>教程支持</strong>：论文提到了 GitHub 上的预配置工作流示例库、"Read the Docs"详细文档页和 "hands-on video tutorials"（Abstract, p.1; Section 2, p.3）。YouTube 频道提供实操教程。这对于弥合教育用户的门槛很重要，但论文未评估这些教程的效果。</li>
<li><strong>默认协议的合理性和风险</strong>：AQME 的关键默认参数基于实验基准——如 CSEARCH 的能量窗口默认 5 kcal/mol、能量阈值 0.25 kcal/mol、RMSD 阈值 0.25 Å，"chosen based on a benchmark study of flexible druglike compounds and natural products"（Section 3.1, p.4）。QCORR 的"两个循环修复 98%"也是基于 2709 个计算的系统基准数据（Section 3.3, p.5）。这种基于数据的默认值设计降低了"默认值不合理"的风险。但论文也承认"an additional cycle is normally needed for complex systems with metals or supramolecular aggregates"（Section 3.3, p.5），说明默认协议在复杂体系上仍需调整。</li>
<li><strong>更大的门槛</strong>：管道中涉及的第三方软件（RDKit、xTB/CREST、Gaussian/ORCA/PySCF）的安装和配置可能成为教育场景的更大障碍。AQME 通过 conda-forge 简化了依赖管理（Section 2, p.3），但前端包（如 PySCF 或商业 Gaussian）仍需用户自行处理。</li>
</ul>
<h3>问题 3</h3>
<p>哪些自动化结果与元数据最适合直接进入可视化或问卷功能优先级？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>QCORR 的 JSON 存储</strong>：QCORR "stores all parsed data as JSON files since this format allows other Python tools to retrieve the information easily"（Section 3.3, p.5）。这正是我们需要的引擎无关中间格式——JSON 元数据 + 外部位姿数据（如 .cube）的组合，可以直接桥接到可视化管道。</li>
<li><strong>QDESCP 的描述符</strong>：原子电荷、Fukui 指数、偶极矩、HOMO-LUMO gap 等描述符本身就是可视化的候选对象。其中原子级性质（如 Mulliken 电荷）可在 Blender 中以分子表面着色形式展示。</li>
<li><strong>构象系综</strong>：CSEARCH 生成的构象系综和多步骤优化轨迹（Section 4.1, p.6, Figure 3）可直接导入 Blender 进行构象对比可视化。</li>
<li><strong>NMR 谱数据</strong>：Boltzmann 平均 NMR 化学位移（Section 4.1, p.6）可直接生成可视化谱图。</li>
<li><strong>与 PySCF 的接口</strong>：AQME 的 QPREP 模块"interfaces with multiple QM programs, such as Gaussian, ORCA, and PySCF"（Abstract, p.1; Section 3.2, p.4-5）。这意味着 AQME 生成的 PySCF 输入和计算结果可以直接进入我们的 PySCF → .cube → OpenVDB 管道。</li>
</ul>
<p><strong>对问卷的启示</strong>：问卷模块 E（PySCF 次级变量）和模块 F（开放反馈）应包含：(1) 用户是否在工作流中使用自动化管道（如 AQME）？(2) 用户最希望在哪个环节加入可视化（中间结果查看 vs 最终结果展示）？(3) 用户对 JSON 格式元数据 + 外部文件（如 .cube）的组合模式是否熟悉和接受？</p>
<h2>阅读后回填</h2>
<ul>
<li><strong>对问卷题目或选项的影响：</strong> 模块 D（引擎无关工作流概念）可引用 AQME 作为"模块化自动化管道"的案例；模块 A（背景与工作流）应包含问题"你是否使用过自动化工作流工具（如 AQME、FireWorks、AiiDA）？"</li>
<li><strong>可转化为访谈追问的内容：</strong> "你在使用自动化计算管道时，是否感觉中间结果的可见性不足？可视化介入的最佳时机是哪个步骤？" "你是否希望自动化工具直接输出可视化就绪的格式？"</li>
<li><strong>关键证据页码/图表：</strong> Figure 1 p.2（AQME 整体架构和模块连接图）；Section 3.3 p.5（QCORR 质量控制基准：24% 重复→98% 两轮修复）；Section 3.2 p.5（QPREP 对 PySCF 的支持和 GenECP 自动生成）；Section 3.3 p.5（JSON 数据存储策略）。</li>
<li><strong>仍未解决的问题：</strong> AQME 目前不直接支持三维场数据（如电子密度、静电势）的计算和输出。QCORR 的 JSON 存储格式虽然开放，但未定义如何与大数据格式（如 .cube、OpenVDB）关联。论文未讨论 AQME 的输出如何直接进入可视化管道。</li>
</ul>
<h2>总结</h2>
<p>AQME 展示了"模块化、端到端、可复现"的计算化学自动化工作流的现代范式。对本项目的六个关键启示：(1) AQME 直接支持 PySCF（QPREP 模块），证明了 PySCF 作为后端程序在自动化管道中的可行性；验证了我们选择 PySCF 作为目标程序的正确性；(2) QCORR 的 JSON 数据存储策略可作为本项目元数据格式的参考——JSON 存储结构性元数据，大二进制数据（如 Cube/OpenVDB）外部引用；(3) AQME 的"two-round fix 98%"基准测试方法值得借鉴：我们的格式转换管道也应对不同 QC 程序的 Cube 输出进行类似的多轮兼容性测试；(4) AQME 的默认参数基于系统基准实验的设计哲学，提示问卷应测量用户是否信任"合理的默认值"；(5) AQME 的教育定位（basic Python + tutorials + Jupyter）与我们的项目目标用户重叠，提示问卷应包含用户技能自评问题；(6) 虽然 AQME 不直接处理三维可视化，但其自动化管道为"从计算到可视化"的无缝集成提供了现成的上游框架。</p>
