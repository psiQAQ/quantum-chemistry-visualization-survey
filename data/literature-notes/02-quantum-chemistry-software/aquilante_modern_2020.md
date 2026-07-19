# aquilante_modern_2020

BibTeX key：`aquilante_modern_2020`

## 摘要

MOLCAS/OpenMolcas is an ab initio electronic structure program providing a large set of computational methods from Hartree–Fock and density functional theory to various implementations of multiconfigurational theory. This article provides a comprehensive overview of the main features of the code, specifically reviewing the use of the code in previously reported chemical applications as well as more recent applications including the calculation of magnetic properties from optimized density matrix renormalization group wave functions.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> OpenMolcas 多参考方法与数据接口</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>OpenMolcas 的多参考方法对可视化提出哪些特殊需求？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>活性空间轨道</strong>（Section III.A, p.4）：CASSCF/RASSCF 计算中活性空间轨道的占据数和轨道形状是理解多参考波函数的关键，必须支持活性轨道可视化。</li>
<li><strong>态平均轨道</strong>：SA-CASSCF 产生适合多个态的轨道，可视化需要展示哪些轨道在不同态之间共享。</li>
<li><strong>自旋- orbit 耦合可视化</strong>（Section SINGLE_ANISO, p.15-16）：SINGLE_ANISO 工具自动生成自旋-轨道耦合能级图（"Similar plots are generated automatically by the PLOT key of SINGLE_ANISO"），产生自旋-轨道本征态和跃迁偶极矩的可视化。</li>
<li><strong>密度差和跃迁密度</strong>：CASPT2/NEVPT2 可产生激发态的密度和跃迁密度。</li>
<li><strong>响应性质可视化</strong>：极化率、超极化率等响应性质。</li>
</ul>
<h3>问题 2</h3>
<p>OpenMolcas 的 HDF5/XML/JSON 接口计划对引擎无关工具有何参考价值？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>多重输出格式进展</strong>（Section I, p.2）：OpenMolcas 正在开发通用 HDF5 格式接口和 XML/JSON 格式化输出（"a general HDF5-formatted interface and XML/json formatted output"）——这是本研究项目最直接相关的进展之一。</li>
<li><strong>跨程序接口</strong>（Section I, p.2）：与 QCMAQUIS 和 Columbus 等程序的"customized"数据交换接口已存在。</li>
<li><strong>HDF5 的选择</strong>：HDF5 是一个自描述的、与平台无关的二进制格式，支持大数据集和元数据，非常适合作为三维场数据的存储格式。OpenMolcas 的 HDF5 接口计划说明社区已经认识到标准化格式的重要性。</li>
<li><strong>与 ORCA 6 JSON 接口的对比</strong>：ORCA 6 选择 JSON（纯文本、自描述、广泛工具支持），OpenMolcas 选择 HDF5（二进制、高性能、适合大数据）。两种不同的设计哲学——对本项目的"格式选择"问卷问题有直接参考价值。</li>
</ul>
<h3>问题 3</h3>
<p>Cholesky 分解 (CD) 方法和密度拟合 (DF) 技术对波函数后处理有何影响？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>CD 的核心作用</strong>（Section II, p.2-3）：OpenMolcas 使用 Cholesky 分解 (CD) 实现密度拟合（DF），这是其区别于其他程序的核心技术之一。CD 简化了两电子积分的处理（"significant computational advantages come from employing Eq. (1) due to the fact that the number of auxiliary functions is only about 3-5 times the total size of the atomic basis set"）。</li>
<li><strong>对后处理的影响</strong>：CD 方法减少了需要存储的两电子积分数量，但同时也使得轨道和密度信息的访问路径与其他程序不同。后处理工具需要理解 CD 分解后的积分格式。</li>
<li><strong>基组灵活性</strong>：CD 方法对基组类型不敏感，支持一般收缩基组——这意味着 OpenMolcas 可处理更广泛的基组类型，后处理工具也必须跟上。</li>
</ul>
<h2>阅读后回填</h2>
<ul>
<li>对问卷题目或选项的影响：
  <ul>
  <li>Q16（格式覆盖面）：应包含 HDF5 作为选项；OpenMolcas 的 HDF5/XML/JSON 接口计划验证了社区对标准化格式的需求。</li>
  <li>D 模块（引擎无关）：OpenMolcas 的 HDF5 接口是"引擎无关"概念的具体实践，可在问卷中引用。</li>
  <li>Q17（首个输入适配器）：OpenMolcas 的 HDF5 格式是重要参考。</li>
  </ul>
</li>
<li>可转化为访谈追问的内容：
  <ul>
  <li>OpenMolcas 的 HDF5 接口是否已完成？可用性如何？</li>
  <li>用户是否通过 Cholesky 向量获取积分信息用于后处理？</li>
  <li>SINGLE_ANISO 的 PLOT 输出格式是什么？是否可导出？</li>
  </ul>
</li>
<li>关键证据页码/图表：
  <ul>
  <li>Section I (p.2)：HDF5/XML/JSON 接口计划的描述。</li>
  <li>Section II (p.2-3)：Cholesky 分解和密度拟合。</li>
  <li>Section SINGLE_ANISO (p.15-16)：PLOT 自动生成功能。</li>
  </ul>
</li>
<li>仍未解决的问题：
  <ul>
  <li>OpenMolcas 的 HDF5 格式 schema 是否公开？</li>
  <li>HDF5 接口覆盖哪些数据类型（轨道系数、密度、梯度、Hessian）？</li>
  <li>是否有 Python 库可用于读取 OpenMolcas HDF5 文件？</li>
  </ul>
</li>
</ul>
<p><strong>总结：</strong> OpenMolcas (Aquilante et al., 2020) 是以多参考方法（CASSCF、CASPT2、RASSCF）见长的开源量子化学软件。对本项目的直接启示：(1) OpenMolcas 正在开发通用 HDF5 格式接口和 XML/JSON 输出（Section I, p.2），这是本项目"标准化数据格式"理念的独立验证——一个社区正在从程序特定格式向通用标准过渡；(2) HDF5 vs JSON 的格式选择为问卷 Q16（格式覆盖面）提供了实质性的备选方案：HDF5 适合大容量二进制数据（如三维网格），JSON 适合结构化元数据；(3) Cholesky 分解方法意味着 OpenMolcas 的积分信息以 CD 向量形式存储，与传统的两电子积分存储方式不同——引擎无关工具必须能处理多种积分表示；(4) SINGLE_ANISO 工具的自动 PLOT 输出展示了"计算程序内嵌可视化"的一种模式，这与"引擎无关外部渲染"形成对比。</p>
