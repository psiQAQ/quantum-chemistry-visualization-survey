# zapata_qmflows_2019

BibTeX key：`zapata_qmflows_2019`

## 摘要

We present the QMflows Python package for quantum chemistry workflow automatization. QMflows allows users to write complex workflows in terms of simple Python scripts. It supports the development of interoperable workflows involving multiple quantum chemistry codes and executes them efficiently on large scale parallel computers. This open source library provides standardized interfaces to a number of quantum chemistry packages and can be easily extended to accommodate additional codes. QMflows features are described and illustrated with a number of representative applications.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 并行工作流、HPC 与多程序自动化</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>QMflows 如何统一输入准备、队列提交、结果收集和后处理？</p>
<p><strong>回答：</strong></p>
<p>QMflows 通过四大模块实现完整工作流自动化（Figure 1, p.8）：</p>
<ul>
<li><strong>输入准备（Module 1, p.10-15）</strong>：基于 PLAMS 库的嵌套 Python 字典输入模型。提供跨程序的 <strong>generic keywords</strong>（如 basis、functional）和 <strong>templates</strong>（如 templates.geometry），用户通过简单的 Python 语句设置计算参数。"The translation from generic to specific keyword is stored as a dictionary that is easy to maintain and extend"（p.11）。核心示例：只需将 'adf' 替换为 'cp2k' 即可切换后端程序（p.12-13）。程序特定关键字通过 inp.specific 子段添加（p.14-15）。</li>
<li><strong>队列提交和工作流执行（Module 2, p.15-17）</strong>：基于 Noodles 框架，采用 <strong>"promised objects"</strong> 模式——"Rather than immediately executing the Python code found in a user script, Noodles will first construct a dependency graph"（p.15）。自动分析任务依赖关系，独立的计算任务并行执行。通过 run() 调用触发实际执行，支持本地线程、SLURM、PBS 等多种后端。</li>
<li><strong>结果收集和恢复（Module 3, p.17-18）</strong>：工作流和依赖关系存储在数据库中，"When the run command is invoked, noodles traverses the graph of job dependencies and checks against the database for a reference to the job results"（p.17-18）。如果某个任务失败或执行被中断，用户只需重新提交原始脚本——"Noodles will query the database for already existing results and execute only the tasks that were not yet successfully completed"（p.18）。</li>
<li><strong>后处理（Module 4, p.18-20）</strong>：区分 primary data（原生格式保留在计算节点）、output data（转换为可移植 HDF5 格式用于下一步）和 metadata（存储在数据库中用于工作流监控）。内置解析器提取坐标、能量、偶极矩、梯度、激发能、Hessian 等关键数据。用户也可使用 QM 程序自带的解析器或编写自定义解析器（p.19-20）。</li>
</ul>
<p><strong>对本项目的启示</strong>：QMflows 的"promised objects"延迟执行模式和自动依赖分析是实现工作流并行化的关键技术。我们的可视化管道也可以采用类似的延迟执行策略：定义可视化任务（如等值面生成、动画渲染）为依赖计算结果的 promised objects，当计算完成时自动触发可视化处理。</p>
<h3>问题 2</h3>
<p>复杂依赖、Python 经验和 HPC 环境中，哪项最限制非开发者采用？</p>
<p><strong>回答：</strong></p>
<p>论文分析指出三项限制因素<em>互为关联</em>，但 QMflows 的设计针对性给出了排位：</p>
<ul>
<li><strong>Python 经验可能是最直接的"入门"门槛</strong>：QMflows 明确的目标是让"users with only basic programming skills"（p.9）也能构建复杂工作流。论文承认"a basic understanding of the Python scripting language is required"（p.9），但同时指出这是"a skill that most computational chemists nowadays possess"（p.9）。</li>
<li><strong>复杂依赖管理是最大的"持续使用"障碍</strong>：论文指出大多数已有工作流工具要求用户手动指定并行化时机——"Parallelization of the workflow often requires input from the user who should indicate the stages in the simulation that can be run in parallel"（p.6）。QMflows 通过 Noodles 的自动依赖分析解决了这个问题——用户不需要手动管理依赖，"complex workflows can be reused, adapted and executed in high-performance computing environments"（p.8）。</li>
<li><strong>HPC 环境的复杂性是"面向特定场景"的障碍</strong>：论文指出"three common tasks need to be automated... the preparation of specific inputs for the QM software package(s)... submission of jobs to a queuing system on a supercomputer facility... collection and analysis of the output files"（p.5-6）。常用的 ad-hoc shell 脚本或 Python 脚本"are often poorly transferable and difficult to maintain and extend"（p.6）。</li>
<li><strong>时间成本——被忽略的关键因素</strong>："In most research groups, the time available to tune and document such scripts is limited, resulting in a suboptimal use of computational and human resources"（p.6）。即使有编程能力，投入时间维护脚本的意愿也是关键限制。</li>
</ul>
<p><strong>综合判断</strong>：三种障碍形成一个倒金字塔——底部是基础 Python 技能（大多数计算化学家已具备），中间是 HPC 环境的复杂性（通过 QMflows 的模板和解析器解决），顶层是自动依赖管理和并行化（QMflows 通过 Noodles 自动处理）。非开发者采用的最关键限制可能是"从简单脚本到健壮工作流的跨越"所需的时间和 Python 调试经验。</p>
<p><strong>对问卷的启示</strong>：模块 A（背景与工作流）应包含用户使用的自动化程度（完全手动 → 简单脚本 → 模板 → 工作流框架 → 自定义框架）的自评问题。</p>
<h3>问题 3</h3>
<p>可视化工具应作为工作流中的独立步骤、结果消费者还是可组合节点？</p>
<p><strong>回答：</strong></p>
<p>基于 QMflows 的架构模式：</p>
<ul>
<li><strong>结果消费者（最适合当前阶段）</strong>：QMflows 将解析后的计算数据存储在 HDF5 和数据库中（Module 4, p.19），且"primary data is kept in the native format of the quantum chemistry program that was used"（p.19）。可视化工具可以作为后处理阶段从这些存储中读取数据。QMflows 当前的后处理"includes the coordinates of the optimized geometry, the total energy of the system, dipole moments, gradient, excitation energies and Hessians matrices"（p.19-20），不包括三维场数据（轨道密度、电子密度等）。这为我们的项目留下了明确的"缺口"——扩展解析器以提取场数据。</li>
<li><strong>可组合节点（长期理想）</strong>：QMflows 的 Noodles 架构支持任何类型任务作为节点加入依赖图。理论上，可视化任务（如 Blender 渲染）可以作为依赖计算结果的节点——"a user can decide whether an error should be propagated to the rest of the dependencies"（p.26）。这意味着我们可以为 QMflows 编写一个 "Blender visualization" 插件/任务，作为 Noodles 工作流中的末级节点。</li>
<li><strong>不应是独立运行步骤</strong>：QMflows 的核心工作流是"pre-submit → run → gather"模式（Figure 2/3/4/5, p.21-26），可视化作为另一个独立进程会打破"postpone execution"的 promise 机制。可视化应该是 gather 阶段的一部分。</li>
</ul>
<p><strong>关键启示</strong>：可视化工具的理想集成方式是作为 QMflows（或类似工作流框架）的一个"任务节点"——它依赖上游计算结果（geometry + 场数据），通过 Noodles 的并行机制在计算完成后自动触发。这种集成比独立手动操作更高效且更可复现。</p>
<h2>阅读后回填</h2>
<ul>
<li><strong>对问卷题目或选项的影响：</strong> 模块 D（引擎无关工作流概念）应包含"可视化是否应集成到工作流中"的概念测试问题；模块 A 可询问"你使用过哪种方式管理工作流（手工/脚本/QMflows/AQME 等）？"</li>
<li><strong>可转化为访谈追问的内容：</strong> "你用的工作流工具中，可视化是自动触发的还是手动完成的？如果可视化能作为工作流的一个自动步骤，会带来什么好处？"</li>
<li><strong>关键证据页码/图表：</strong> Figure 1 p.8（QMflows 四大模块架构图——输入/执行/恢复/后处理）；Module 2 p.15-17（Noodles 的 promised objects 延迟执行和自动依赖分析）；Module 4 p.18-20（HDF5 可移植数据格式 + 解析器提取的关键数据类型）；Section "Conclusions" p.27-28（QMflows 四大设计支柱总结）。</li>
<li><strong>仍未解决的问题：</strong> QMflows 目前不解析或传递三维场数据（轨道密度、电子密度、静电势网格），其 HDF5 输出主要用于小数据（标量、向量、矩阵）。将 .cube 文件集成到 QMflows/Noodles 的数据传递管道中是一个开放问题。</li>
</ul>
<h2>总结</h2>
<p>QMflows 是专注于"可互操作并行工作流"的早期成功工具之一（2019），通过 PLAMS（输入模板）+ Noodles（依赖分析和并行）的组合降低了量化计算工作流的复杂度和门槛。对本项目的五个关键启示：(1) QMflows 的"generic keywords + templates"跨程序统一输入模式最有借鉴价值——我们的格式转换器也可以提供"一键转换"的模板，隐藏各程序 Cube 输出格式差异；(2) Noodles 的"promised objects"延迟执行模式是解决"计算完成后自动触发可视化"的理想方案——可视化可表达为依赖计算结果的 promise，计算完成时自动触发渲染；(3) QMflows 的"primary data 原生格式保留 + output data HDF5 转换"双层存储策略（Module 4, p.19），与我们的"原生 .cube + 转换为 OpenVDB"的两层策略一致；(4) QMflows 的 HDF5 数据格式仅覆盖标量和向量数据，三维场数据是明显的功能缺口——我们的项目可以直接填补这个缺口；(5) QMflows 支持 ADF、CP2K、DIRAC、GAMESS-US 和 ORCA（p.9），但未支持 PySCF——这提示我们的 PySCF-specific 工作流需要自建类似 QMflows 的适配器。</p>
