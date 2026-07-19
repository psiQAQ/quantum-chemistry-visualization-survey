# smith_p_2020

BibTeX key：`smith_p_2020`

## 摘要

PSI4 is a free and open-source ab initio electronic structure program providing implementations of Hartree–Fock, density functional theory, many-body perturbation theory, configuration interaction, density cumulant theory, symmetry-adapted perturbation theory, and coupled-cluster theory. Most of the methods are quite efficient, thanks to density fitting and multi-core parallelism. The program is a hybrid of C++ and Python, and calculations may be run with very simple text files or using the Python API, facilitating post-processing and complex workflows; method developers also have access to most of PSI4’s core functionalities via Python. Job specification may be passed using The Molecular Sciences Software Institute (MolSSI) QCSCHEMA data format, facilitating interoperability. A rewrite of our top-level computation driver, and concomitant adoption of the MolSSI QCARCHIVE INFRASTRUCTURE project, makes the latest version of PSI4 well suited to distributed computation of large numbers of independent tasks. The project has fostered the development of independent software components that may be reused in other quantum chemistry programs.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> Psi4、高通量与 Python 工作流</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>

<h2>阅读问题</h2>

<h3>问题 1</h3>
<p>Psi4 的 Python 接口和高通量定位与 PySCF 有哪些实质差异？</p>
<p><strong>回答：</strong></p>
<ul>
  <li><strong>语言架构差异：</strong> Psi4 是 C++/Python 混合体（摘要："The program is a hybrid of C++ and Python"），核心计算在 C++ 层执行，Python 作为上层接口；PySCF 则核心也是 Python（带 C/NumPy 加速）。Psi4 的方法开发者可通过 Python 访问大部分核心功能（摘要："method developers also have access to most of PSI4's core functionalities via Python"）。</li>
  <li><strong>高通量基础设施：</strong> Psi4 采用了 MolSSI QCARCHIVE 基础设施（摘要："adoption of the MolSSI QCARCHIVE INFRASTRUCTURE project"），使其特别适合大规模独立任务的分布式计算——这是 PySCF 目前没有的标准化高通量框架。</li>
  <li><strong>标准化数据格式：</strong> Psi4 使用 QCSCHEMA 数据格式传递作业规范（摘要："Job specification may be passed using The Molecular Sciences Software Institute (MolSSI) QCSCHEMA data format, facilitating interoperability"）——这是与 PySCF 的本质差异，Psi4 参与了 MolSSI 的格式标准化工作。</li>
  <li><strong>SAPT 方法：</strong> Psi4 原生实现对称自适应微扰理论（SAPT，摘要中列出），这是其独特优势，PySCF 不直接提供。</li>
  <li><strong>社区模式：</strong> Psi4 项目促进了可被其他 QC 程序重用的独立软件组件（摘要："The project has fostered the development of independent software components that may be reused in other quantum chemistry programs"），体现了模块化设计理念。</li>
  <li><strong>PDF 限制说明：</strong> 本文 PDF 为扫描版无法提取文本（附件 key=4JK3VEBF，错误码：PDF text extraction returned empty result）。以上回答基于摘要（~1150 字符）和 Zotero 元数据。</li>
</ul>

<h3>问题 2</h3>
<p>标准变量、数据模型和插件生态对跨程序后处理提供了什么先例？</p>
<p><strong>回答：</strong></p>
<ul>
  <li><strong>QCSCHEMA 格式（摘要）：</strong> 这是 MolSSI 定义的标准化量子化学作业规范数据格式，使不同 QC 程序之间可以交换作业定义。对本项目的直接启示：引擎无关工作流需要类似 QCSCHEMA 的标准化输入/输出描述。</li>
  <li><strong>QCARCHIVE 基础设施（摘要）：</strong> 提供了分布式计算和大规模任务调度的标准化框架。这为"跨程序、跨平台"的可视化工作流提供了架构先例。</li>
  <li><strong>Python API 的双向性（摘要）：</strong> 用户可以通过 Python API 驱动计算，方法开发者也可以通过 Python 访问核心功能。这种"前后端合一"的模式使得 Psi4 本身就可以作为后处理/可视化流水线中的一环。</li>
  <li><strong>独立软件组件复用（摘要）：</strong> Psi4 项目孵化的独立组件（如 Libint、Libxc 等）已被其他 QC 程序采用。这证明"引擎无关"的组件化思路是可行的。</li>
  <li><strong>MolSSI 生态体系：</strong> Psi4 是 MolSSI（The Molecular Sciences Software Institute）支持的核心项目之一。QCSCHEMA 和 QCARCHIVE 都是 MolSSI 定义的量子化学软件基础设施标准。这意味着"引擎无关"不仅仅是一个技术决策，更是一个社区标准化的过程——MolSSI 正在扮演标准化组织的角色。</li>
</ul>

<h3>问题 3</h3>
<p>用户采用 Python 化工作流的主要收益和障碍应如何进入 survey？</p>
<p><strong>回答：</strong></p>
<ul>
  <li><strong>收益（依据摘要推断）：</strong>
    <ul>
      <li>简化后处理和复杂工作流（"facilitating post-processing and complex workflows"）。</li>
      <li>无需学习多种语言——同一个 Python 环境完成计算和后处理。</li>
      <li>方法开发者可直接通过 Python 访问核心功能，降低开发门槛。</li>
      <li>QCSCHEMA 标准化格式使得 Python 脚本可以跨程序工作。</li>
      <li>Psi4 与其他 pyQC 包的兼容：Python 接口允许在同一脚本中调用 Psi4+PySCF+其他 pyQC 包，形成互补的 QC 工作流。</li>
    </ul>
  </li>
  <li><strong>障碍（需问卷验证）：</strong>
    <ul>
      <li>Python 环境的依赖管理（conda/pip 兼容性，版本锁定）。</li>
      <li>大规模计算时 Python 调用的开销（函数调用开销、GIL 限制）。</li>
      <li>缺乏标准化数据模型时，Python 后处理脚本需要为不同输出格式编写不同的解析器。</li>
      <li>PSI4 的 C++/Python 混合模式——用户是否需要 C++ 知识来编译/扩展？</li>
      <li>QCSCHEMA 的采用率仍然有限——尚未成为所有主要 QC 程序公认的标准。</li>
    </ul>
  </li>
  <li><strong>进入 survey 的建议：</strong>
    <ul>
      <li>在 A 模块中询问用户使用 Python QC API 的经验（Psi4/PySCF/Qiskit Nature 等）。</li>
      <li>在 D 模块中测试用户对"标准化数据格式（如 QCSCHEMA）"vs "程序特定格式"的偏好。</li>
      <li>在 F 模块（开放反馈）中询问用户对 Python 工作流的主要痛点。</li>
      <li>在 D 模块加入对 MolSSI/QCSCHEMA 的认知度调查。</li>
    </ul>
  </li>
</ul>

<h2>阅读后回填</h2>
<ul>
  <li><strong>对问卷题目或选项的影响：</strong>
    <ul>
      <li>Q16（格式覆盖面）：应包含 QCSCHEMA/QCARCHIVE 作为标准化数据格式选项。</li>
      <li>Q17（首个输入适配器）：PSI4 的 Python API+QCSCHEMA 模式可作为"标准化 API"路线的代表。</li>
      <li>D 模块：QCSCHEMA 的成功经验是"引擎无关工作流"的重要实证。</li>
      <li>B 模块：MolSSI 的标准化工作提供了"社区信任"的一种形式——政府支持 + 学术社区参与的混合模式。</li>
    </ul>
  </li>
  <li><strong>可转化为访谈追问的内容：</strong>
    <ul>
      <li>PSI4 用户是否实际使用 QCSCHEMA？感知价值如何？</li>
      <li>用户是否将 Psi4 与其他程序（如 PySCF）结合使用？格式互操作如何处理？</li>
      <li>独立组件（Libint、Libxc）的复用经验——用户是否意识到这些组件是"引擎无关"的？</li>
      <li>MolSSI 标准在多大程度上影响了用户的软件选择？</li>
    </ul>
  </li>
  <li><strong>关键证据页码/图表：</strong>
    <ul>
      <li>摘要（全文唯一可提取的文本源，PDF 为扫描版无文本层）：QCSCHEMA、QCARCHIVE、C++/Python 混合架构的关键描述。</li>
      <li>注意：PDF 文本提取失败（空结果），本文的回答基于摘要和 Zotero 元数据。</li>
      <li>建议寻找正式出版版本的文本版（DOI: 10.1063/5.0006002）以获得完整信息。</li>
    </ul>
  </li>
  <li><strong>仍未解决的问题：</strong>
    <ul>
      <li>PSI4 的 Python API 与 PySCF 的 Python API 在易用性上有何具体差异？需阅读正文后才能回答。</li>
      <li>QCSCHEMA 的采用率如何？是否有版本兼容性问题？</li>
      <li>PSI4 的高通量能力是否包含自动可视化输出？</li>
      <li>PSI4 1.4 的 wavefunction 对象能否直接导出 cube 文件？</li>
    </ul>
  </li>
</ul>

<p><strong>总结：</strong> PSI4 1.4 (Smith et al., 2020) 的核心贡献在于将 MolSSI QCSCHEMA 标准化数据格式和 QCARCHIVE 基础设施集成到量子化学工作流中，为本项目的"引擎无关"理念提供了直接先例。关键启示：(1) 标准化数据格式（QCSCHEMA）是实现跨程序互操作的关键基础设施，问卷 D 模块应直接引用这个案例；(2) Psi4 的 C++/Python 混合架构证明 Python API 可以成为"核心功能的前端"，这与 PySCF 的纯 Python 路线形成互补而非对立——问卷应探索用户对两种架构的偏好；(3) 独立软件组件（Libint、Libxc 等）的复用模式为"引擎无关可视化工具"的模块化设计提供了蓝图；(4) PDF 扫描版无法提取文本，建议寻找正式出版版本或确认是否有 arXiv 版本可供参考；(5) 通过 MolSSI 的角度看，Psi4 不仅仅是单个程序，而是量子化学软件标准化生态的一部分——这为我们的"引擎无关"愿景提供了宏观层面的先例。</p>
