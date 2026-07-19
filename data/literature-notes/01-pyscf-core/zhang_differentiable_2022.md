# zhang_differentiable_2022

BibTeX key：`zhang_differentiable_2022`

## 摘要

We introduce an extension to the PySCF package, which makes it automatically differentiable. The implementation strategy is discussed, and example applications are presented to demonstrate the automatic differentiation framework for quantum chemistry methodology development. These include orbital optimization, properties, excited-state energies, and derivative couplings, at the mean-field level and beyond, in both molecules and solids. We also discuss some current limitations and directions for future work.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 可微量子化学与交互分析</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>自动微分最可能支持哪些交互式参数扫描、敏感性分析或逆向设计场景？</p>
<p><strong>回答：</strong>论文通过六个应用案例系统展示了自动微分（AD）支持的各类参数敏感性与逆向设计场景：<br/><br/>
1. <strong>轨道优化（逆向设计）</strong>（Sec III A, p.204801-4, Fig 2-3）：论文以随机相位近似（RPA）为例，仅需显式实现能量函数（代码24-31行），轨道梯度和Hessian由AD自动获取（代码33-36行），实现轨道优化OO-RPA。OO-RPA的He2结合能曲线（Fig 3）显示轨优修正了RPA的低估问题，与CCSD(T)结果一致。该模式可直接推广到任意方法的变分轨道优化——典型的逆向设计（通过调整轨道系数最小化能量）。<br/><br/>
2. <strong>响应性质（敏感性分析）</strong>（Sec III B, p.204801-5, Fig 4, Tables I-II）：静态拉曼活性（∂³E/∂R∂ε²）和IR强度通过AD从基态能量自动求导获得。以BH分子CCSD/aug-cc-pVDZ级别，AD计算的谐振频率（2336.70 cm⁻¹）、拉曼活性（217.84 Å⁴/amu）与参考值高度一致（Table I）。H₂O分子的IR强度也与参考值吻合（Table II）。这种"仅需写能量函数"的模式使任意响应性质的计算标准化。<br/><br/>
3. <strong>激发能（参数扫描）</strong>（Sec III C, p.204801-6, Fig 5, Table III）：通过对CC振幅方程求导获得CC Jacobian（式16），再对角化得到激发能。H₂分子EOM-EE-CCSD/6-31G*级的四个最低单重激发态能量与参考值完全一致（Table III）。该方法原则上适用于任何波函数方法。<br/><br/>
4. <strong>导数耦合（非绝热动力学参数）</strong>（Sec III D, p.204801-7, Fig 6, Table IV）：通过对波函数重叠⟨Ψ_I(R₀)|Ψ_J(R)⟩求导获得电子态间的导数耦合。CIS和FCI级别的结果与参考实现完全一致（Table IV）。二阶导数耦合也可通过再次AD获得。Hellmann-Feynman部分可通过仅对哈密顿求导简化计算（式19）。<br/><br/>
5. <strong>固体应力张量（材料性质扫描）</strong>（Sec III E, p.204801-9, Figs 7-8）：Si原胞在HF水平下的应力张量通过AD获得，与有限差分结果平均偏差仅4×10⁻⁸ eV/Å³。这涉及倒格矢和平面波网格对应变变形的响应——但所有复杂性对用户隐藏。<br/><br/>
6. <strong>隐式微分对迭代求解器的加速</strong>（Sec II D, p.204801-3, Fig 1）：论文详细比较了"展开迭代"与"隐式微分"两种AD策略。隐式微分通过对方程（12）求解，将内存占用降低4倍（Sec IV, p.204801-11），且导数精度仅受收敛阈值控制（Fig 1蓝线）。这使AD对SCF和CC迭代求解器的大规模参数扫描变得实用。<br/><br/>
<strong>交互式场景启示：</strong>AD的核心价值在于将"参数→性质"的映射自动化——用户仅需编写正向计算函数，所有导数由框架自动生成。这直接支持：(a) 实时势能面扫描（如沿反应坐标自动计算梯度/曲率）；(b) 基组/赝势优化（如Sec II A提到的基函数指数和收缩系数求导）；(c) 逆向分子设计（如OO-RPA的模式可迁移到其他能量函数的变分优化）；(d) 多性质联合敏感性分析（如同时计算IR+Raman+激发能的核坐标响应）。</p>
<h3>问题 2</h3>
<p>分子与材料工作流中的可视化对象和数据结构有何差异？</p>
<p><strong>回答：</strong>论文明确区分了分子（molecular）与材料/固体（solid/periodic）两种工作流，其可视化和数据结构差异如下：<br/><br/>
<strong>1. 基组与积分模块差异：</strong><br/>
- <strong>分子：</strong>使用 <code>gto</code> 模块处理Gaussian型基函数（式1：收缩Gaussian基函数——中心r₀、指数α_i、收缩系数C_i）。ERIs由Libcint积分库解析求值（Sec II A, p.204801-2）。分子轨道（MO）系数矩阵C作为核心数据结构。<br/>
- <strong>材料/固体：</strong>使用 <code>pbc/gto</code> 模块引入周期性边界条件（PBC）。密度拟合使用 <code>pbc/df</code> 模块（Sec III E, p.204801-9），采用混合高斯/平面波方法（mixed Gaussian and plane wave approach）。平面波基对应均匀网格点（uniform grid points），其响应在应力张量计算中不可忽略。<br/><br/>
<strong>2. 物理量对象差异：</strong><br/>
- <strong>分子：</strong>核坐标R（3N维向量）、电子密度ρ(r)、分子轨道、能量梯度∂E/∂R、偶极矩、拉曼活性张量等——均为有限系统属性，无周期性。<br/>
- <strong>材料/固体：</strong>引入晶格矢量（lattice vectors）和倒格矢（reciprocal lattice vectors）、单位胞体积V、应变张量ε_αβ（式21）。应力张量σ_αβ = (1/V) ∂E/∂ε_αβ（式20）是材料特有的力学量。应变变形同时作用于核坐标和晶格矢量（Sec III E, p.204801-9）。<br/><br/>
<strong>3. 可视化相关数据结构的差异：</strong><br/>
- <strong>分子：</strong>可视化对象通常是有限范围的标量场（如电子密度|ψ(r)|²、静电势）、分子轨道等值面、振动模式矢量——在有限区域内定义。<br/>
- <strong>材料/固体：</strong>需要处理周期性重复单元（unit cell）、布里渊区采样（k-points）、能带结构（band structure）——这些需要支持周期性边界条件的可视化管线。论文采用平面波密度拟合，意味着电子密度等量在倒易空间和实空间之间变换，可视化可能需要处理多重复制。<br/>
- <strong>数据格式差异：</strong>分子数据通常使用.cube或类似格式存储于有限盒子中；周期性系统需要兼容PBC的格式（如VASP的CHGCAR等），处理方式不同。<br/><br/>
<strong>4. 本项目的关联：</strong>论文证明PySCFAD在分子和周期性系统中均无缝工作，意味着若以PySCF作为计算后端，可视化管线必须同时支持两种系统的输出。.cube格式常用于分子；周期性系统可能需要超胞近似或专门的能带可视化方案。本项目设计引擎无关中间格式时需考虑两类系统的数据结构差异。</p>
<h3>问题 3</h3>
<p>使用可微接口需要怎样的编程能力，是否应作为采用障碍或高级用户分层？</p>
<p><strong>回答：</strong>论文提供了判断AD接口编程需求的多层证据：<br/><br/>
<strong>1. 所需编程能力层次：</strong><br/>
- <strong>基础层（脚本使用）：</strong>Python基础 + PySCF API熟悉度。论文提供的代码示例（Figs 2,4-7）均约10-30行Python代码——仅需定义分子对象、设置计算参数、调用能量函数并应用AD求导。如Fig 2的OO-RPA仅26行代码，Fig 4的拉曼活性约20行代码。<br/>
- <strong>进阶层（方法开发）：</strong>需要理解AD模式选择（前向vs反向模式，Sec I, p.204801-1）、隐式微分vs展开迭代的权衡（Sec II D, p.204801-3）、JAX的XLA编译要求（Sec V, p.204801-11）。论文提到"JAX除非通过XLA编译否则比NumPy效率低"（Conclusions第1点）。<br/>
- <strong>专家层（底层扩展）：</strong>需要为C扩展（如ERIs）注册解析导数函数（Sec II A, p.204801-2），处理积分对称性（"incorporating permutation symmetries in electron integrals is tricky"，Conclusions第2点），实现out-of-core数据追踪（Conclusions第3点）。<br/><br/>
<strong>2. 当前性能成本：</strong>（Sec IV, p.204801-10, Fig 9-10）<br/>
- CCSD(T)能量计算：PYSCFAD比PYSCF慢5-8倍（因缺乏积分对称性）。<br/>
- CCSD核梯度：PYSCFAD比解析梯度慢5-8倍，但隐式微分的AD内存占用比展开迭代低4倍。<br/>
- NUMPY后端在大基组下性能差（因einsum单核运行），JAX后端由XLA优化。<br/>
- 这意味着即使熟悉AD，用户仍需面对显著的性能折衷。论文指出"it is potentially possible for PYSCFAD to be just as efficient as PYSCF"（p.204801-10），但目前并非如此。<br/><br/>
<strong>3. 作为采用障碍或分层依据的判断：</strong><br/>
- <strong>不应作为绝对障碍：</strong>论文的核心论点是AD"消除了实现复杂导数的负担"（结论, p.204801-11），"almost no extra effort"即可实现新方法。对于仅需调用已有AD功能的用户，编程门槛很低（10-30行代码）。<br/>
- <strong>应作为高级用户分层：</strong>对于需要自己构建新方法或优化性能的用户，需要JAX/AD的深入理解。论文将PYSCFAD定位为"methodology development platform"和"rapidly prototype new methodologies"（p.204801-11）——明确面向方法开发者，而非普通终端用户。<br/>
- <strong>问卷建议：</strong>在调查中应区分(a) 仅使用已有AD功能的用户（低编程门槛）、(b) 使用AD开发新方法的用户（中等门槛）、(c) 扩展AD框架底层组件的用户（高门槛）。建议在问卷中询问"您使用可微计算的目的"（应用现有功能/开发新方法/扩展框架底层）作为分层变量。</p>
<h2>阅读后回填</h2>
<ul>
<li>对问卷题目或选项的影响：(1) 可在模块A（工作流）中增加"使用自动微分进行参数扫描/优化"作为计算类型选项；(2) 模块B（选择可信度）中可加入"支持自动微分"作为工具筛选条件；(3) 模块D（工作流集成）可询问用户对AD输出格式的需求（如梯度→可视化场的映射）；(4) 论文证明AD对分子和材料同样适用，因此问卷不应区分这两类系统对AD的需求。</li>
<li>可转化为访谈追问的内容：(a) 用户当前是否使用AD进行量子化学计算？使用哪些AD框架（JAX/PyTorch/TensorFlow）？(b) 用户是否遇到AD内存不足或编译时间过长的问题？(c) 用户是否有将AD计算的梯度/曲率信息可视化的需求（如反应路径曲率可视化）？(d) 逆向分子设计场景中，用户希望以何种形式呈现优化路径（动画/静态对比图/参数空间图）？</li>
<li>关键证据页码/图表：(a) OO-RPA实现——Fig 2 (p.204801-4), Fig 3 (p.204801-5)；(b) 拉曼活性实现——Fig 4 (p.204801-6), Table I-II (p.204801-6至204801-7)；(c) CC Jacobian/激发能——Fig 5 (p.204801-7), Table III (p.204801-8)；(d) 导数耦合——Fig 6 (p.204801-8), Table IV (p.204801-9)；(e) 应力张量——Fig 7 (p.204801-9), Fig 8 (p.204801-10)；(f) 计算效率——Fig 9-10 (p.204801-10至204801-11)；(g) 隐式微分内存优势——Sec IV最后一段 (p.204801-11)</li>
<li>仍未解决的问题：(1) 论文未讨论AD输出（梯度、Hessian等）的可视化表示——这些高阶张量如何直观呈现给用户？(2) PDF提到的"编译器级AD工具"（Ref 20）的发展是否已解决C代码的黑箱微分问题？(3) 论文的"仅in-core"限制对于大规模体系（如周期性大体系）意味着什么可视化数据量限制？(4) 论文未分析分子与材料AD工作流在可视化管线中的具体差异。</li>
</ul>
<h2>总结与本项目关联</h2>
<p><strong>核心发现：</strong>PYSCFAD通过JAX后端实现了PySCF的自动微分，使量子化学方法开发者"几乎无需额外努力"即可获得任意阶导数。论文通过6个应用案例（轨道优化、响应性质、激发能、导数耦合、固体应力张量）全面验证了AD在分子和周期性系统中的有效性。关键实现策略包括：用JAX替换NumPy/SciPy、注册C积分函数的解析导数作为黑箱、以及隐式微分迭代求解器以节省内存。当前性能为PySCF的5-8分之一，但具有优化潜力。<br/><br/>
<strong>与项目关联：</strong>(1) PySCFAD证明了PySCF作为本项目计算后端的可行性——其Python原生的API与JAX加速能力适合与可视化引擎集成；(2) AD输出的导数信息（能量梯度、力常数、偶极矩导数等）本身就是可视化对象（如振动模式动画、反应路径曲率可视化），本项目应考虑这些AD特有数据的可视化管线；(3) 论文对"仅需写能量函数"的强调意味着AD框架可大幅降低用户获取导数数据的门槛，从而使基于导数的可视化（如静电势梯度场）不再需要手动解析推导。<br/><br/>
<strong>对问卷设计的具体建议：</strong>(1) 在模块A（计算工作流）中增加"可微/自动微分"作为单独的计算类型选项，并区分"使用现有AD功能"和"开发AD新方法"两个子类；(2) 在模块B（工具选择因素）中增加"支持自动微分"作为量化化学平台选择的技术考量因素；(3) 在模块D（可视化需求）中增加"导数场/响应性质可视化"作为专门的可视化类型；(4) 在用户画像部分记录用户的编程语言偏好（Python/JAX友好度）和AD框架使用经验，作为高级用户分析的维度。</p>
