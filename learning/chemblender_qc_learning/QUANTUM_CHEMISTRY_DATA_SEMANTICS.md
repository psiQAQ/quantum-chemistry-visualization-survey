# 量子化学数据语义与 ChemBlender

## 面向科学可视化开发的入门教材

**版本**：2026-09-20  
**路线**：数据语义优先、量化核心格式  
**适用对象**：有物理/光学背景、具备基础 Python 能力、需要理解 ChemBlender 科学数据链的学习者

> 本教材只覆盖首轮要求：计算架构、实际使用的量子化学近似、核心输入输出格式、物理量语义、三维网格和最小验证。
> 不要求完整推导，也不要求编写量子化学求解器。

## 学习目标

读完并经过练习后，应能独立回答：

1. 一个文件保存的是几何、计算设置、轨道、密度、势，还是已经采样好的三维场？
2. 一个数组中的每个数代表什么物理量，单位是什么，坐标在哪里？
3. ChemBlender 为什么把源文件、标准化数据、科学数组和 Blender 显示对象分开？
4. 画面出现后，哪些科学结论仍然没有被验证？

所有判断都要区分：

- **已确认**：由当前文件、源码、manifest、明确字段或可复现计算直接支持。
- **推断**：根据命名、数值形状或常见约定得到的暂时判断。
- **未知**：当前资料不足以判断，不能用“看起来合理”填补。

第一次出现的专业词会立即解释。英文名称保留，是为了能和代码、文档和输出文件对应。

---

# 第一章：完整数据链

## 1. 量子化学可视化不是把文件直接画出来

**量子化学**（quantum chemistry）是用量子力学描述分子或材料中电子与原子核行为的计算领域。**电子结构**（electronic structure）特指电子状态、能量、轨道、密度等信息。

ChemBlender 面对的是一条数据链：

~~~text
几何与计算设置
        ↓
量子化学程序：在选定近似和数值条件下求解
        ↓
结果文件：轨道、密度矩阵、能量、网格或其他属性
        ↓
Reader / parser：读取器和解析器
        ↓
标准化科学模型：Structure、BasisSet、OrbitalSet、DensityMatrix
        ↓
数值后端：在空间点上评价轨道、密度或静电势
        ↓
Grid3D：带坐标、单位和物理语义的三维数组
        ↓
CBQ sidecar：科学项目边车及 .npy 数组
        ↓
Blender View：体、面、切片、剖面和表面着色
~~~

四种动作不能混淆：

| 动作 | 它做什么 | 它不证明什么 |
|---|---|---|
| 计算 | 根据模型和设置得到电子结构或其他结果 | 不保证每个字段被正确读取 |
| 解析 | 把文件内容变成结构化数据 | 不自动知道含义不明确的数值是什么 |
| 采样 | 在空间点上评价一个函数 | 不自动保证单位、轴序和来源正确 |
| 渲染 | 把数组转成几何、颜色或体积图像 | 不证明物理定义和数值误差正确 |

**核心原则**：容器格式、物理语义和显示方式是三个不同层次。

Cube 是三维场容器，可以装电子密度、轨道、静电势或其他标量场。扩展名本身不是物理量定义。

## 2. ChemBlender 的当前边界

当前工作区把科学计算和 Blender 展示分开：

- chemblender_prepare 负责读取、派生、验证和导出科学数据。
- cbq_core 保存标准化模型、数组、来源和项目边车。
- Blender 侧消费准备好的科学实体和可重建显示对象。
- 数值后端不应反向依赖 Blender 的 bpy API。

**边车**（sidecar）是与主工程相邻、保存结构化科学数据的附属项目。这里，.blend 负责 Blender 工程，.cbq 目录保存可恢复科学实体和数组。

开发者优先问：

1. 物理数据在什么对象中？
2. 单位和语义保存在哪里？
3. 它从哪个源文件、计算或派生操作产生？
4. Blender 关闭后，是否能从 CBQ 重新建立显示？

---

# 第二章：薛定谔方程与最小理论

## 3. 薛定谔方程

**薛定谔方程**（Schrödinger equation）是描述量子态的基本方程。定态问题常写成：

~~~text
H Ψ = E Ψ
~~~

- H 是**哈密顿算符**（Hamiltonian），表示给定模型下的能量运算规则。
- Ψ 是**波函数**（wavefunction），描述量子态的数学对象。
- E 是该状态的能量。

本轮不推导方程，只需要记住：任何结果都是某个哈密顿模型、某些条件和某种近似下的结果。

### 3.1 实际使用的条件

| 条件 | 含义 | 对数据的影响 |
|---|---|---|
| 非相对论近似 | 暂不把相对论效应作为主模型 | 适合首轮分子轨道和密度示例 |
| Born–Oppenheimer 近似 | 将电子运动和原子核运动分开；计算电子时固定核位置 | 坐标、核电荷和电子态成为重要输入 |
| 固定几何 | 一次电子结构计算中原子位置通常不随电子求解同时变化 | 同一几何才适合直接比较两个电子场 |
| 电子态条件 | 总电荷、电子数和自旋多重度被固定 | 决定占据数、alpha/beta 通道和密度定义 |
| 基组有限展开 | 用有限数量的基函数表示轨道 | 轨道和密度是近似数值 |
| 边界条件 | 例如孤立分子、周期晶体或有限网格盒 | 影响势零点、网格范围和可比较性 |

**Born–Oppenheimer 近似**（玻恩–奥本海默近似）不是忽略原子核，而是在电子问题中先固定核位置；核仍通过核电荷和坐标参与哈密顿量。

### 3.2 从方程到文件

实际流程通常是：

1. 给定原子核坐标、核电荷、总电荷和电子态。
2. 选择可计算的近似模型。
3. 用有限基函数表示轨道或密度。
4. 通过数值迭代获得能量、轨道、密度或派生量。
5. 将结果写成文件或在有限空间网格上采样。

文件中常见的是原子坐标、基函数及其系数、轨道系数、占据数、密度矩阵和有限网格值。这些表示彼此相关，但不能无条件互相替代。

## 4. Hartree–Fock

**Hartree–Fock**（HF）是一种平均场模型。**平均场**（mean field）表示：每个电子不逐一跟踪其他电子的瞬时位置，而是在其他电子形成的平均作用中运动。

HF 用单电子轨道构造多电子近似态。多个轨道组合成 **Slater 行列式**（Slater determinant），一种满足电子交换反对称性的多电子表示。

HF 的要点：

1. 轨道决定电子分布，电子分布又决定平均场。
2. 电子交换效应由反对称性体现。
3. 普通 HF 不完整描述电子相关，即电子运动超出平均场的协同变化。

常见输入：原子坐标、总电荷、自旋多重度、基组、restricted 或 unrestricted 形式、初始猜测和收敛阈值。

常见输出：总能量、SCF 收敛信息、轨道能量、轨道系数、占据数、密度矩阵以及由这些对象求得的电子密度和静电势。

## 5. Density Functional Theory

**Density Functional Theory**（DFT，密度泛函理论）把电子数密度作为主要变量。**泛函**（functional）是把一个函数映射到数值的规则；DFT 中的交换-相关泛函近似电子交换与相关能量。

很多 DFT 程序使用 **Kohn–Sham 轨道**作为计算辅助表示。它们便于构造密度和求解方程，但不能简单宣称每条轨道都是实验可直接观测量。

DFT 除几何、电子态和基组外，还必须说明泛函、积分网格、收敛标准以及可能的色散修正、溶剂模型或约束。同一几何和基组换一个泛函，也会得到不同密度。方法名称是数据语义的一部分。

## 6. SCF

**Self-Consistent Field**（SCF，自洽场）是许多 HF 和 DFT 计算使用的迭代求解框架，不是独立物理理论。

~~~text
1. 建立初始密度或轨道
2. 用当前密度构造有效算符
3. 求解轨道和轨道能量
4. 根据占据数更新密度
5. 比较能量、密度或残差
6. 未收敛则继续，收敛则保存结果
~~~

**收敛**（convergence）表示变化量小于预设阈值，不等于结果绝对精确。

应记录是否收敛、阈值、迭代次数、最终能量、初始猜测以及方法、基组、电荷和多重度。

| 名称 | 它是什么 | 数据链作用 |
|---|---|---|
| 薛定谔方程 | 基础量子力学方程 | 规定量子问题 |
| HF | 平均场电子结构模型 | 规定多电子近似 |
| DFT | 以密度为核心的模型家族 | 规定能量和密度近似 |
| SCF | 迭代求解框架 | 让轨道和密度达到自洽 |

**后-SCF**（post-SCF）表示 SCF 之后的相关方法或分析。SCF 密度和后-SCF 密度不能仅因形状相同就当成同一个对象。

---

# 第三章：AO、MO、密度和静电势

## 7. 结构、电子态和基组

结构通常包含原子序数、坐标、坐标单位以及可选的键、晶胞、拓扑或层级信息。

只有结构坐标通常不能推出电子数、轨道、密度矩阵、计算方法、基组或 SCF 收敛状态。相同 XYZ 坐标可以用不同电荷、基组和方法得到不同电子场。

**总电荷**决定电子数相对于核电荷的偏离。**自旋多重度**（spin multiplicity）通常写成：

~~~text
2S + 1
~~~

单重态多重度为 1，双重态为 2。电荷和多重度会影响电子数、alpha/beta 电子数、轨道占据和开放壳层表示。

**基组**（basis set）是用来展开轨道的一组有限函数。当前波函数链重点使用高斯型基函数。文件中常见原始函数、收缩函数、壳层、角动量、Cartesian 或 pure/spherical 约定、指数和收缩系数。

基组决定“用多少、什么形状的函数表示轨道”，不等于新的物理量。

## 8. AO 和 MO

**原子轨道**（AO，atomic orbital）是基于原子中心的局部函数。**分子轨道**（MO，molecular orbital）是 AO 的线性组合：

~~~text
φᵢ(r) = Σμ Cμᵢ χμ(r)
~~~

φᵢ是第 i 个 MO 的空间振幅，Cμᵢ是系数，χμ是 AO 基函数。MO 系数通常无量纲；轨道空间振幅常见单位为 bohr^(-3/2)。

实数 MO 的正负值通常表示波函数相位，不表示正负电荷。整条轨道乘以 -1 后，平方模不变，但正负颜色会交换。因此 MO 等值面常用发散色图，颜色交换不一定代表错误。

当前轨道集有三种形式：

| OrbitalKind | 通道 | 含义 |
|---|---|---|
| restricted | restricted | alpha/beta 通常共享空间轨道，适合闭壳层 |
| unrestricted | alpha、beta | 两个自旋通道可有不同空间轨道，适合开放壳层 |
| generalized | generalized | 更一般的自旋轨道，不能强行压成两通道 |

**闭壳层**指电子通常成对占据相同空间轨道；**开放壳层**指存在未成对电子或 alpha/beta 占据不同。

## 9. 密度矩阵

AO 基函数通常不是正交的。**重叠矩阵**（overlap matrix）为：

~~~text
Sμν = ∫ χμ(r) χν(r) d³r
~~~

对实数轨道和适当占据数，密度矩阵可简化写成：

~~~text
P = C diag(nᵢ) Cᵀ
N = Tr(P S)
~~~

Tr 是矩阵迹，P 是密度矩阵，S 是 AO 重叠矩阵。不能在非正交 AO 基中用 Tr(P) 代替 Tr(P S)。

对于 unrestricted 表示：

~~~text
Pα：alpha 通道
Pβ：beta 通道
Ptotal = Pα + Pβ
Pspin  = Pα - Pβ
~~~

总电子密度通常非负；自旋密度是两个通道的差，可以为正或负。自旋密度的负值不是负概率。

当前 DensityMatrix 明确记录 level：scf 或 post_scf；spin_role：total 或 spin；方阵维度；无量纲单位；对应结构、基组和来源。

## 10. 物理量对照

| 量 | 表示什么 | 常见单位 | 是否可正可负 | 典型显示 |
|---|---|---|---:|---|
| MO φ | 单电子轨道振幅 | bohr^(-3/2) | 可以 | 正负相位等值面 |
| 单轨道平方模 | 单轨道空间概率密度 | bohr^(-3) | 不可以 | 单轨道密度 |
| 总电子密度 ρ | 电子数空间分布 | electron/bohr³ | 通常不可以 | 体积或非负等值面 |
| 自旋密度 | alpha 与 beta 密度之差 | electron/bohr³ | 可以 | 正负等值面 |
| 差分密度 Δρ | 两个状态的密度之差 | electron/bohr³ | 可以 | 正负重排面 |
| 静电势 V | 单位正试探电荷的静电势能 | hartree/e | 可以 | 密度表面颜色 |
| ELF、LOL、RDG | 特定定义的派生指标 | 常见为无量纲 | 取决于定义 | 专用显示 |

**电子云**是视觉或教学俗称，不是足以替代 semantic_role 和 unit 的科学字段。

## 11. 静电势和差分量

对孤立、全电子、非周期模型，静电势可粗略理解为：

~~~text
V(r) = ΣA ZA / |r - RA|
       - ∫ ρ(r') / |r - r'| d³r'
~~~

ECP/赝势（effective core potential，有效芯势）、周期边界、势零点和核附近奇点需要额外条件。静电势不是电子密度，也不是原子电荷着色。

差分密度必须记录差分方向、两种几何、电荷和电子数、方法、基组、密度层级以及是否使用同一坐标和网格。不能为了让积分看起来合理而随意重新归一化。

---

# 第四章：核心文件格式

## 12. 文件能装什么，不等于项目读取什么

| 问题 | 需要记录 |
|---|---|
| 文件身份 | 文件名、格式、来源、版本、SHA-256 |
| 结构 | 原子数、元素、坐标、单位、拓扑或晶胞 |
| 计算条件 | 方法、基组、电荷、多重度、程序和版本 |
| 科学对象 | 轨道、密度矩阵、三维场、能量或其他结果 |
| 空间数据 | 原点、步向量、形状、轴序、值单位 |
| 保留边界 | 哪些字段被解析，哪些只保留在原始 envelope |

**Envelope**（信封）是保留原始 JSON 或附加字段的容器。它能保留信息，但不能自动证明所有字段已经成为可计算科学实体。

## 13. XYZ、SMILES、SDF

XYZ 通常保存原子数、元素符号、笛卡尔坐标和 comment，不保证保存键拓扑、电荷、多重度、基组、方法、电子密度或轨道。

SMILES（简化分子线性输入规范）主要用字符串表达分子图、键、形式电荷、同位素和部分立体化学信息，通常没有真实三维坐标。

SDF（Structure Data File）可以保存一个或多个分子记录、原子、键、坐标和属性字段。属性字段可能是用户自定义文本，不能只看字段名猜物理含义。

## 14. Gaussian 和 ORCA 输入

Gaussian 和 ORCA 是常见量子化学程序。输入文件通常包含任务类型、方法和基组、总电荷、多重度、原子坐标、收敛和积分设置。

当前普通 Reader 重点读取明确的 Cartesian 几何和部分设置，不等于在 Blender 中执行量子化学计算。

输入文件可以说明方法、基组、电荷、多重度和坐标，但不能单独说明计算一定成功、SCF 一定收敛或轨道网格已经存在。

## 15. FCHK

FCHK（Gaussian formatted checkpoint）是常见格式化检查点文本文件，可能保存标题、方法和基组线索、原子数、核电荷、坐标、电荷、电子数、alpha/beta 电子数、基函数、壳层、轨道能量、占据数、轨道系数和密度矩阵。

具体字段取决于生成程序和导出方式，不能只凭扩展名保证对象齐全。

当前 H₂O fixture 的文件头包含：

~~~text
SP        RHF                                                         STO-3G
Number of atoms                            I                3
Charge                                     I                0
Multiplicity                               I                1
Number of electrons                        I               10
Number of alpha electrons                  I                5
Number of beta electrons                   I                5
Number of basis functions                  I                7
SCF Energy                                 R     -7.495929232844363E+01
~~~

直接解释为 RHF/STO-3G、3 个原子、中性单重态、10 个电子、5 alpha、5 beta 和 7 个基函数。这支持文件身份和计算条件判断，但不等于已经验证三维密度。

## 16. Molden

Molden 是常见波函数和轨道交换格式，可能包含原子、坐标、基组壳层、轨道系数、轨道能量和占据数。

不同程序导出的 Molden 文件可能在原子单位、角动量排列、纯球谐/笛卡尔约定、轨道顺序和方法元数据上不同。没有方法声明时，不要从轨道图猜测 HF、DFT 或泛函。

## 17. QCSchema

QCSchema 是量子化学软件交换计算输入输出的 JSON 数据结构约定。JSON 是结构化文本表示。

它可能包含 molecule、model、driver、properties、return_result、程序版本、原始返回值和 provenance。普通计算结果 JSON 不一定包含完整波函数、基组系数或三维场。

## 18. Cube

Cube 通常包含原子数和坐标、网格原点、三个网格轴的数量和步向量、标量值以及可选多个 dataset。

Cube 的物理含义可能依赖来源说明、生成命令或外部上下文。读取时至少记录 dataset_index、原点、步向量、shape、坐标单位、值单位、semantic role、dataset 数量、来源和哈希。

## 19. CBQ 和 .npy

CBQ 是 ChemBlender 保存科学实体的项目边车，通常由 manifest.json、实体元数据、arrays 目录下的 NumPy .npy 数组、来源、revision 和可重建 View 信息组成。

单独的 .npy 通常不能告诉你物理量、单位、原点、步向量、计算来源或总/自旋/差分身份。因此必须和 CBQ manifest、实体元数据一起读取。

---

# 第五章：Grid3D 与三维数值空间

## 20. Grid3D 的四个层次

Grid3D 不是一个三维数组加一个名字，而是：

1. 数值数组：值、维度、shape、dtype、值单位。
2. 空间坐标：origin、三条 step vectors、坐标单位。
3. 物理语义：semantic_role、dataset、状态。
4. 来源链：源对象、操作、参数、后端和 hash。

## 21. 仿射网格

对索引 i、j、k，空间点为：

~~~text
r(i,j,k) = origin + i·s₀ + j·s₁ + k·s₂
~~~

origin 是网格起点，s₀、s₁、s₂ 是三个步向量，shape 是每轴采样数。

**仿射网格**（affine grid）允许网格轴不与笛卡尔 x/y/z 平行。当前模型要求 origin 有三个有限数、三条步向量线性独立、数据最后三维为 x/y/z、坐标单位有长度量纲、空间维度为正。

正交网格示例：

~~~text
(0.25, 0,    0)
(0,    0.25, 0)
(0,    0,    0.25)
~~~

非正交网格可能是：

~~~text
(0.50, 0.00, 0.00)
(0.10, 0.48, 0.00)
(0.05, 0.08, 0.52)
~~~

只保存三个步长数字会丢失方向信息。

## 22. shape、范围和分辨率

一维有 N 个点、步长为 h 时，相邻点距离是 h，第一个点到最后一个点的范围是 (N - 1)h。包围盒体积还要结合三条步向量的行列式。

增加切片显示中的插值样本，不等于增加原始三维计算的科学分辨率。

## 23. 网格积分

每个网格平行六面体体积：

~~~text
ΔV = |det(s₀, s₁, s₂)|
~~~

简单矩形求和：

~~~text
∫ f(r)d³r ≈ ΔV Σᵢⱼₖ f(i,j,k)
~~~

电子密度积分应接近电子数；自旋密度目标取决于 alpha/beta 定义；MO 平方模积分可用于归一化检查。误差还受网格范围、步长、核附近解析、截断、插值和源精度影响。

## 24. 多 dataset 和轴序

数据维度可能是 x、y、z，也可能是 dataset、x、y、z。有 dataset 轴时必须明确 dataset_index，不能静默取第一层。

两个数组 shape 相同不代表空间对齐。配对两个场至少核对 origin、step vectors、shape、coordinate unit、dataset、structure identity、采样和插值规则。

## 25. 当前语义预设

| semantic role | 值单位 | 是否允许正负 | 常见显示 |
|---|---|---:|---|
| molecular_orbital | inverse_bohr_to_three_halves | 是 | 正负相位等值面 |
| electron_density | electron_per_cubic_bohr 或 electron_per_cubic_angstrom | 否 | 体积或非负等值面 |
| spin_density | 电子数密度单位 | 是 | 正负等值面 |
| electrostatic_potential | hartree_per_elementary_charge | 是 | 发散色图或表面属性 |
| difference_density | 电子数密度单位 | 是 | 正负重排等值面 |
| scalar_field | 由合同决定 | 未知 | 必须先解决语义 |

这张表是显示合同，不是根据数值符号猜测语义的规则。

## 26. 体、面、切片和表面属性

**体渲染**（volume rendering）把三维数组映射到体积外观，透明度和光照会影响视觉印象。

**等值面**（isosurface）是满足 f(r) = c 的空间曲面。MO 常用正负面表达相位；电子密度常用非负阈值；自旋密度和差分密度通常保留正负面。

切片是在平面上插值得到二维数据；剖面是在直线上插值得到一维数据。它们不会自动增加原始场的信息。

field-on-surface（表面上的场）表示用一个场决定表面几何，再在表面顶点采样另一个场并着色。典型例子是“电子密度决定表面，ESP 决定颜色”。两个 dataset 必须分别保存来源和单位。

## 27. 显示变换和科学变换

| 操作 | 只作用于显示时 | 替换权威数组时 |
|---|---|---|
| 调整颜色范围 | 通常是显示参数 | 改变图例与数值解释 |
| 调整透明度 | 视觉效果 | 不应覆盖原始数值 |
| 取绝对值 | 会丢失正负语义，需明确标记 | 生成不同派生场 |
| min–max 归一化 | 可作为显示映射，但原单位必须保留 | 原始量纲和定量比较会丢失 |
| 重新采样 | 可生成显示缓存 | 必须记录插值、坐标和来源 |

安全规则：

1. 权威科学数组不被显示优化静默覆盖。
2. 改变数值的操作应成为有名称和来源的派生数据。
3. 图例说明显示值还是物理值。
4. 原始单位、范围和 semantic role 始终可追溯。

---

# 第六章：ChemBlender 标准化数据模型

## 28. ArrayData

ArrayData 把数组和结构信息绑定：

~~~json
{
  "dims": ["x", "y", "z"],
  "unit": "electron_per_cubic_bohr",
  "shape": [56, 49, 60],
  "dtype": "float64"
}
~~~

dims 是轴名字，unit 是值单位，shape 是轴长度，dtype 是数值类型。x/y/z 和 z/y/x 可能有相同数量的数，但空间解释不同。

## 29. PropertyDataset

PropertyDataset 的核心字段：

~~~text
id
revision
semantic_role
domain
data
status
source_calculation
provenance_ids
~~~

status 可以是 complete、partial 或 ambiguous。如果单位未知，当前模型要求保持 ambiguous，不能把未知单位伪装成完成数据。

## 30. Grid3D

Grid3D 增加：

~~~text
origin
step_vectors
coordinate_unit
structure_id
~~~

简化的电子密度对象：

~~~json
{
  "semantic_role": "electron_density",
  "domain": "grid",
  "status": "complete",
  "coordinate_unit": "bohr",
  "origin": [-5.9075, -5.9275, -6.7025],
  "step_vectors": [
    [0.25, 0.0, 0.0],
    [0.0, 0.25, 0.0],
    [0.0, 0.0, 0.25]
  ],
  "data": {
    "dims": ["x", "y", "z"],
    "unit": "electron_per_cubic_bohr",
    "shape": [56, 49, 60]
  }
}
~~~

这只说明标准化模型中的一个电子密度网格，仍需要 provenance_ids 说明如何生成。

## 31. BasisSet、OrbitalSet 和 DensityMatrix

BasisSet 记录名称、原子中心、壳层、原始指数、收缩系数、Cartesian/pure 约定、归一化方式和所属结构。只保存一个 7×7 系数矩阵而没有基组，通常不足以复现轨道。

OrbitalSet 记录 structure_id、basis_set_id、kind、channels 和 provenance_ids。每个 OrbitalChannel 记录 label、coefficients、energies、occupations 和可选不可约表示。能量单位为 Hartree，占据数无量纲。

通道合同：

~~~text
restricted   → restricted
unrestricted → alpha, beta
generalized  → generalized
~~~

DensityMatrix 记录 structure_id、basis_set_id、level、spin_role、方阵数据、source_calculation 和 provenance_ids。当前模型要求它是实数、方阵、无量纲，并且行列都对应基函数。

## 32. 来源链

**Provenance**（来源链）应能回答：

- 原始文件是什么；
- SHA-256 是什么；
- 使用哪个 Reader 和版本；
- 使用哪个后端；
- 操作名称和参数是什么；
- 父对象有哪些；
- 当前对象 revision 是什么。

**SHA-256** 是内容哈希算法。哈希能确认字节身份，但不能单独证明科学语义正确。

## 33. 工作流生命周期

~~~text
inspect
  → 确认 Reader、源文件、哈希和可用性
derive
  → 从 Structure/BasisSet/OrbitalSet/DensityMatrix 求场
validate
  → 检查 CBQ 的实体、单位、数组和来源合同
Preview / Import
  → 将 CBQ 导入 Blender
Create View
  → 创建体、面、切片或表面属性
Save / Reopen
  → 保存 .blend 与相邻 .cbq
Rebuild
  → 从权威 CBQ 数据重建显示
~~~

**显示缓存**（render cache）是可重建的体积或网格文件；它不应替代 CBQ 中的权威科学数组。

---

# 第七章：H₂O 和 CH₃ 实例

## 34. H₂O FCHK

当前 H₂O fixture 包含：

~~~text
路径：
examples/scientific-visualization/inputs/wavefunction/
water_sto3g_hf_g03.fchk

方法：RHF / STO-3G
原子数：3
总电荷：0
多重度：1
电子数：10
alpha 电子数：5
beta 电子数：5
基函数数：7
~~~

阅读顺序：确认文件和哈希；读取原子和坐标；读取电荷、电子数、多重度；读取方法和基组；读取壳层、指数和收缩系数；读取轨道系数、能量和占据；确认密度矩阵；最后才派生网格。

对象链：

~~~text
FCHK
  ├─ Structure
  ├─ BasisSet
  ├─ OrbitalSet: kind = restricted
  ├─ DensityMatrix: level = scf, spin_role = total
  └─ Provenance: source hash + reader + method/basis evidence
~~~

求值关系：

~~~text
BasisSet + OrbitalSet + points
  → MO grid

BasisSet + DensityMatrix + points
  → electron density grid

DensityMatrix + nuclear coordinates/charges + points
  → ESP grid
~~~

## 35. H₂O 网格

当前水分子 CBQ 示例中可以看到类似元数据：

~~~text
semantic_role: molecular_orbital
coordinate_unit: bohr
origin: [-5.9075, -5.9275, -6.7025]
step_vectors:
  [0.25, 0.0, 0.0]
  [0.0, 0.25, 0.0]
  [0.0, 0.0, 0.25]
shape: [56, 49, 60]
value unit: inverse_bohr_to_three_halves
~~~

同一空间网格上，电子密度值单位会是 electron_per_cubic_bohr；ESP 可能是 hartree_per_elementary_charge。即使 shape 相同，也必须依据 semantic_role 和 unit 区分。

## 36. CH₃ 迁移

CH₃ fixture 用于开放壳层迁移检查。重点是：

- 轨道可能分为 alpha 和 beta 通道；
- 总密度和自旋密度不是同一个数组；
- 不能把单个 alpha 或 beta 通道当总密度；
- 自旋密度允许正负；
- OrbitalKind 和 DensityMatrixSpin 必须与文件实际内容对应。

迁移时填写：

~~~text
输入文件：
方法/基组：
总电荷：
多重度：
alpha/beta 信息：
轨道通道：
密度矩阵层级：
派生网格语义：
验证目标：
~~~

没有文件证据的项目标为未知，不根据分子名称猜测。

---

# 第八章：最小科学验证

## 37. 四个层次

### A：字节和来源

源文件存在、SHA-256 匹配、文件大小和路径记录、Reader/版本/解析参数记录。

### B：结构和模型

原子数、原子序数和坐标一致；单位明确；电荷和多重度有来源；方法、基组和电子态有来源；Structure、BasisSet、OrbitalSet 和 DensityMatrix 的 ID 关系正确。

### C：数值对象

dtype、shape、dims 正确；单位合法；数值有限；网格几何正确；积分或范数符合定义；多通道和多 dataset 没有被静默丢弃。

### D：显示和生命周期

View 绑定正确实体；图例单位正确；几何场和着色属性场分开；.blend 与 .cbq 可冷重开；删除缓存后可恢复；渲染参数没有改写权威数组。

## 38. 轨道、密度和网格

归一化轨道可检查：

~~~text
Cᵀ S C ≈ I
∫ |φ(r)|² d³r ≈ 1
~~~

电子密度可检查：

~~~text
∫ ρ(r) d³r ≈ N
Tr(P S) ≈ N
~~~

网格必须足够大、足够细；不能用截断后的等值面几何估计归一化。积分通过也不能证明每个局部点正确。

逐项检查网格：dims、shape、origin、三条 step vectors、coordinate_unit、value unit、有限性、dataset_index、体积因子和插值规则。

ESP 还要检查核排除半径、非有限值、全电子或 ECP 模型、势零点和单位。不能把 inf 或 NaN 静默填为 0。

## 39. 后端对照

两个后端只有在相同几何、电荷、电子态、方法、基组约定、密度矩阵、坐标点、单位、轴序、网格和积分条件下，才适合逐点比较。

两个程序各自独立做了 SCF，不代表产生了同一个波函数。

## 40. 证据边界

| 证据 | 能证明 | 不能证明 |
|---|---|---|
| 文件存在 | 路径可访问 | 内容语义正确 |
| OCR 有文本 | 文字可搜索 | 公式和定义准确 |
| Parser 成功 | 语法被读取 | 物理量映射正确 |
| 数组有限 | 没有 NaN/Inf | 单位、语义和坐标正确 |
| Render 成功 | 视图可生成 | 物理结论正确 |
| 电子数积分正确 | 一个整体不变量合理 | 局部场处处正确 |
| 冷重开成功 | 工程可恢复 | 原始科学计算无误 |

科学验收应组合多个独立检查。

---

# 第九章：常见错误

## 41. 把容器名当物理量

错误思路是“Cube 就是电子密度”。正确思路是“Cube 是三维场容器，实际物理量由来源、单位、生成操作和语义证据确定”。

## 42. 把 MO 当电子密度

MO 是振幅；电子密度是由占据轨道或密度矩阵求得的空间分布。MO 的负值通常是相位，不是负电子数。

## 43. 把自旋密度当总密度

自旋密度是 alpha 和 beta 通道的差，可正可负；总电子密度是通道的适当组合。两者单位可能相同，但 semantic role 不同。

## 44. 只看 shape

两个数组 shape 相同，不代表单位、物理量、origin、step vectors 相同，也不代表可以直接相减或绑定到同一表面。

## 45. 用视觉处理掩盖语义问题

需要警惕：取绝对值后不标记；归一化后仍显示原单位；把未声明字段命名成电子密度；用颜色替代数值图例；把光照当作场值变化；用截图证明数组正确；用同一个错误中间量产生“双方一致”的结果。

最小修复原则：保留原始数据，再建立明确命名、单位和来源的派生数据。

---

# 第十章：四小时首轮路线与卡片

| 时间 | 目标 | 产出 | 通过标准 |
|---|---|---|---|
| 0–15 分钟 | 暴露语义断点 | LX00 独立回答 | 区分已确认、推断、未知 |
| 15–45 分钟 | 区分 MO、密度、ESP | 三张物理量卡片 | 定义、单位、符号和输入不混淆 |
| 45–90 分钟 | 连接矩阵与场 | H₂O 数据链草图 | 说明 C、P、S 和空间求值 |
| 90–150 分钟 | 读取核心格式 | 格式—物理量表 | 判断字段有无和缺失边界 |
| 150–180 分钟 | 理解网格 | Grid3D 检查表 | 解释 origin、step、shape、unit |
| 180–210 分钟 | 复核 H₂O | 来源和验证记录 | 证据定位到源文件和派生操作 |
| 210–230 分钟 | 迁移 CH₃ | alpha/beta 检查 | 不把开放壳层压成闭壳层 |
| 230–240 分钟 | 复述和审计 | 独立解释 | 说明错误显示方案为何不成立 |

## 46. 物理量卡片

~~~text
名称：
英文或 semantic_role：
数学定义：
输入对象：
输出对象：
值单位：
坐标单位：
是否允许正负：
积分或范数检查：
适合的显示：
不能做的解释：
来源证据：
未知项：
~~~

## 47. 格式卡片

~~~text
格式：
主要描述：
可以包含：
不能保证：
当前 Reader：
解析后对象：
关键单位：
计算条件：
常见误判：
来源和 hash：
~~~

## 48. Grid3D 卡片

~~~text
semantic_role：
dataset_index：
dims：
shape：
origin：
step_vectors：
coordinate_unit：
value_unit：
数据范围：
finite 检查：
积分或范数：
来源对象：
派生操作：
缓存和恢复方式：
~~~

训练规则：

- 先凭自己的理解预测，再查资料或运行检查。
- 一次只处理一个主要断点。
- 错误时先定位哪一个判断不成立，再补最小概念。
- 听懂解释不等于独立掌握。
- 能在 H₂O 上完成不等于能处理 CH₃，必须做一次变体迁移。
- 学习状态只有在学习者实际作答或操作后更新。

LX00 练习保留在 [EXERCISES.md](exercises/EXERCISES.md)，本教材不附标准答案。

---

# 第十一章：首轮不要求的内容

下面内容重要，但不阻塞首轮：

- 完整 HF/DFT 推导；
- 两电子积分、耦合簇和多参考理论；
- 自己实现 SCF/DFT 求解器；
- GPU 和大规模并行优化；
- 周期边界、声子、能带、DOS/PDOS；
- 激发态、NTO、振动、IR/Raman；
- NCI、ELF、QTAIM 的完整理论和后端；
- 所有结构和计算文件格式；
- 完整 Reader API 开发。

进入这些专题前，应先能解释 H₂O 的 MO、电子密度和 ESP，读取 Grid3D 坐标和单位，完成最小数值验证，从 CBQ 冷重开并处理一个新变体。

---

# 第十二章：资料入口

## 学习包

- [REPORT.md](REPORT.md)：调研边界、概念骨架和路线。
- [CODEX_HANDOFF.md](CODEX_HANDOFF.md)：导师规则、现场核验和状态维护。
- [reading_manifest.json](reading_manifest.json)：资料身份、优先级和本地状态。
- [materials/catalog/README.md](materials/catalog/README.md)：目录优先 OCR 导航。
- [exercises/EXERCISES.md](exercises/EXERCISES.md)：LX00–LX08 练习。
- [learning_state.json](learning_state.json)：只记录有学习者证据的能力状态。

## ChemBlender 当前文档

- [波函数、网格与表面开发计划](../../../ChemBlender_2_x/docs/quantum-visualization/plans/wavefunction-and-grids.md)
- [格式与支持范围](../../../ChemBlender_2_x/docs/user/workflows/formats.md)
- [密度网格、切片与正负等值面](../../../ChemBlender_2_x/docs/user/zh-CN/density-grids.md)
- [自动化与 Reader 扩展 API](../../../ChemBlender_2_x/docs/prepare/zh-CN/protocol-and-reader-api.md)
- [Blender 完整联动 SOP](../../../ChemBlender_2_x/docs/user/zh-CN/blender-workflow.md)

## 当前实现和输入

- Grid3D：../../../ChemBlender_2_x/cbq_core/model/grids.py
- ArrayData：../../../ChemBlender_2_x/cbq_core/model/arrays.py
- OrbitalSet 和 DensityMatrix：../../../ChemBlender_2_x/cbq_core/model/wavefunction.py
- 网格语义预设：../../../ChemBlender_2_x/cbq_core/grid_semantics.py
- 波函数网格求值：../../../ChemBlender_2_x/chemblender_prepare/core/wavefunction_grid.py
- H₂O FCHK：examples/scientific-visualization/inputs/wavefunction/water_sto3g_hf_g03.fchk
- CH₃ FCHK：examples/scientific-visualization/inputs/wavefunction/ch3_hf_sto3g.fchk
- H₂O CBQ manifest：examples/scientific-visualization/output/molecular/scenes/water.cbq/manifest.json
- 输入身份和哈希：examples/scientific-visualization/input-manifest.json

---

# 附录：最小术语表

| 术语 | 含义 |
|---|---|
| AO | 原子轨道，atomic orbital |
| MO | 分子轨道，molecular orbital |
| HF | Hartree–Fock，平均场电子结构方法 |
| DFT | Density Functional Theory，密度泛函理论 |
| SCF | Self-Consistent Field，自洽场迭代 |
| 1-RDM | one-particle reduced density matrix，一粒子约化密度矩阵 |
| ESP | electrostatic potential，静电势 |
| FCHK | Gaussian formatted checkpoint，格式化检查点文件 |
| QCSchema | 量子化学计算交换数据结构 |
| Cube | 常见三维体数据文本容器 |
| CBQ | ChemBlender 科学项目边车格式 |
| Grid3D | 带空间坐标和语义的三维网格对象 |
| provenance | 数据来源链 |
| semantic role | 数据的物理或科学语义角色 |
| post-SCF | SCF 之后的相关方法或分析 |
| field-on-surface | 在一个场定义的表面上采样另一个场 |
| restricted | 共享空间轨道的闭壳层表示 |
| unrestricted | alpha/beta 通道可分开的开放壳层表示 |
| generalized | 更一般的自旋轨道表示 |
| ECP | effective core potential，有效芯势或赝势 |
| NCI | non-covalent interaction，非共价相互作用分析 |
| ELF | electron localization function，电子局域函数 |
| QTAIM | quantum theory of atoms in molecules，分子中原子量子理论 |

## 读完后的自测

1. 为什么一个 Cube 文件名不能证明它是电子密度？
2. 为什么 MO 可以有负值，而总电子密度通常不应解释为负值？
3. 为什么 shape 相同的两个 Grid3D 仍可能不能相减？
4. 为什么 Tr(P) 通常不能代替 Tr(P S)？
5. 为什么 Render 成功不能代替 provenance 和数值验证？
6. H₂O 和 CH₃ 的轨道通道与密度语义可能有什么不同？
