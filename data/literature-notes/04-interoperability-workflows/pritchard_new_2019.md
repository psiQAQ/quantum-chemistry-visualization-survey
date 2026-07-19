# pritchard_new_2019

BibTeX key：`pritchard_new_2019`

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 基组资源、版本与互操作</p>
<p><strong>问题生成依据：</strong> 题名与附件可检索内容；Zotero 摘要字段为空</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>Basis Set Exchange 如何处理基组版本、来源、元素覆盖和不同程序格式？</p>
<p><strong>回答：</strong></p>
<p><strong>版本处理：</strong> BSE 采用显式版本号机制，将版本号编码在文件名中（Section 2.1, p.4）。等价于原始 BSE 的数据被固定为 version 0，变更则递增版本号。只有"可能改变计算结果"的修改（如指数或系数的变化）才计入新版本；添加新元素、更新元数据或参考文献<em>不</em>触发版本变更（Section 2.1.2, p.4-5）。此设计保留了旧版本数据的可访问性用于复现，被认为"是旧版 BSE 严重缺失的功能"（p.4）。</p>
<p><strong>来源管理：</strong> component 文件中包含 provenance 元数据（Section 2.1, p.3）；参考文献通过网站可下载为 plain text、JSON 或 BibTeX 格式（Section 2.2.1, p.7）；basis set 名称对应的 URL 包含获取数据所需的所有信息，可共享（Section 2.2.1, p.7）。BSE 还包含 notes 和 family notes 文本文件，记录基组在不同程序中的变体和差异（Section 2.1, p.4）。</p>
<p><strong>元素覆盖：</strong> 数据按元素存储（per-element），component 文件可包含多个元素的数据，element 文件将 component 组合为元素级基组，table 文件将所有元素的 element 数据聚合为完整基组（Section 2.1, p.3-4，Figure 2 以 6-31G* 为例展示层次结构）。网站提供可交互元素周期表，支持"Select All"选择基组覆盖的所有元素（Section 2.2.1, p.6-7）。</p>
<p><strong>程序格式：</strong> 核心 Python 库提供 compose + convert 功能，将组合好的基组转换为特定 QC 程序的输入格式（Section 2.1.1, p.4）。格式支持包括：Gaussian、NWChem、GAMESS US、Molpro、Turbomole、CFour、Dalton、Psi4、Molcas、GAMESS UK 等（Table 2, p.9）。新格式随 Python 库自动添加至网站（Section 2.2.1, p.7）。连续集成定期将新 BSE 输出与旧 BSE 下载数据对比以确保格式转换正确（Section 2.1.2, p.5）。</p>
<h3>问题 2</h3>
<p>用户更需要工具自动获取/转换基组，还是只需保存可追溯标识？</p>
<p><strong>回答：</strong></p>
<p>论文的三层架构设计表明两类需求<em>同时存在</em>且各有用户群体：</p>
<ul>
<li><strong>自动获取/转换（power user / 开发者）</strong>：BSE Python 库可嵌入到 QC 程序中提供运行时基组获取与格式转换，"negating the need to manage their own library of basis set data"（Section 2.1, p.3）。CLI 允许非 Python 工作流集成（Section 2.1.3, p.5）。REST API 提供统一的 HTTP 访问（Section 2.2.2, p.8, Table 1）。"Optimized General Contractions"、uncontract、recontract 等后处理功能也含在库内（Section 2.1.1, p.4）。</li>
<li><strong>可追溯标识（普通研究者）</strong>：网站保持原有的"URL 即可重现"的共享方式（Section 2.2.1, p.7），用户可通过浏览器快速筛选和下载单次计算所需的基组（Section 2, p.2 "the website is convenient for many researchers to browse and download basis sets for one-off calculations"）。</li>
</ul>
<p>关键推论：对本项目而言，可追溯标识是互操作的基础（用户需要知道用了哪个基组的哪个版本），而自动转换是工程化的前提。Survey 应将二者分开测量：用户是在乎"能在工作流中自动获取"还是"保存了版本标识即可手动获取"。</p>
<h3>问题 3</h3>
<p>离线、API 可用性和程序格式差异是否应成为采用或部署问题？</p>
<p><strong>回答：</strong></p>
<p>从论文看，这些<strong>确实</strong>是部署和采用的关键考量：</p>
<ul>
<li><strong>离线可用性</strong>：BSE 将核心数据封装为可独立安装的 Python 库（"Users are able to install the library through typical Python distribution channels", Section 2.1, p.3），这本身就解决了很多 HPC 环境无法访问外部网站的问题。</li>
<li><strong>API 可用性</strong>：新增 REST API（"read-only access to the BSE data through RESTful APIs which can be used from any programming language", Section 2.2.2, p.8）是对旧版 BSE（"did not have...an easy, modern callable interface" Section 1, p.2）的主要改进之一。旧版 WSDL 接口"ultimately the services were not exposed for external access"（Section 1, p.2）导致无法被程序自动化调用。</li>
<li><strong>程序格式差异</strong>：BSE 通过测试确保格式正确性——将旧 BSE 的下载数据作为基准，"output from the new BSE is regularly compared against this previous output using continuous integration"（Section 2.1.2, p.5）。数值数据存储为字符串以"allow for exact matching with published data"（Section 2.1, p.3）。</li>
</ul>
<p>对本项目的启示：格式互操作层必须在设计时就考虑离线部署场景（尤其是含 PySCF 的 HPC/容器环境），并通过测试套件持续验证格式转换的正确性和版本兼容性。</p>
<h2>阅读后回填</h2>
<ul>
<li><strong>对问卷题目或选项的影响：</strong> 模块 C（三维场文件格式与现有工作流）应增加关于"基组版本追踪"的子问题；模块 B（选择可信度）应涵盖"程序间基组实现差异是否影响可视化一致性"的问题。</li>
<li><strong>可转化为访谈追问的内容：</strong> "你在使用不同 QC 程序时是否遇到过因基组实现不同导致的计算结果差异？能否举例？" "你是否希望工作流工具自动完成基组下载和格式转换？"</li>
<li><strong>关键证据页码/图表：</strong> Section 2.1 p.3-4（层次化文件结构 + 版本管理策略）；Table 2 p.8-9（格式使用统计：Gaussian 占 56.55%，NWChem 16.79%，GAMESS US 10.69%——反映格式支持优先级）；Section 2.1.2 p.5（连续集成验证格式转换正确性）。</li>
<li><strong>仍未解决的问题：</strong> 论文未讨论基组在网格/场（cube 文件）上下文中如何保持版本一致性；BSE 的格式转换虽然经过测试但论文未说明如何处理各程序输入语法中的歧义或不兼容。</li>
</ul>
<h2>总结</h2>
<p>BSE 的成功验证了"中心化、版本可控、多格式输出"的资源管理模式在 QC 社区的有效性（约 10,000 月活用户/45,000 月交互）。对本项目而言，BSE 的三层架构（数据层/API 层/展示层）是一个值得参考的模式。特别值得借鉴的是：(1) 将数值存储为字符串以确保精度可追溯；(2) 用连续集成持续验证格式转换的正确性；(3) 同时服务于"点击获取"的普通用户和"API 集成"的开发者。BSE 用户对格式的需求分布（Gaussian > 50%）也提示我们的 .cube → OpenVDB 工作流应优先支持主流 QC 程序的输出格式。</p>
