# di_felice_perspective_2023

BibTeX key：`di_felice_perspective_2023`

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 科研软件可持续性与互操作</p>
<p><strong>问题生成依据：</strong> 题名与 PDF 摘要段；Zotero 摘要字段为空</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>论文认为可持续软件最关键的基础设施是 API、模块化、测试、文档、社区还是人才培养？</p>
<p><strong>回答：</strong>论文认为上述所有要素都是必要的且相互依赖，但最核心的是<strong>模块化设计与稳定API</strong>以及<strong>互操作性</strong>。具体而言：<br/><br/>
1. <strong>模块化与API</strong>（第7066页）："computational chemistry software must be designed in a modular and encapsulated manner. The modules should be as decoupled as possible"；提供C/C++和Python API比语言本身更重要——"the computer language a module is written in tends to be less important than the languages for which it provides APIs for"；Python被定位为"glue language capable of calling disparate pieces of software"（第7066页）。<br/><br/>
2. <strong>互操作性</strong>（第7067页）：论文给出了严格定义——"interoperability means that two pieces of software 'just work' together... it must be possible to swap the components with no work other than telling the framework to use the new component"——即零胶水代码的理想状态。论文批评计算化学领域的重复造轮现象（"reinventing the wheel", 第7065页）。<br/><br/>
3. <strong>测试与文档</strong>（第7066-7067页）：需要"more than unit testing and includes the following: integration, performance, deployment, and acceptance testing"；"Developer and user documentation, tutorials, and resources are extremely important"。论文还提到代码覆盖率的必要性（"code coverage to ensure that the code is indeed exhaustively tested", 第7067页）。<br/><br/>
4. <strong>社区与人才培养</strong>（第7067页）："growing and retaining the overall computational chemistry community. Without users or developers working together, the field will slowly die"。特别提到开发者流失问题——"developers of scientific software are in high demand by industry"，以及"providing computer science and engineering resources, since many members of the community are scientists by trade"。<br/><br/>
5. <strong>Stewardship（软件看护）</strong>（第7066页）：定义为"tasks beyond initial method development meant to ensure that the software remains viable over the long-term"，包括语义版本控制、稳健部署策略和社区参与。<br/><br/>
6. <strong>PluginPlay框架</strong>（第7065页）：论文介绍PluginPlay——"a framework for developing modular scientific software"，用于"decouple the low-level and high-level layers"，是模块化设计的具体实现案例。<br/><br/>
<strong>对本项目的启示：</strong>项目构建的引擎无关工作流本质上就是在实践本文提倡的"互操作性"理想——通过通用中间格式（如OpenVDB）解耦计算引擎与可视化引擎，这正是模块化和API优先思想的体现。论文对stewardship五要素的操作化定义（稳定性、可访问性、可复现性、可靠性、社区参与）可直接作为评估工具可信度的框架。</p>
<h3>问题 2</h3>
<p>哪些互操作需求来自新硬件和多尺度工作流，哪些与本项目直接相关？</p>
<p><strong>回答：</strong><br/><br/>
<strong>来自新硬件的需求：</strong><br/>
1. <strong>加速器异构计算</strong>（第7063-7064页）：GPU、TPU等需要"performance portable"软件，传统CPU导向代码面临重构挑战；论文特别指出"AI-hardware's low mixed-precision floating-point operations add new challenges to the numerical accuracy, algorithm stability, and convergence estimates"；<br/>
2. <strong>Exascale资源利用</strong>（第7057页）：需要"seamless integration of several levels of parallelism"和"support for parallel models on new architectures and accelerators"；<br/>
3. <strong>低精度算术的利用</strong>（第7063-7064页）：AI硬件带来的数值精度挑战需要新的算法设计；<br/>
4. <strong>新的编程模型需求</strong>（第7057页）：需要"new domain-specific languages (DSLs) to deal with the algebraic complexity of models"和"operation/communication optimization of sparse models"。<br/><br/>
<strong>来自多尺度工作流的需求：</strong><br/>
1. <strong>QM/MM与QM/QM嵌入</strong>（第7061页）："coupling quantum methods with classical and continuum methods"以及不同方法论边界的无缝衔接。论文详细讨论了嵌入方法的分类（DFT embedding, DMET, projector-based embedding等）；<br/>
2. <strong>中层抽象层</strong>（第7064-7065页）：如PluginPlay——"a framework for developing modular scientific software"，用于"decouple the low-level and high-level layers"；<br/>
3. <strong>框架层互操作</strong>（第7065页）：ASE、NewtonX、SHARC、QCEngine等用于"abstracting (interfacing to) standalone packages"；<br/>
4. <strong>实时动力学与多时间尺度</strong>（第7061页）："coupling variable length-scale methodologies...will require variable time-scale simulations"。<br/><br/>
<strong>与本项目直接相关的：</strong><br/>
1. <strong>数据标准化与可访问性</strong>（第7066页）："The software and the data it produces should be standardized and curated to ensure both remain accessible at later times"——这正是本项目将.cube转换为OpenVDB的科学依据；<br/>
2. <strong>Python作为胶水语言</strong>（第7066页）："Python has emerged as a glue language capable of calling disparate pieces of software"——支持项目以Python为中心的架构设计；<br/>
3. <strong>元编程与可扩展性</strong>（第7066页）："meta-programming allows other developers to programatically, and noninvasively, extend and customize existing code"；<br/>
4. <strong>减少"reinventing the wheel"</strong>（第7065页）：论文批评计算化学领域重复造轮现象，本项目也旨在减少可视化管线的重复实现；<br/>
5. <strong>10个科学挑战领域</strong>（第7057-7058页）：电池技术、太阳能、催化、材料设计、生物化学、分离科学、重元素化学、气相化学、强场物理、超快科学——这些领域的计算输出（电子密度、静电势、激发态性质等）都是潜在的可视化对象，增加了对通用可视化管线的需求。</p>
<h3>问题 3</h3>
<p>survey 应如何区分用户感知的易用性与开发者关注的长期维护性？</p>
<p><strong>回答：</strong><br/><br/>
论文明确区分了这两个维度，为问卷设计提供了框架：<br/><br/>
<strong>1. 用户感知的易用性（User-Facing Ease of Use）：</strong><br/>
- "easy setup and reliable access"（第7066页）；<br/>
- "user-friendly for complex chemical workflows"（第7067页）；<br/>
- Python API作为"glue"能力降低集成门槛（第7066页）；<br/>
- 用户社区参与的重要性——"engaging the user community ensures that the software gets used"（第7067页）；<br/>
- 论文承认科学家并非计算机科学家——"many members of the community are scientists by trade"（第7067页），因此需要"providing computer science and engineering resources"。<br/><br/>
<strong>2. 开发者关注的长期维护性（Developer-Focused Long-Term Maintainability）：</strong><br/>
- "semantic versioning combined with good version control practices"（第7066页）；<br/>
- 测试基础设施的广度——不仅单元测试，还包括集成测试、性能测试、部署测试和验收测试（"integration, performance, deployment, and acceptance testing", 第7066页）；<br/>
- 开发者文档和代码覆盖率（"code coverage to ensure that the code is indeed exhaustively tested", 第7067页）；<br/>
- 人才保留困境——"developers of scientific software are in high demand by industry"（第7067页）；<br/>
- "designing for the dynamic nature of science is hard"（第7066页）——科学软件的长期设计面临学科演进的不确定性。<br/><br/>
<strong>3. 可操作的区分策略：</strong><br/>
- 论文揭示了一个关键洞察——"the performance limitations of Python remain a relevant consideration, even in its use as a glue language"（第7066页），说明用户感知的易用性（Python API）与开发者关注的性能可移植性之间存在张力；<br/>
- 问卷可以设计三组问题：(a) 询问用户对不同集成方式的偏好（API vs GUI vs 文件格式），测量<strong>感知易用性</strong>；(b) 询问用户对版本稳定性、向后兼容性、长期支持的需求，测量<strong>维护性期望</strong>；(c) 询问用户是否曾因软件重构或版本升级导致分析管线中断——这直接触及"开发者担忧的维护性问题"并转化为用户的真实体验；<br/>
- 论文的"stewardship"五要素（第7066页）提供了将两类用户视角统一为可操作维度的理论框架：<strong>稳定性</strong>、<strong>可访问性</strong>、<strong>可复现性</strong>、<strong>可靠性</strong>、<strong>社区参与</strong>。<br/>
<strong>4. 论文的核心建议</strong>（第7067页）："once an idea has been vetted, developers must disseminate the feature in a sustainable manner"——这意味着工具从原型到稳定发布的过渡需要明确的质量门槛，这个门槛也应在问卷中测量用户的期望水平。</p>
<h2>阅读后回填</h2>
<ul>
<li>对问卷题目或选项的影响：论文对"互操作性"的严格定义（"no glue code, data conversions, language barriers, or additional configuration"）可作为问卷中引擎无关工作流概念验证的基准。Python作为胶水语言的定位支持以PySCF为中心的方案。论文对"stewardship"的五要素可作为评估工具可信度的框架。建议在模块B中增设"版本稳定性与向后兼容性"作为独立考量因素。</li>
<li>可转化为访谈追问的内容：(a) 用户是否经历过因软件重构或版本升级导致的工作流中断？具体是哪个软件、什么场景？(b) 用户对"零胶水代码"互操作（论文的理想标准）的现实期望是什么——是否认为这是可实现的理想状态还是不必要的过度设计？(c) 用户如何看待Python作为科学计算胶水语言的性能局限？是否有因此改用C++/Fortran的经验？(d) 开发者社区的流失（如核心开发者离开学术界进入工业界）是否影响用户的软件选择或长期信赖度？</li>
<li>关键证据页码/图表：(a) 可持续发展定义及模块化要求——p.7066；(b) 互操作性严格定义——p.7067；(c) Python作为胶水语言及其性能局限——p.7066；(d) PluginPlay插件框架——p.7065；(e) 中层抽象层需求——p.7064-7065；(f) 人才保留困境——p.7067；(g) 测试分类（集成/性能/部署/验收测试）——p.7066；(h) 10个科学挑战领域概览及Figure 1——p.7057-7058；(i) stewardship五要素——p.7066</li>
<li>仍未解决的问题：(1) 论文设定了"真互操作"的理想标准（zero glue code），但现实情况下用户可接受的折衷程度是多少？——这对项目技术栈选择有直接影响；(2) Exascale计算对可视化数据量的影响（更大系统→更大体积数据）未被论文直接讨论，但以NWChem/PySCF等大规模计算软件为背景，可视化管线必须考虑海量数据处理；(3) 论文未讨论容器化（Docker/Singularity）对可复现性和部署简便性的影响——这是2023年后学术界快速普及的技术。</li>
</ul>
<h2>总结与本项目关联</h2>
<p><strong>核心发现：</strong>Di Felice等（2023）的这篇Perspective由25位来自主要量子化学软件包（NWChem、GAMESS、PySCF、Q-Chem等）的开发者共同撰写，是对计算化学软件可持续发展最全面的顶层论述之一。论文系统论证了可持续软件需要模块化设计、稳定API、严格测试、完善文档和活跃社区的协同作用，而<strong>互操作性</strong>是避免重复造轮、实现生态协同的关键。论文对"互操作性"的严格定义——组件可零胶水代码替换——为本项目的引擎无关工作流提供了理论基准。论文提出了Stewardship概念的五要素（稳定性、可访问性、可复现性、可靠性、社区参与），可作为工具可信度评估的操作化框架。<br/><br/>
<strong>与项目关联：</strong>(1) 论文将Python定位为科学计算胶水语言，直接支持本项目"PySCF → 中间格式 → Blender"的Python中心架构方向；(2) 论文强调数据标准化与可访问性（"software and the data it produces should be standardized and curated"），是本项目选择OpenVDB作为通用中间格式的科学依据；(3) PluginPlay等中层抽象层的思路可类比于本项目的模块化设计——将计算（PySCF）与渲染（Blender）解耦；(4) 论文批评的重复造轮现象（"reinventing the wheel"）说明领域对减少可视化管线重复实现的工作有真实需求；(5) 10个科学挑战领域的多样化计算输出（密度、势场、激发态等）增加了对通用可视化管线的需求。<br/><br/>
<strong>对问卷设计的具体建议：</strong>(1) 在模块B（选择可信度因素）中增设"版本稳定性与向后兼容性"和"Stewardship质量"作为评价维度；(2) 在模块D（引擎无关工作流）中以论文的"互操作性"严格定义作为基准，询问用户对不同层次互操作（零胶水代码、低胶水代码、文件格式互转）的可接受度；(3) 设计三组问题分别测量"感知易用性"（集成方式偏好）和"维护性期望"（版本稳定性需求）；(4) 在用户画像部分增设"是否经历过因软件版本升级导致工作流中断"作为筛选问题；(5) 论文的"stewardship"五要素可作为模块B问题设计的理论框架，转化为Likert量表的具体问项。</p>
