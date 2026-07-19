# gagliardi_sustainable_2023

BibTeX key：`gagliardi_sustainable_2023`

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 软件普及、社区与培训</p>
<p><strong>问题生成依据：</strong> 题名与附件可检索内容；Zotero 摘要字段为空</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>"everyone, everywhere"具体指哪些非专家用户和使用环境？</p>
<p><strong>回答：</strong>本文是JCTC特刊"Electronic Structure Theory Packages of Today and Tomorrow"的编辑前言（第6857页），"everyone, everywhere"具体指：<br/><br/>
1. <strong>非专家用户</strong>（第6857页）："scientists with limited quantum chemistry training now routinely harness the power of these calculations to drive their investigations"——即受过有限量子化学训练但依赖计算工具推动研究的科学家；<br/>
2. <strong>全球协作环境</strong>（第6857页）："Many of these software packages began as internal projects led by pioneering individuals and have since transformed into worldwide endeavors, thanks in part to advancements in research collaboration tools and data sharing"——软件从个人项目发展为全球协作项目，适应分布式研究环境；<br/>
3. <strong>社区驱动的基础设施</strong>（第6857页）："they have grown into community-driven simulation infrastructures that welcome contributions from researchers worldwide"——非专家用户也成为贡献者；<br/>
4. <strong>特定使用环境例子</strong>（第6857-6858页）：量子化学软件在凝聚态模拟（Crystal、Quantum ESPRESSO、Koopmans）、分子体系模拟（OpenMolcas、NWChem、TURBOMOLE、GAMESS）等不同领域的应用扩散，说明"everywhere"覆盖从分子到材料、从CPU到GPU的多样化计算环境。</p>
<h3>问题 2</h3>
<p>减少新编程技能要求、提高互操作性和建设社区三者中，哪项最影响采用？</p>
<p><strong>回答：</strong><br/><br/>
论文没有为这三者排出明确优先级，但提供了以下结构证据：<br/><br/>
1. <strong>减少编程技能要求被列为开发者核心承诺</strong>（第6857页）："Another noteworthy aspect that emerges in several of these contributions is the commitment of the developers to reduce the need for researchers to acquire new programming languages or skills. Instead, they aim to develop interoperable software, fostering greater accessibility and inclusivity in electronic software development."——这里"Instead"句型表明<strong>减少新编程技能要求</strong>是目标，<strong>互操作性</strong>是实现手段，<strong>社区建设</strong>是结果。<br/><br/>
2. <strong>社区的可持续性是未来的关键挑战</strong>（第6857页）："A significant challenge for the community is ensuring that the next generation of researchers is equipped to sustain these valuable resources and continue advancing the field."——论文将下一代培训视为最大的长期挑战，这暗示<strong>社区建设</strong>是可持续性的最终保障。<br/><br/>
3. <strong>从问卷设计角度的建议</strong>：这三者形成递进关系——降低技能门槛提高<strong>初始采用</strong>，互操作性提高<strong>持续使用</strong>，社区建设决定<strong>长期存活</strong>。问卷应分别测量用户对这三个维度的感知重要性和满意度。<br/><br/>
4. <strong>对本项目的启示</strong>：本项目降低3D可视化编程门槛（用户不需要学习PySCF内部渲染代码和Blender Python API的全部），通过引擎无关中间格式实现互操作性，这两点直接对应了本编辑前言中强调的开发者承诺。</p>
<h3>问题 3</h3>
<p>问卷需要怎样的问题来测量软件持续维护与下一代培训的信任价值？</p>
<p><strong>回答：</strong><br/><br/>
本文篇幅较短（2页），但在以下方面提供了问卷设计依据：<br/><br/>
1. <strong>软件寿命作为信任信号</strong>（第6857页）："Many of these software packages began as internal projects led by pioneering individuals in the late 1980s and have since transformed into worldwide endeavors"——软件的持续发展历史（如Crystal始于1988年、MOLCAS始于1989年）本身就是信任信号。问卷可以问："软件包的开发历史和持续时间对您选择工具的决策有多大影响？"<br/><br/>
2. <strong>开源/商业的共同社区特征</strong>（第6857页）："Whether open source or commercial, these software products share a common feature: they have grown into community-driven simulation infrastructures that welcome contributions from researchers worldwide."——社区驱动而非许可证类型是信任的核心。问卷应区分"开源"和"社区活跃"两个不同维度。<br/><br/>
3. <strong>下一代培训的具体体现</strong>（第6857-6858页）：本文介绍的各软件包中，多个提及了降低GPU编程门槛（Quantum ESPRESSO的GPU移植）、提供基础教程、以及推动开放数据共享。问卷可问："您认为软件项目提供培训资源（教程、工作坊、在线课程）对您选择该软件有多重要？"<br/><br/>
4. <strong>从社区规模推断信任</strong>：论文描述了软件从个人项目到全球社区的发展轨迹。问卷可设计行为问题："您是否更倾向于使用有活跃用户论坛/邮件列表/贡献者社区的软件？"<br/><br/>
5. <strong>转移风险认知</strong>：如果"next generation of researchers"未能接管维护，用户面临迁移成本。问卷可问："您是否担心您当前使用的量子化学软件因开发者退休或缺乏维护而无法在未来继续使用？"</p>
<h2>阅读后回填</h2>
<ul>
<li>对问卷题目或选项的影响：论文提出"减少编程技能要求→互操作性→社区"的递进关系链，可作为问卷模块D（引擎无关工作流）的理论假设。论文明确将"降低新编程技能门槛"列为开发者核心承诺，为问卷中拖放式/零代码工作流选项提供了权威参考。</li>
<li>可转化为访谈追问的内容：a) 用户是否因编程语言门槛而放弃使用某些量子化学软件？b) 用户是否参与过量子化学软件的社区贡献（文档、测试、代码）？c) 用户如何看待从专家工具到社区基础设施的转型？</li>
<li>关键证据页码/图表："scientists with limited quantum chemistry training"定义（p.6857）、三要素关系链（p.6857）、社区驱动特征（p.6857）、下一代培训挑战（p.6857）。</li>
<li>仍未解决的问题：论文未讨论降低编程技能要求与保留高级用户可定制性之间的平衡。"Everyone, everywhere"的概念涵盖了不同背景的用户，但未涉及这些用户对可视化等特定功能的需求差异。</li>
</ul>

<h2>总结</h2>
<p>Gagliardi（2023）作为JCTC主编为"Electronic Structure Theory Packages of Today and Tomorrow"特刊撰写的编辑前言，虽仅2页，但清晰地勾勒了计算化学软件发展的三个核心主题：(1)服务于"受过有限量子化学训练的科学家"这一日益扩大的用户群体；(2)开发者通过降低编程技能门槛和增强互操作性来提高可及性；(3)下一代研究者的培训是软件可持续性的最大挑战。论文确认了社区驱动模式——而非许可证类型（开源vs商业）——是软件长期成功的关键。对本项目而言：引擎无关工作流本质上是在3D可视化领域"降低新技能要求"的具体实践；问卷可将本文提出的"减少编程需求→互操作性→社区"链作为假设框架，分别测量用户对这三环节的感知和需求强度。</p>
