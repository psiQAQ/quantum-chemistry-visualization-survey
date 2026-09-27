# 量子化学数据语义与 ChemBlender

## 从薛定谔方程到分子计算输入、输出与三维场的完整概念教程

**版本**：2026-09-22

**适用对象**：第一次系统学习分子量子化学，希望理解 Gaussian、ORCA、PySCF 等软件能计算什么，以及输入、输出和结果限制的学习者

**最终用途**：看到未见过的分子计算输入或输出时，能识别体系、方法、任务、主要数值及其物理含义，检查结果是否足以支持相应解释，并理解它如何进入 ChemBlender 的数据链

**学习方式**：长期、逐章学习；每章先理解概念，再阅读一种实际输入或输出片段，最后独立解释一个变体。当前不要求安装、运行软件或编写脚本

**能力边界**：讲清必要假设、关键公式、计算任务和结果解释；不要求推导多电子积分、实现求解器或掌握高阶电子相关理论。阅读并解释样例不等于已具备独立运行软件的操作能力

> 本教材不是阅读完成证明。只有学习者独立完成练习和迁移任务，才可以更新
> `learning_state.json`。LX00 仍保留在 [EXERCISES.md](exercises/EXERCISES.md)，这里不提供其标准答案。

---

## 0. 学习契约

### 0.1 最终实战

给定一份未见过的分子计算输入和对应输出，独立完成以下工作：

1. 说明分子结构、坐标单位、电荷、自旋、方法、基组和计算任务。
2. 区分单点能、几何优化、振动频率和激发态计算，指出输出分别回答什么问题。
3. 解释能量、轨道、电子密度、原子部分电荷、静电势、振动模式或跃迁数据的含义、单位与条件。
4. 检查收敛、态身份、基组和近似误差，指出还不能从输出推出什么。
5. 将同一物理问题迁移到另一软件的输入和输出表达；需要可视化时，说明 ChemBlender 可接收哪些科学对象及转换损失。

### 0.2 成功标准

- 能从输入独立说明体系、电子数、自旋、方法、基组和任务；知道不同软件的字段约定可能不同。
- 能读懂本教材中的关键公式，说明成立条件、符号、对应数据和不能推出的结论。
- 能从输出区分单点能、优化、频率、轨道/密度/静电势和激发态结果，并正确报告单位与计算条件。
- 能检查收敛和最基本的物理一致性；不把容器格式、物理量或显示方式混为一谈。
- 能在 H₂O 案例上完成解释，并迁移到开放壳层分子或另一软件的同类结果。

### 0.3 学习裁剪

**主线深入学**：分子与电子态输入；薛定谔方程、Born-Oppenheimer 近似和原子单位；HF/SCF、Gaussian 基组和 Kohn-Sham DFT；单点能、优化和频率；轨道、密度、电荷分析和静电势；收敛、误差、单位及来源核验。

**主线完成后学**：激发态、垂直激发能、跃迁强度及其解释。完整 Reader 格式族按实际文件查 [ChemBlender 输入格式图鉴](CHEMBLENDER_INPUT_FORMAT_ATLAS.md)。

**暂缓**：周期固体软件、反应路径、溶剂和分子间作用专题；两电子积分详细求积、完整求解器实现、耦合簇和多参考方法推导、相对论与 GPU 优化。

**CBQ 项目边车**（CBQ sidecar）：与 `.blend` 工程相邻、保存可恢复科学实体和数组的 ChemBlender 项目目录。

**NumPy 数组文件**（`.npy`）：保存数组数据类型（dtype）、形状（shape）和数值的二进制文件；单独使用时通常没有物理语义、坐标和来源。

**基石练习**（Keystone Exercise）：面对 H₂O 的输入、单点/优化/频率输出与 FCHK，独立说明计算条件、结果意义和验证要求；再把同一检查表迁移到 CH₃ 或另一软件。需要可视化时，继续追踪 `Structure → BasisSet → OrbitalSet/DensityMatrix → MO/ρ/ESP Grid3D → CBQ → View`。

---

# 第一章：先看整条计算与可视化架构

## 1.1 量子化学计算不是“输入文件直接变成图片”

**量子化学**（quantum chemistry）：用量子力学模型计算分子的能量、电子状态与性质。本教程以孤立分子为主。

**电子结构**（electronic structure）：体系的电子能量、轨道、密度、占据和响应等信息。

**解析器**（parser）：把文件语法转换成程序内结构化对象的代码。

**来源链**（provenance）：记录数据来自哪个文件、程序、方法、参数和派生步骤的证据。

```text
几何、电子态和计算设置
        ↓
输入解析与模型建立
        ↓
积分/数值算符、初始猜测、SCF 或其他求解
        ↓
能量、轨道、密度矩阵、梯度、频率等结果
        ↓
结果文件或交换数据结构
        ↓
ChemBlender Reader
        ↓
Structure / BasisSet / OrbitalSet / DensityMatrix / PropertyDataset
        ↓
在空间点上评价 MO、密度、ESP 等量
        ↓
Grid3D + 来源链
        ↓
CBQ 项目边车 + .npy 数组
        ↓
Blender 体、面、切片、剖面或表面着色 View
```

| 层 | 主要问题 | 不能自动证明 |
|---|---|---|
| 计算 | 模型和参数下求得了什么 | 文件一定写全、状态一定正确 |
| 解析 | 哪些字段被读成对象 | 字段物理含义一定无歧义 |
| 派生/采样 | 如何从系数或矩阵得到空间场 | 单位、范围和误差一定正确 |
| 渲染 | 如何把数值映射成几何和颜色 | 计算、解析和数值结果正确 |

### 阅读输入输出的四个固定问题

1. 权威科学数据在哪个对象中？
2. 单位、坐标、物理语义和计算条件保存在哪里？
3. 数据经过了哪些解析、转换或采样？
4. 结果是否满足基本收敛与物理检查；若导入 ChemBlender，科学对象与显示结果能否追溯到原始条件？

**本节来源**：[SW04, PDF p.5]；ChemBlender 当前架构文档与模型源码。

---

# 第二章：从薛定谔方程到分子电子问题

## 2.1 含时与定态薛定谔方程

**波函数**（wavefunction，\(\Psi\)）：描述量子态的复值函数。

**哈密顿算符**（Hamiltonian，\(\hat H\)）：对波函数作用并表示体系总能量的算符。

**算符**（operator）：把一个函数映射成另一个函数的运算规则。

含时薛定谔方程是

$$
i\hbar\frac{\partial \Psi(\mathbf{x},t)}{\partial t}
=\hat H\Psi(\mathbf{x},t).
$$

若 \(\hat H\) 不显含时间，并寻找能量确定的定态，可分离出

$$
\hat H\psi(\mathbf{x})=E\psi(\mathbf{x}).
$$

**成立条件**：第二式针对时间无关哈密顿量的定态本征值问题。

**符号**：\(i\) 是虚数单位；\(\hbar\) 是约化普朗克常数；\(\mathbf{x}\) 代表全部所需坐标；\(E\) 是能量本征值。

**数据对应**：程序通常不保存完整多电子 \(\Psi\)，而保存能量、MO 系数、占据数、密度矩阵或采样场。

**不能推出**：只见到 `SCF Energy` 或轨道系数，不能反推出所有求解条件和完整多电子波函数。

**本节来源**：[CT, 课件 2 p.5，已核对页面图]；[BK01, PDF p.113]。

## 2.2 归一化与期望值

**归一化**（normalization）：把总概率或粒子数缩放到定义要求的值。

**期望值**（expectation value）：在给定状态下重复测量某物理量的平均值。

$$
\langle\Psi|\Psi\rangle
=\int \Psi^*(\mathbf{x})\Psi(\mathbf{x})\,d\mathbf{x}=1,
$$

$$
\langle \hat A\rangle
=\frac{\langle\Psi|\hat A|\Psi\rangle}
       {\langle\Psi|\Psi\rangle}.
$$

**成立条件**：积分覆盖波函数的全部坐标；第一式用于归一化态。

**符号**：星号表示复共轭；\(\hat A\) 是可观测量对应算符；尖括号是内积记号。

**数据对应**：MO 系数的归一化需要 AO 重叠矩阵；网格 MO 的平方模可做数值积分检查。

**不能推出**：某个裁剪网格上的积分接近 1，不证明波函数的全部局部值正确。

**本节来源**：[BK01, PDF pp.113,117]。

## 2.3 非相对论分子哈密顿量

**非相对论近似**（non-relativistic approximation）：在首轮模型中不显式处理接近光速运动及自旋-轨道等相对论效应。

**有效芯势**（effective core potential，ECP，常称赝势）：用一个有效势替代部分芯电子及其与原子核的显式相互作用。

在原子单位制下，分子的非相对论哈密顿量可分成

$$
\hat H_{\mathrm{tot}}
=\hat T_n+\hat T_e+\hat V_{ne}+\hat V_{ee}+\hat V_{nn},
$$

其中

$$
\begin{aligned}
\hat T_n&=-\sum_A\frac{1}{2M_A}\nabla_A^2,\\
\hat T_e&=-\sum_i\frac{1}{2}\nabla_i^2,\\
\hat V_{ne}&=-\sum_A\sum_i\frac{Z_A}{|\mathbf R_A-\mathbf r_i|},\\
\hat V_{ee}&=\sum_i\sum_{j>i}\frac{1}{|\mathbf r_i-\mathbf r_j|},\\
\hat V_{nn}&=\sum_A\sum_{B>A}\frac{Z_AZ_B}{|\mathbf R_A-\mathbf R_B|}.
\end{aligned}
$$

**成立条件**：采用非相对论、库仑相互作用和原子单位；未写质量极化等小修正。

**符号**：\(A,B\) 标记原子核；\(i,j\) 标记电子；\(M_A\)、\(Z_A\)、\(\mathbf R_A\) 是核质量、核电荷数和核坐标；\(\mathbf r_i\) 是电子坐标。

**数据对应**：结构文件给出元素和 \(\mathbf R_A\)；总电荷决定电子总数；ECP/赝势会改变核附近的有效描述。

**不能推出**：仅凭原子坐标不能确定电子态、方法、基组、边界条件或密度。

**本节来源**：[BK01, PDF pp.113,118, Eq. 3.3/3.24]；[BK02, PDF pp.58-60]。

## 2.4 Born-Oppenheimer 近似

**Born-Oppenheimer 近似**（Born-Oppenheimer approximation，BO 近似）：利用原子核远重于电子的质量差，在求电子态时把核坐标暂作固定参数。

总波函数近似分离为

$$
\Psi_{\mathrm{tot}}(\mathbf r,\mathbf R)
\approx \psi_e(\mathbf r;\mathbf R)\,\chi_n(\mathbf R),
$$

固定 \(\mathbf R\) 后先求电子方程

$$
\hat H_e(\mathbf R)\psi_e(\mathbf r;\mathbf R)
=E_e(\mathbf R)\psi_e(\mathbf r;\mathbf R),
$$

$$
\hat H_e=\hat T_e+\hat V_{ne}+\hat V_{ee}+V_{nn}(\mathbf R).
$$

**成立条件**：电子与核运动可近似分离；在强非绝热耦合、势能面交叉、某些激发态或强场问题中可能失效。

**符号**：\(\mathbf r\) 汇总电子坐标；\(\mathbf R\) 汇总核坐标；\(\psi_e\) 是参数依赖于核位置的电子波函数；\(\chi_n\) 是核波函数。

**数据对应**：单点计算文件中的坐标是某个固定几何；几何优化则在多个几何上反复求电子问题。

**不能推出**：BO 近似不等于删除原子核；核电荷、坐标与核-核排斥仍进入电子能量。

**本节来源**：[BK01, PDF pp.113-116]；[BK02, PDF pp.58-60]；[CT, 课件 2 p.17]。

## 2.5 原子单位、坐标和电子态

**原子单位制**（atomic units，a.u.）：令基本常量 \(m_e=e=\hbar=4\pi\varepsilon_0=1\) 的单位体系，量子化学公式因此更简洁。

| 量 | 常见单位 | 关键换算 |
|---|---|---|
| 长度 | bohr、ångström（Å） | \(1\ \mathrm{bohr}\approx0.529177\ \mathrm{Å}\) |
| 能量 | hartree（\(E_h\)）、eV | \(1\ E_h\approx27.2114\ \mathrm{eV}\) |
| 轨道振幅 | \(\mathrm{bohr}^{-3/2}\) | 平方后成为 \(\mathrm{bohr}^{-3}\) |
| 电子密度 | electron/bohr³ | 空间积分给电子数 |
| 静电势 | hartree/e | 需说明势零点和边界 |

**ångström**（Å）：常用于原子坐标的长度单位。

**bohr**：原子单位制的长度单位。

**hartree**：原子单位制的能量单位。

电子数由

$$
N_e=\sum_A Z_A-q
$$

决定，其中 \(q\) 是以正电为正的总电荷。自旋多重度为

$$
M=2S+1=N_\alpha-N_\beta+1
$$

（第二个等号针对通常取最高 \(M_S\) 分量的输入约定）。

**成立条件**：体系电子数为整数，且输入程序采用常见电荷和多重度约定。

**符号**：\(S\) 是总自旋量子数；\(N_\alpha,N_\beta\) 是 alpha/beta 自旋电子数。

**数据对应**：Gaussian/ORCA 输入和 FCHK 常显式保存 charge、multiplicity、alpha/beta electron counts。

**不能推出**：只知道 \(N_\alpha,N_\beta\) 不能证明波函数没有自旋污染或电子态正确。

**本节来源**：[BK01, Appendix C；PDF pp.117,129]；H₂O/CH₃ FCHK fixtures。

---

# 第三章：Hartree-Fock、基组与 SCF

## 3.1 变分原理与反对称性

**变分原理**（variational principle）：任意合格试探波函数的能量期望值不低于基态精确能量。

$$
E[\Psi_t]
=\frac{\langle\Psi_t|\hat H_e|\Psi_t\rangle}
       {\langle\Psi_t|\Psi_t\rangle}
\ge E_0.
$$

**费米子反对称性**（fermionic antisymmetry）：交换任意两个电子的全部坐标，多电子波函数必须变号。

$$
\Psi(\ldots,\mathbf x_i,\ldots,\mathbf x_j,\ldots)
=-\Psi(\ldots,\mathbf x_j,\ldots,\mathbf x_i,\ldots).
$$

**成立条件**：\(\Psi_t\) 满足边界、归一化和粒子交换等要求；\(\hat H_e\) 为厄米算符。

**符号**：下标 \(t\) 表示试探态；\(E_0\) 是同一哈密顿量下的基态能量；\(\mathbf x\) 同时含空间和自旋坐标。

**数据对应**：计算方法和波函数类型决定试探空间；优化的是轨道或展开系数，不是随意改文件中的能量。

**不能推出**：更低的两个近似方法能量不能在不同哈密顿量、几何或基组下直接比较优劣。

**本节来源**：[BK01, PDF p.117, Eq. 3.19]。

## 3.2 单 Slater 行列式假设

**Slater 行列式**（Slater determinant）：由单电子自旋轨道构成、自动满足交换反对称性的行列式。

$$
\Phi(1,\ldots,N)=\frac{1}{\sqrt{N!}}
\begin{vmatrix}
\phi_1(1)&\cdots&\phi_N(1)\\
\vdots&\ddots&\vdots\\
\phi_1(N)&\cdots&\phi_N(N)
\end{vmatrix}.
$$

Hartree-Fock（HF）进一步把试探多电子态限制为一个 Slater 行列式。

**成立条件**：轨道满足正交归一；采用单行列式平均场近似。

**符号**：\(N\) 是电子数；\(\phi_p(i)\) 是第 \(p\) 个自旋轨道在电子 \(i\) 坐标处的值。

**数据对应**：RHF/UHF/ROHF 文件保存的轨道系数和占据可构造相应行列式或密度。

**不能推出**：单行列式不能完整表示电子相关；HF 交换来自反对称性，但相关能量仍缺失。

**本节来源**：[BK01, PDF pp.117-118, Eq. 3.20]。

## 3.3 Gaussian 基函数

**基组**（basis set）：用有限组已知函数展开未知轨道的近似空间。

**Gaussian 原始函数**（Gaussian primitive）：指数部分为 \(e^{-\alpha r^2}\) 的单个高斯函数。

**收缩函数**（contracted function）：多个原始函数按系数线性组合得到的基函数。

**笛卡尔 Gaussian**（Cartesian Gaussian）：用 \(x,y,z\) 的多项式表达角向部分的 Gaussian 函数。

笛卡尔 Gaussian 原始函数可写为

$$
g_i(\mathbf r;\mathbf R_A,\mathbf a)
=N_i(x-X_A)^{a_x}(y-Y_A)^{a_y}(z-Z_A)^{a_z}
e^{-\alpha_i|\mathbf r-\mathbf R_A|^2},
$$

收缩函数为

$$
\chi_\mu(\mathbf r)=N_c\sum_i d_{i\mu}g_i(\mathbf r).
$$

**成立条件**：采用 Gaussian 型轨道基组；笛卡尔表达使用非负整数角动量分量 \(a_x,a_y,a_z\)。

**符号**：\(\alpha_i\) 是指数，控制函数宽窄；\(d_{i\mu}\) 是收缩系数；\(N_i,N_c\) 是归一化因子；\(\mathbf R_A\) 是函数中心。

**数据对应**：FCHK/Molden 常保存壳层、中心、角动量、指数和收缩系数；解析器必须知道 primitive 顺序、归一化和收缩约定。

**不能推出**：基组名称相同不保证不同程序导出的 AO 顺序、归一化或球谐/笛卡尔约定相同。

**球谐 Gaussian**（spherical Gaussian）：用球谐函数表示角向部分，也称 pure/spherical 基函数。对于 \(d\) 壳及更高角动量，笛卡尔与球谐函数数量和排序不同，文件转换必须显式处理。

**本节来源**：[SW01, PDF p.6, Eq. 1-3]；[BK01, PDF p.124]。

## 3.4 AO、MO 与重叠矩阵

**原子轨道基函数**（atomic orbital basis function，AO）：通常以原子为中心、用于展开轨道的基函数；它不一定是孤立原子的真实轨道。

**分子轨道**（molecular orbital，MO）：在整个分子上定义的单电子轨道。

$$
\phi_p(\mathbf r)=\sum_{\mu=1}^{K}C_{\mu p}\chi_\mu(\mathbf r).
$$

AO 一般不互相正交，重叠矩阵为

$$
S_{\mu\nu}=\langle\chi_\mu|\chi_\nu\rangle
=\int\chi_\mu^*(\mathbf r)\chi_\nu(\mathbf r)\,d\mathbf r.
$$

**成立条件**：使用有限线性基组；若系数或基函数为复数，应使用共轭转置。

**符号**：\(K\) 是 AO 数；\(C_{\mu p}\) 是 MO 系数；\(S\) 衡量 AO 非正交性。

**数据对应**：`BasisSet` 给出 \(\chi_\mu\)；`OrbitalSet` 给出 \(C\)、轨道能量、占据和通道；两者缺一不能在空间点评价 MO。

**不能推出**：MO 系数数组本身不是三维轨道场；必须结合基函数、中心、顺序和坐标才能求值。

**本节来源**：[BK01, PDF p.124, Eq. 3.52]；[CT, 课件 2 p.62，已核对页面图]。

## 3.5 Fock 方程与 Roothaan-Hall 方程

**Fock 算符**（Fock operator，\(\hat f\)）：HF 中包含单电子项、平均库仑作用和交换作用的有效单电子算符。

$$
\hat f\phi_p=\varepsilon_p\phi_p.
$$

把 MO 用 AO 基组展开后得到广义矩阵本征值问题：

$$
\mathbf F\mathbf C=\mathbf S\mathbf C\boldsymbol\varepsilon.
$$

这就是闭壳层 HF 的 Roothaan-Hall 方程；UHF 对 alpha、beta 通道分别有耦合的矩阵方程。

**成立条件**：采用 HF 单行列式与有限 AO 基组；\(F\) 依赖当前密度，因此问题仍是非线性的。

**符号**：\(F_{\mu\nu}=\langle\chi_\mu|\hat f|\chi_\nu\rangle\)；\(C\) 的列是 MO 系数；\(\boldsymbol\varepsilon\) 是轨道能量对角矩阵。

**数据对应**：FCHK/Molden 可保存 \(C\) 和 \(\varepsilon\)；重叠矩阵常需由同一基组重新计算或从后端获得。

**不能推出**：轨道能量不等于任意实验激发能；虚轨道的解释尤其依赖基组和近似。

**本节来源**：[BK02, PDF p.153, Eq. 3.139]；[CT, 课件 2 pp.62-63，已核对页面图]。

## 3.6 密度矩阵与 SCF

对给定 MO 占据数 \(n_p\)，AO 密度矩阵可写为

$$
\mathbf P=\mathbf C\,\mathrm{diag}(n_p)\,\mathbf C^\dagger.
$$

**自洽场**（Self-Consistent Field，SCF）：反复用当前密度构造有效算符、求新轨道、再更新密度，直到变化满足阈值的迭代过程。

**后-SCF 方法或结果**（post-SCF method/result）：在 SCF 参考态之后再加入电子相关修正或计算性质的方法与数据层级。

```text
几何/基组/方法/电子态
  → 一、二电子积分或相应数值算符
  → 初始密度 P(0)
  → 构造 F[P]
  → 解 FC = SCε
  → 按占据构造新 P
  → 检查能量、密度或残差
  → 收敛后计算性质并写文件
```

**成立条件**：\(P\) 与 \(C\) 使用同一 AO 顺序和归一化；占据数与电子态一致。

**符号**：\(C^\dagger\) 是共轭转置；\(n_p\) 常为 0、1、2 或分数占据；\(F[P]\) 强调 Fock 矩阵依赖密度。

**数据对应**：文件可记录初始猜测、迭代次数、收敛阈值、最终能量、轨道和密度矩阵；ChemBlender 还需记录 density level 是 `scf` 还是 `post_scf`。

**不能推出**：`converged=true` 只说明满足指定判据，不保证是全局最低能、稳定解、正确电子态或足够准确。

### 闭壳层与开放壳层的轨道约束

**限制性闭壳层 HF**（Restricted Hartree-Fock，RHF）：alpha、beta 成对电子共享同一空间轨道。

**非限制性 HF**（Unrestricted Hartree-Fock，UHF）：alpha、beta 可使用不同空间轨道。

**限制性开壳层 HF**（Restricted Open-shell Hartree-Fock，ROHF）：双占据轨道受共享约束，同时保留未成对电子。

**开放壳层**（open shell）：存在未成对电子的电子态。

SCF 结果至少要分开检查：数值收敛、轨道稳定性、目标态身份和方法/基组误差。

**本节来源**：[BK01, PDF pp.129-131]；[BK02, PDF pp.157,160,227-230]；[CT, 课件 2 p.68]。

---

# 第四章：Kohn-Sham 密度泛函理论

## 4.1 Hohenberg-Kohn 定理提供什么

**密度泛函理论**（Density Functional Theory，DFT）：以基态电子密度 \(\rho(\mathbf r)\) 为基本变量的理论框架。

**泛函**（functional）：把一个函数整体映射成数值的规则，例如 \(E[\rho]\)。

Hohenberg-Kohn 第一条定理说明，在给定粒子数和适用条件下，基态密度唯一决定外势（差一个常数），因而决定基态性质。可以概括为

$$
E_0=E[\rho_0].
$$

**成立条件**：讨论相互作用电子体系的基态；需要固定粒子数和外势问题。

**符号**：\(\rho_0\) 是基态电子密度；\(E[\rho]\) 是密度泛函。

**数据对应**：本教程的分子 DFT 输出仍需记录泛函、Gaussian 基组、数值积分网格、电子态和收敛条件。

**不能推出**：定理不直接给出未知泛函的实用表达式，也不表示任意近似泛函都精确。

**本节来源**：[CT, 课件 3 p.43，已核对页面图]；[BK01, Chapter 6]。

## 4.2 Kohn-Sham 方程

**Kohn-Sham 方法**（Kohn-Sham method，KS）：引入一个产生相同基态密度的非相互作用参考体系，用单电子方程求密度。

$$
\left[
-\frac12\nabla^2
+v_{\mathrm{ext}}(\mathbf r)
+v_H[\rho](\mathbf r)
+v_{xc}[\rho](\mathbf r)
\right]\phi_i(\mathbf r)
=\varepsilon_i\phi_i(\mathbf r),
$$

$$
\rho(\mathbf r)=\sum_i f_i|\phi_i(\mathbf r)|^2,
$$

$$
v_{xc}[\rho](\mathbf r)
=\frac{\delta E_{xc}[\rho]}{\delta\rho(\mathbf r)}.
$$

**交换-相关泛函**（exchange-correlation functional，XC functional）：收纳未知交换、相关以及 Kohn-Sham 动能修正的近似泛函。

**数值积分网格**（numerical integration grid）：用带权离散点近似积分的网格。

**色散修正**（dispersion correction）：补偿常见近似泛函对长程色散相互作用描述不足的附加模型。

**溶剂模型**（solvation model）：用显式或连续介质近似描述环境溶剂影响的模型。

**成立条件**：Kohn-Sham 基态 DFT；实际结果取决于所选 XC 近似和数值条件。

**符号**：\(v_{\mathrm{ext}}\) 是核等外部势；\(v_H\) 是经典 Hartree 库仑势；\(v_{xc}\) 是交换-相关势；\(f_i\) 是占据数。

**数据对应**：分子输入应保存泛函、基组、数值积分网格、电荷、自旋多重度和 SCF 阈值；若启用色散修正或溶剂模型，也要保存对应设置。输出保存 KS 轨道、占据、能量和密度。

**不能推出**：一般 KS 轨道和轨道能量不能无条件解释为可直接观测的单电子态或激发能。

**本节来源**：[BK01, PDF pp.259-260,283（印刷页 260）]；[CT, 课件 3 pp.47-48，已核对页面图]。

## 4.3 DFT 积分网格不等于可视化 Grid3D

分子 Gaussian 型 DFT 常用原子中心数值积分网格计算 XC 项；它可能由径向点和角向点组成，并经过 pruning（裁剪）。这不是最终用于显示的规则三维盒状网格。

| 网格 | 用途 | 常见结构 |
|---|---|---|
| DFT 数值积分网格 | 求 XC 能量、势和导数 | 原子中心、非均匀点集、权重 |
| 可视化 Grid3D | 采样 MO、密度或势以显示/分析 | origin + 三条 step vectors + shape |

改变 DFT 积分网格可能改变自洽密度；改变输出 Grid3D 的范围和步长主要改变采样误差和显示分辨率。两者都必须记录，但不能混用。

**本节来源**：[RV02, PDF pp.7,19-22]；[BK01, Chapter 6]。

---

# 第五章：计算任务决定输入与输出

同一个 H₂O 结构可以用于不同任务。先看软件要回答的问题，再看它使用的方法和基组；仅凭输出里出现一个能量数字，无法知道做了哪种任务。

| 任务 | 固定或变化的量 | 主要输出 | 首先核实 |
|---|---|---|---|
| 单点能 | 原子核坐标固定，求该几何上的电子态 | 总能量、SCF 记录；可请求轨道或性质 | 坐标、电子态、方法和基组 |
| 几何优化 | 在一系列核坐标上重复能量/梯度计算 | 结构轨迹、最终结构、梯度和终止判据 | 是否真正达到收敛阈值、有无约束 |
| 振动频率 | 在指定几何附近分析能量曲率 | 频率、正常模、零点能及部分光谱强度 | 是否在已优化的同一几何上、是否有虚频 |
| 激发态 | 在给定参考态与几何上求较高电子态 | 激发能、跃迁强度、态的组成 | 基态参考、态序号、自旋与几何 |

## 5.1 单点能：一个固定几何对应一个能量

**单点能计算**（single-point calculation）在固定核坐标 \(\mathbf R\) 上求电子问题。常见输出的 `total energy` 包括电子能和该几何的核-核排斥；它仍是指定哈密顿量、电子态、方法和基组下的结果。单个总能量不是结合能、反应能、零点校正能或吉布斯自由能。

读单点输出时按顺序找：输入几何与单位 → 电荷/多重度 → HF 或 DFT 及泛函 → 基组和其他近似 → SCF 是否收敛 → 总能量及单位 → 可选的轨道、偶极矩或布居分析。比较两个能量前，先检查它们是否描述相同的物理问题及可比的计算条件。

**可解释边界**：SCF 的最后一次能量变化小，只说明所设迭代条件得到满足；它本身不证明目标电子态正确、波函数稳定或方法误差足够小。

## 5.2 几何优化：寻找驻点，不保证全局最低点

**几何梯度**（energy gradient）是能量对核坐标的一阶导数：

$$
g_{A\alpha}=\frac{\partial E}{\partial R_{A\alpha}}.
$$

**成立条件**：同一电子态、方法和坐标约定下定义可微的势能面。**符号**：\(A\) 是原子，\(\alpha\) 是笛卡尔坐标分量。**数据对应**：优化输出的梯度、最大位移、能量变化和每步几何。**不能推出**：梯度很小不保证该点是最低点。

优化器沿着由能量及梯度定义的**势能面**（potential-energy surface，PES）改变原子坐标；每一步通常要重新求电子态。输出中的“优化完成”首先表示程序自己的终止条件满足。接着需检查最终几何是否合理、约束是否生效、SCF 是否在各关键步收敛，并用频率判断最终结构附近的曲率。多个起始构象可能通向不同的局部极小值。

**输入与输出的区别**：输入坐标是起点；最终坐标是优化结果；轨迹中间的能量属于不同几何，不能把某一步的轨道或密度误配到另一结构。

## 5.3 振动频率：在驻点附近分析能量曲率

**Hessian 矩阵**（Hessian matrix）是能量对核坐标的二阶导数：

$$
H_{A\alpha,B\beta}
=\frac{\partial^2E}
{\partial R_{A\alpha}\partial R_{B\beta}}.
$$

**成立条件**：在指定几何和电子态附近讨论同一势能面，常用谐振近似。**符号**：\(A,B\) 是原子；\(\alpha,\beta\) 是坐标分量。**数据对应**：质量加权 Hessian 的本征值给谐振频率，本征向量给**正常模**（normal mode，即集体振动方向）。**不能推出**：谐振频率不自动等于实验基频，尤其未含非谐性和环境效应时。

非线性 \(N\) 原子分子有 \(3N-6\) 个振动正常模；线性分子有 \(3N-5\) 个。常见频率单位是 \(\mathrm{cm}^{-1}\)。程序报告的负号或 `imaginary` 往往标记负曲率对应的虚频；需结合输出约定和振动方向核对。无明显虚频支持“给定模型下的局部极小值”，不证明全局最低点；一个明显虚频通常表示一阶鞍点候选，也可能暴露结构或数值问题。

**零点振动能**（zero-point energy，ZPE）是谐振近似下零振动量子数仍有的能量。`electronic energy`、`electronic energy + ZPE`、焓和吉布斯自由能使用不同校正及温度/压力约定，不能只比较最后一行数字。IR 强度与频率也不是同一物理量：频率给模式位置，强度反映相应跃迁的响应。

## 5.4 激发态：同一几何上的能级差与跃迁

**激发态**（excited state）是高于所选参考态的电子态。基态 HF/DFT 的一串轨道能量不是激发态能量表。常见的 CIS、TDHF 或**含时密度泛函理论**（TDDFT）需要明确参考态、求解的态数与自旋通道；不同方法对多电子激发和电荷转移态的误差可能差别很大。

**垂直激发能**（vertical excitation energy）是在相同核坐标上，激发态与参考态的能量差 \(\Delta E=E_{\mathrm{exc}}(\mathbf R_0)-E_{\mathrm{ref}}(\mathbf R_0)\)。输出常用 eV，也可能给 \(\mathrm{cm}^{-1}\) 或波长；换算波长前先核实能量单位。**振子强度**（oscillator strength，\(f\)）是无量纲的电偶极跃迁强度指标，不是“激发态电子数”。

读激发态结果要记录：参考态及其收敛情况、计算几何、方法/泛函/基组、态序号、激发能、\(f\)、自旋和态组成。某个态含有多种轨道跃迁贡献，不能把它简单等同于一个 HOMO→LUMO 图像；当几何或方法改变，态序号也可能换位。吸收峰的位置和强度还涉及展宽、振动及环境等条件，此处只学电子跃迁的基本解释。

**本章来源**：[LV, 第 15.10、15.12 节，PDF pp.494、510-511]；[BK01, 第 4.14、6.9、11.9、12.1-12.4 节]；[RV02, PDF pp.1-3、19-22]；[ORCA 6.1 任务与频率说明](https://www.faccts.de/docs/orca/6.1/manual/contents/essentialelements/basics.html)、[PySCF TDDFT 说明](https://pyscf.org/user/tddft.html)。

---

# 第六章：从轨道和密度矩阵到空间物理量

## 6.1 一粒子密度矩阵与电子密度

**一粒子约化密度矩阵**（one-particle reduced density matrix，1-RDM）：去掉多电子波函数中其余电子坐标后，用来计算一电子性质的矩阵/核。

在 AO 基中，空间电子密度为

$$
\rho(\mathbf r)
=\sum_{\mu\nu}P_{\mu\nu}
\chi_\mu(\mathbf r)\chi_\nu^*(\mathbf r).
$$

电子数满足

$$
N_e=\int\rho(\mathbf r)\,d\mathbf r
=\mathrm{Tr}(\mathbf P\mathbf S).
$$

**成立条件**：\(P\)、\(S\) 和 \(\chi\) 使用同一 AO 定义、顺序与归一化；\(P\) 表示所声称的 total density。

**符号**：\(P_{\mu\nu}\) 是 AO 密度矩阵；\(\mathrm{Tr}\) 是矩阵迹。

**数据对应**：`DensityMatrix` + `BasisSet` + 空间点才能评价 \(\rho\)；非正交 AO 中电子数检查必须用 `Tr(PS)`，不是 `Tr(P)`。

**不能推出**：形状正确的方阵不一定是密度矩阵，也不能仅凭矩阵值知道它是 SCF、post-SCF、total 还是 spin。

按定义，总电子密度 \(\rho(\mathbf r)\ge0\)。实际网格若出现极小负值，应先检查浮点误差、插值、AO 顺序、矩阵约定或实际语义，而不能把“负电子密度”当作正常物理区域。

**本节来源**：[SW01, PDF pp.10-11, Eq. 11/14]；[BK02, PDF pp.153,227-228]。

## 6.2 alpha/beta、自旋密度和开放壳层

$$
\begin{aligned}
\mathbf P_{\mathrm{total}}&=\mathbf P_\alpha+\mathbf P_\beta,\\
\mathbf P_{\mathrm{spin}}&=\mathbf P_\alpha-\mathbf P_\beta,\\
\rho_{\mathrm{spin}}(\mathbf r)&=\rho_\alpha(\mathbf r)-\rho_\beta(\mathbf r).
\end{aligned}
$$

**alpha/beta 自旋通道**（alpha/beta spin channels）：非相对论自旋轨道中两个正交自旋分量。

**自旋密度**（spin density）：alpha 与 beta 电子密度之差，可以为正、负或零。

**成立条件**：通道定义和 AO 基一致；总密度与自旋密度使用明确约定。

**符号**：\(P_\alpha,P_\beta\) 分别是两个自旋通道的密度矩阵。

**数据对应**：unrestricted `OrbitalSet` 应保留 `alpha`/`beta`；`DensityMatrix.spin_role` 应区分 `total` 和 `spin`。

**不能推出**：自旋密度负值不是负概率；单个 alpha 通道也不是总电子密度。

**本节来源**：[BK02, PDF pp.227-230]；CH₃ FCHK fixture。

## 6.3 MO、单轨道密度和差分密度

| 量 | 定义 | 符号范围 | 常见单位 | 适合显示 |
|---|---|---:|---|---|
| MO 振幅 | \(\phi_p(\mathbf r)\) | 正/负/零 | bohr⁻³ᐟ² | 正负相位等值面 |
| 单轨道密度 | \(|\phi_p(\mathbf r)|^2\) | 非负 | bohr⁻³ | 非负等值面/体 |
| 总电子密度 | \(\rho(\mathbf r)\) | 非负 | electron/bohr³ | 体、等值面 |
| 自旋密度 | \(\rho_\alpha-\rho_\beta\) | 可正可负 | electron/bohr³ | 正负等值面 |
| 差分密度 | \(\rho_A-\rho_B\) | 可正可负 | electron/bohr³ | 电子重排正负面 |

实数 MO 整体乘以 \(-1\) 不改变平方模和任何单行列式物理状态；因此相位颜色整体交换通常不是错误。差分密度必须记录减法方向、几何、电子数、方法、基组和对齐网格。

**本节来源**：[BK01, PDF pp.124,129]；[SW01, PDF pp.10-11]。

## 6.4 原子部分电荷是分配规则的结果

**原子部分电荷**（atomic partial charge）把分子的电子分布按指定规则归给各原子；它不是在单个原子上直接测到的唯一数值。对全电子计算，若规则给原子 \(A\) 分配 \(N_A\) 个电子，则

$$
q_A=Z_A-N_A,
\qquad \sum_A q_A=Q_{\mathrm{molecule}}.
$$

**成立条件**：电子分配覆盖整个分子且不重复计数，并使用一致的核电荷与电子数约定。**符号**：\(q_A\) 是以元电荷 \(e\) 为单位的原子净电荷，\(Z_A\) 是核电荷数，\(N_A\) 是分配到该原子的电子数。**数据对应**：输出的每原子电荷列表必须连同原子顺序、分析方法、几何、电子态和方法/基组保存；求和应接近输入总电荷。**不能推出**：同一个分子只有一套“真实原子电荷”，或某原子电荷能脱离所用分配规则解释。

| 分配方式 | 从什么数据得到 | 读取时要问 |
|---|---|---|
| Mulliken 等基函数布居 | 密度矩阵与 AO 重叠，按基函数所属原子分配电子 | 基组是否改变了结果；是否使用相同 AO 与重叠约定 |
| Hirshfeld 等实空间分配 | 用原子参考密度或空间权重分配分子电子密度 | 使用哪套参考原子密度、权重和积分设置 |
| ESP 拟合电荷 | 调整原子点电荷，使其近似重现所采样的分子静电势 | 采样区域、总电荷约束和其他拟合约束是什么 |

比较两组电荷前先确认分配方法相同。即使总电荷求和正确，也不能证明每个原子的电荷在不同方法、基组或构象下相同；ESP 拟合电荷只是静电势的简化表示，不等于下一节的连续空间势场。

**本节来源**：[BK01, 第 10.1-10.3 节，PDF pp.340-350，尤其 pp.341、344、347、350]。

## 6.5 静电势

**静电势**（electrostatic potential，ESP）：单位正试探电荷在空间某点感受到的静电势能。

对孤立、全电子、非周期模型，原子单位下常写为

$$
V(\mathbf r)
=\sum_A\frac{Z_A}{|\mathbf r-\mathbf R_A|}
-\int\frac{\rho(\mathbf r')}{|\mathbf r-\mathbf r'|}\,d\mathbf r'.
$$

**成立条件**：孤立库仑边界、全电子核项和明确势零点；ECP、周期体系及溶剂模型需要额外定义。

**符号**：第一项是核贡献；第二项是电子密度贡献；\(\mathbf r'\) 是积分变量。

**数据对应**：ESP 链是 `Structure + BasisSet + DensityMatrix + evaluation points`；不能漏掉用于从 AO 密度矩阵求 \(\rho\) 的 `BasisSet`。

**不能推出**：ESP 不是电子密度、原子部分电荷或 MO；核附近奇点和周期势零点必须单独处理。

**本节来源**：[BK01, PDF pp.113,118，库仑势项]；[SW01, PDF pp.10-11，密度表达]。

---

# 第七章：Grid3D 是有坐标和语义的场

## 7.1 仿射三维网格

**Grid3D**（three-dimensional affine grid）：由数组、原点、三条步向量、单位、物理角色和来源共同定义的三维场。

**仿射网格**（affine grid）：三条网格轴可以倾斜，不要求与 Cartesian x/y/z 平行。

$$
\mathbf r(i,j,k)
=\mathbf o+i\mathbf s_0+j\mathbf s_1+k\mathbf s_2.
$$

**成立条件**：\(\mathbf s_0,\mathbf s_1,\mathbf s_2\) 线性独立；索引范围与数组 shape 一致。

**符号**：\(\mathbf o\) 是 origin；\(\mathbf s_a\) 是 step vector；\(i,j,k\) 是整数网格索引。

**数据对应**：Grid3D 至少保存 `origin`、`step_vectors`、`shape`、`dims`、`coordinate_unit`、`value_unit`、`semantic_role` 和来源。

**不能推出**：只保存三个“步长”或只看数组 shape，不能恢复倾斜轴、空间方向和实际范围。

一轴有 \(N\) 个采样点时，第一个点到最后一个点的跨度是 \((N-1)\mathbf s\)，不是 \(N\mathbf s\)。

**本节依据**：ChemBlender `Grid3D` 模型与 Cube/VASP grid reader fixtures；上式是本教材按项目仿射坐标约定写出的定义式，不冒充教材原式。

## 7.2 体元与网格积分

$$
\Delta V=\left|\det[\mathbf s_0\ \mathbf s_1\ \mathbf s_2]\right|,
$$

$$
\int f(\mathbf r)\,d^3r
\approx\Delta V\sum_{ijk}f_{ijk}.
$$

**成立条件**：简单矩形求积、均匀仿射网格；边界和步长足以解析目标函数。

**符号**：\(\Delta V\) 是每个网格平行六面体体积；行列式使用完整步向量。

**数据对应**：电子密度积分应接近电子数；MO 平方模积分应接近 1；自旋密度积分取决于 \(N_\alpha-N_\beta\)。

**不能推出**：整体积分正确不证明局部场、单位、数据集（dataset）选择或 AO 顺序全部正确。

**本节依据**：ChemBlender Grid3D 验证约定；电子密度与 MO 归一化依据 [SW01, PDF pp.10-11]。

## 7.3 多 dataset、轴序和配准

**dataset**（数据集）：同一容器或同一空间网格中的一个独立数值场。多 dataset Cube 可能具有 `dataset,x,y,z` 维度。

两个场只有在以下条件兼容时才能逐点相减或进行 field-on-surface：

- structure identity；
- origin、三条 step vectors、shape 和轴序；
- coordinate/value units；
- dataset index；
- 物理语义、电子态和计算条件。

**field-on-surface**（表面上取场）：用一个场定义等值面，再在该表面顶点采样另一个场，例如在电子密度表面着色 ESP。几何场与属性场必须分别保存来源。

**本节依据**：ChemBlender wavefunction/grid 设计文档、Cube 多 dataset fixture 与 field-on-surface 工作流。

---

# 第八章：ChemBlender 科学对象与文件边界

## 8.1 核心对象

| 对象 | 必要内容 | 典型来源 |
|---|---|---|
| `Structure` | 原子序数、坐标、单位；可含晶胞/PBC/拓扑 | XYZ、CIF、POSCAR、FCHK |
| `BasisSet` | 中心、壳层、角动量、指数、收缩系数、约定 | FCHK、Molden |
| `OrbitalSet` | 系数、能量、占据、限制性或 alpha/beta 通道 | FCHK、Molden |
| `DensityMatrix` | AO 方阵、`scf/post_scf`、`total/spin` | FCHK 或后端派生 |
| `Grid3D` | 数组、空间映射、单位、物理角色、来源 | Cube 或空间求值 |
| `PropertyDataset` | 具名维度、单位、状态与来源 | cclib/CJSON/QCSchema 等 |

**限制性轨道集**（restricted orbital set）：闭壳层 alpha/beta 共享空间轨道，通道名为 `restricted`。

**非限制性轨道集**（unrestricted orbital set）：保留 `alpha` 与 `beta` 两套空间轨道。

**广义轨道集**（generalized orbital set）：更一般的自旋轨道表示，不能静默压成前两种。

**本节依据**：ChemBlender `cbq_core/model` 科学对象定义，提交快照见 12.2。

## 8.2 文件格式只说明容器能力

| 格式 | 主要能力 | 不能保证 |
|---|---|---|
| XYZ/extXYZ | 坐标；extXYZ 可有 cell、PBC、typed properties | 波函数、方法、基组 |
| Gaussian/ORCA input | 任务、方法、基组、charge/multiplicity、geometry | 计算成功或结果存在 |
| FCHK/Molden | 结构、基组、轨道，部分文件有密度矩阵 | 所有字段齐全或程序约定一致 |
| Cube | 结构、仿射网格和值，可有多 dataset | 值一定是电子密度 |
| QCSchema/CJSON | 结构化输入/结果 envelope | 原始字段都已转成 ChemBlender 实体 |
| CBQ + `.npy` | ChemBlender 科学对象、数组、来源和 View | 原始量化程序的完整私有语义 |

**结果信封**（result envelope）：保留原始 JSON/扩展字段的容器；字段仍在不等于 ChemBlender 已理解其物理语义。

完整格式、真实片段、依赖和损失边界见 [ChemBlender 输入格式图鉴](CHEMBLENDER_INPUT_FORMAT_ATLAS.md)。

**本节依据**：ChemBlender `reader-capability-matrix.json` 与格式文档，提交快照见 12.2。

## 8.3 同一个分子问题在三款软件中怎样表达

以中性、单重态 H₂O 的固定几何 HF/STO-3G 单点计算为例。下面 Gaussian 和 ORCA 片段取自项目示例；PySCF 片段按官方接口写成阅读示意，**没有在本教材中执行或宣称得到相同数值**。STO-3G 是演示基组，不能把这个例子当作准确性推荐。

```text
Gaussian 输入（节选）
#p hf/sto-3g

water

0 1
O  0.000000 0.000000 0.000000
H  0.758602 0.000000 0.504284
H -0.758602 0.000000 0.504284
```

```text
ORCA 输入（节选）
! HF STO-3G TightSCF
* xyz 0 1
O  0.000000 0.000000 0.000000
H  0.758602 0.000000 0.504284
H -0.758602 0.000000 0.504284
*
```

```python
# PySCF 阅读示意：这几行没有在本教材中执行
from pyscf import gto, scf
mol = gto.M(
    atom="O 0 0 0; H 0.758602 0 0.504284; H -0.758602 0 0.504284",
    unit="Angstrom", charge=0, spin=0, basis="sto-3g",
)
mf = scf.RHF(mol)
mf.kernel()
```

| 同一概念 | Gaussian | ORCA | PySCF |
|---|---|---|---|
| 几何 | 电荷/多重度行后的元素与坐标 | `* xyz` 块中的元素与坐标 | `mol.atom`，并用 `unit` 明确单位 |
| 总电荷 | `0 1` 的第一个数 | `* xyz 0 1` 的第一个数 | `charge=0` |
| 自旋输入 | `0 1` 的第二个数是多重度 \(M=1\) | `* xyz 0 1` 的第二个数是 \(M=1\) | `spin=0` 表示 \(N_\alpha-N_\beta=2S\)，不是多重度；此处对应 \(M=1\) |
| 方法/基组 | `hf/sto-3g` | `HF STO-3G` | `scf.RHF` 与 `basis="sto-3g"` |
| 任务 | 路由段没有 `Opt/Freq/TD`，此处作为单点示例 | 未要求 `Opt/Freq`，此处作为单点示例 | `mf.kernel()` 求该固定几何的 SCF 解 |

上表的“同一概念”不表示三款程序的默认阈值、初猜、基组约定或输出格式完全相同；比较数值前须核对各自版本、设置和终止状态。Gaussian/ORCA 输入是文本作业；PySCF 的输入可由 Python 对象和方法调用构成。它们都必须回答“算什么体系、用什么模型、做什么任务”。

**读输出时对应找证据**：Gaussian 日志常见 `SCF Done`，ORCA 日志常见 `FINAL SINGLE POINT ENERGY`；PySCF SCF 对象保存 `converged`、`e_tot`、`mo_energy`、`mo_occ`、`mo_coeff`。这些位置分别提示收敛、总能、轨道能量、占据和系数，**轨道能量不能代替总能量**。示例片段中的软件输入本身不证明计算已运行，更不证明结果可信。

**本节来源**：项目示例 `examples/user-workflows/inputs/gaussian/water.gjf`、`examples/user-workflows/inputs/orca/water.inp`；[ORCA 6.1 坐标输入](https://www.faccts.de/docs/orca/6.1/manual/contents/essentialelements/coordinates.html)、[PySCF 分子输入](https://pyscf.org/user/gto.html)、[PySCF SCF 结果属性](https://pyscf.org/pyscf_api_docs/pyscf.scf.html)。

## 8.4 文本日志、检查点和网格文件各承担什么

| 文件或对象 | 通常先用于回答 | 阅读时的边界 |
|---|---|---|
| 输入文件或 PySCF 对象 | 原本请求什么体系、方法、基组和任务 | 不是计算完成的证据 |
| `.log`/`.out` 或程序标准输出 | 实际运行、收敛、能量、优化步和性质 | 版本与任务影响段落名称；只截最后一行会丢失条件 |
| FCHK、Molden 或程序检查点 | 几何、基组、轨道/系数等结构化结果 | 并非每个文件都含密度矩阵或所有计算条件 |
| Cube 或派生 `Grid3D` | 特定坐标网格上的某个场 | 文件容器不自动确定它是轨道、总密度还是势 |

跨软件迁移时先迁移“物理问题”的描述，再识别各程序字段。把一个输出交给 ChemBlender 之前，仍需确认当前 reader 实际支持哪些字段；格式图鉴中的能力不等于程序本身的全部输出能力。

---

# 第九章：从文件和任务阅读分子结果

## 9.1 H₂O：从 FCHK 到 MO、密度和 ESP

当前水分子 fixture 的已确认字段：

```text
SP        RHF                                                         STO-3G
Number of atoms                            I                3
Charge                                     I                0
Multiplicity                               I                1
Number of electrons                        I               10
Number of alpha electrons                  I                5
Number of beta electrons                   I                5
Number of basis functions                  I                7
SCF Energy                                 R     -7.495929232844363E+01
```

可直接确认：RHF/STO-3G、3 个原子、中性单重态、10 个电子、5 alpha/5 beta、7 个基函数和一个 SCF 能量。不能因此宣称 FCHK 中所有对象都已正确解析。

```text
FCHK
 ├─ 原子序数 + 坐标 ───────────────→ Structure
 ├─ 壳层/中心/指数/收缩系数 ───────→ BasisSet
 ├─ MO 能量/系数/占据 ─────────────→ OrbitalSet
 └─ 密度字段（若存在且身份明确） ──→ DensityMatrix

BasisSet + OrbitalSet + points
  → φp(r) → MO Grid3D

BasisSet + DensityMatrix + points
  → ρ(r) → density Grid3D

Structure + BasisSet + DensityMatrix + points
  → V(r) → ESP Grid3D

科学实体 + Grid3D + provenance
  → CBQ/.npy → Blender View
```

### H₂O 最小验证

1. `C†SC ≈ I`：轨道正交归一。
2. `Tr(PS) ≈ 10`：密度矩阵电子数。
3. \(\int\rho\,dV\approx10\)：有限 Grid3D 的电子数，允许记录截断误差。
4. MO 平方模积分、Grid3D origin/step/shape/unit、有限性和 dataset 选择。
5. ESP 的核附近非有限值、ECP/全电子身份、势零点和单位。

## 9.2 CH₃：开放壳层迁移

对 CH₃ 不沿用“闭壳层水分子”的默认假设。检查：

- charge 与 multiplicity；
- \(N_\alpha,N_\beta\)；
- restricted/unrestricted/ROHF/UHF 身份；
- alpha/beta 轨道和占据；
- total/spin density matrix；
- \(\int\rho_{spin}\,dV\) 与通道电子数差是否一致。

任何文件没有明确给出的项目标为未知，不根据分子名称猜测。

## 9.3 H₂O：单点、优化和频率不能混作同一结果

本节是**任务阅读示意**，没有运行新的 H₂O 优化或频率计算，也不把 9.1 的 FCHK 当作这些任务的输出。给定同一个起始几何，按下面的关系阅读三类作业：

```text
起始 H₂O 几何 + 电荷/自旋 + 方法/基组
  ├─ 单点 → 该起始几何上的电子能量及可请求的轨道/性质
  └─ 优化 → 多步几何与能量 → 最终几何、梯度与收敛判据
                                  └─ 频率 → 该最终几何附近的正常模、频率、ZPE
```

记录结果时为每一个能量、轨道、密度和频率写明对应几何。优化后的能量通常不能与另一计算的“起始几何单点能”直接解释为方法差异，因为几何也变了。若频率作业使用了不同方法、基组或几何，应把差异单独记录，不能自动接成一条严格一致的链。

**独立解释题**：给一份优化日志和一份频率日志，先指出哪一份提供最终核坐标、哪一份提供局部曲率证据；再列出必须核对哪些条件，才能说它们分析的是同一个分子状态。此处不预填答案。

---

# 第十章：科学验证与错误诊断

## 10.1 四层验证

| 层 | 必查内容 | 典型失败 |
|---|---|---|
| 来源 | path、hash、reader/version、参数 | 读错文件或版本 |
| 模型 | 原子、单位、charge/multiplicity、method/basis | 结构对、电子态错 |
| 数值 | dtype、shape、dims、finite、范数/积分 | 轴序、AO 顺序、dataset 错 |
| 生命周期 | CBQ 关系、View 绑定、冷重开 | 只在当前会话“看起来正常” |

## 10.2 高频错误

1. “Cube 就是电子密度”——错；Cube 是网格容器。
2. “MO 负值是负电荷”——错；实 MO 的正负通常表示相位。
3. “自旋密度负值不合理”——错；它是 alpha-beta 差。
4. “两个数组 shape 相同就能相减”——错；还需空间配准、单位和语义一致。
5. “SCF 收敛就得到正确基态”——错；还需稳定性和态身份检查。
6. “渲染成功说明科学结果正确”——错；渲染只证明显示链可运行。
7. “ESP 只需密度矩阵和原子坐标”——不完整；AO 密度矩阵还需 BasisSet 才能在空间求密度。
8. “把极小负密度截成 0 就解决问题”——错；先定位数值误差或语义/约定错误，再决定是否建立有记录的派生数组。

## 10.3 证据边界

| 证据 | 能证明 | 不能证明 |
|---|---|---|
| Parser 成功 | 语法被接受 | 物理映射正确 |
| 数组有限 | 无 NaN/Inf | 单位、坐标和角色正确 |
| 电子数积分合理 | 一个整体不变量合理 | 局部值处处正确 |
| 两后端相近 | 特定条件下输出接近 | 两者独立且都无共同错误 |
| 冷重开成功 | 项目可恢复 | 原始计算科学正确 |

## 10.4 按任务读取“成功”证据

| 任务 | 必须先看 | 仍需要追问 |
|---|---|---|
| 单点能 | 程序终止、SCF 收敛、总能量、几何与方法/基组 | 是否是目标电子态；能量差的参照是否一致 |
| 几何优化 | 每步电子计算、最终梯度/位移/能量判据、约束与最终几何 | 是局部极小、鞍点，还是遇到错误的终止条件 |
| 频率 | 所用几何与方法、Hessian 来源、频率及正常模、质量 | 虚频/近零频率是否可信；热校正的温度、压力和谐振近似是什么 |
| 激发态 | 基态参考、几何、根数、每态能量/强度、自旋 | 根的身份是否随条件变化；所用方法能否描述目标类型的激发 |
| 原子部分电荷 | 分配方法、原子顺序、数值单位和总电荷求和 | 结果是否依赖基组、参考密度或 ESP 拟合区域 |
| 空间场 | 量的生成式、单位、Grid3D 坐标、数据来源 | 积分、范数、核附近行为及显示映射是否合理 |

这张表不是软件给出的“通过证书”，而是读者检查日志和结构化结果的顺序。`normal termination`、`converged`、视觉平滑分别说明程序流程、数值条件或显示状态，不能互相代替。

## 10.5 将误差分层，避免只调一个阈值

1. **物理模型与电子态**：电荷或自旋填错、选错几何构象，后续数值收敛仍可能非常稳定。先核对电子数、态身份和核坐标。
2. **方法近似**：HF 缺少电子相关，近似 DFT 依赖交换相关泛函；某个泛函在某类性质上表现好，不保证激发态、频率或弱相互作用都同样可靠。
3. **表示误差**：有限基组截断了可表示的轨道空间。比较不同基组时须保持任务、电子态和几何条件可比；`STO-3G` 的教学结果不应被解释成收敛基组极限。
4. **数值误差**：SCF、几何优化及 DFT 积分网格各有阈值；这些阈值收紧只处理相应的数值问题，不能修复模型或数据语义错误。
5. **后处理与读取误差**：日志、检查点、Cube、`Grid3D` 可能在单位、轨道排序、网格轴序或状态引用上丢失或混淆信息。需回到权威输出字段和来源链核实。

报告差异时先说明比较的**量**、**单位**和**条件**，再给数值；不要用“两个软件结果不同”直接断定软件错误。可先让两者处理相同几何、电子态、方法与基组，接着比较收敛设置和输出约定，最后再讨论方法自身的不确定性。

**本节来源**：[BK01, 第 5.13、6.11 节及第 12 章]；[LV, 第 15.10-15.12、16.5 节]；[PySCF SCF 稳定性说明](https://pyscf.org/user/scf.html)、[ORCA 6.1 频率说明](https://www.faccts.de/docs/orca/6.1/manual/contents/structurereactivity/frequencies.html)。

---

# 第十一章：按理解证据推进的完整学习路线

这是一条长期路线，阶段长短由学习者的独立解释决定。先完成当前阶段的一份未见过的输入/输出判读，再进入下一阶段；阅读、导师讲解和样例渲染都不单独算作掌握。首次诊断仍是 [LX00](exercises/EXERCISES.md)，不在教材中预填答案。

| 阶段 | 本教材与三本书的阅读入口 | 要解释的输入和输出 | 独立检查点 |
|---|---|---|---|
| A. 体系与薛定谔方程 | 本教材第二章；Levine 第 1、3 章及 §13.1，氢原子直观图像按需查第 6 章 | 原子坐标、单位、电荷、多重度、固定核假设 | 面对新分子，说明电子数与自旋输入，指出几何在单点计算中为何固定 |
| B. HF、基组与 SCF | 本教材第三章；Levine §8.1、§10.5-10.6、§11.1、§15.3-15.4；需要矩阵推导时查 Szabo–Ostlund 第 2 章和 §3.4 | 方法、基组、SCF 记录、轨道系数/占据和总能量 | 能说明近似、为何需要迭代及“收敛”尚未证明什么 |
| C. DFT 与方法边界 | 本教材第四章；Levine §16.5；Jensen 第 5 章、§6.2、§6.5、§6.8 | 泛函、基组、积分网格和 KS 结果 | 给定 HF/DFT 输出，区分共同输入、不同近似和不能直接作出的比较 |
| D. 三类基本计算任务 | 本教材第五章 §5.1-5.3；Levine §15.10-15.12；Jensen §11.9、§12.1-12.4 | 单点能、优化轨迹、最终几何、梯度、频率、正常模与 ZPE | 给两份日志，判断它们是否能构成同一结构的优化→频率证据链 |
| E. 电子分布与可视化 | 本教材第六至九章；Levine §14.1、§15.6-15.8；Jensen 第 10 章、§11.7 | 轨道、总/自旋密度、部分电荷、ESP、网格和文件格式 | 对未见过的场说明生成量、单位、坐标、来源和显示不能证明的内容 |
| F. 独立核验与跨软件迁移 | 本教材 §8.3、§10.4-10.5；Jensen §5.13、§6.11、第 12 章 | Gaussian、ORCA、PySCF 对同一分子任务的输入与结果 | 在另一软件的样例中找出相同物理条件、不同字段约定和结果限制 |
| G. 激发态 | 完成 A–F 后读本教材 §5.4；Jensen §4.14、§6.9；Levine §9.8-9.9 | 基态参考、激发能、态序号、振子强度与跃迁描述 | 不用 HOMO–LUMO 能隙代替实际激发能，并解释一条跃迁数据缺少的条件 |

三本书的分工贯穿各阶段：Levine 给出按量子力学概念递进的主线；Szabo–Ostlund 在多电子波函数、Roothaan 矩阵和 SCF 约定上提供细节；Jensen 把方法、基组、计算任务、输出性质与收敛实例连起来。目录只用于定位，实际判断仍须核对相应正文、程序版本和样例条件。

### 练习卡：关键公式

```text
公式：
成立条件：
每个符号：
输入文件字段：
程序内对象：
输出数组/单位：
不能推出：
来源页码：
```

### 练习卡：未知输入与输出

```text
软件/版本与文件：
计算对象、几何与单位：
电荷、自旋及电子数：
任务、方法、泛函、基组：
结果字段、物理量与单位：
收敛/终止证据：
当前可解释的结论：
模型、数值和文件语义的限制：
若进入 ChemBlender，对应对象与转换损失：
事实/推断/未知：
```

### 五题漏洞审计

1. 定义边界：为什么总电子密度和差分密度的负值解释不同？
2. 因果：为什么 `FC=SCε` 仍要迭代？
3. 反例：给出一个 shape 相同但不能相减的 Grid3D 对。
4. 条件变化：同一个 H₂O 从单点改为优化再做频率，哪些输出必须绑定到新几何？
5. 新场景：一份激发态输出只列轨道跃迁和波长，判断还需要什么信息才能解释跃迁强度与态身份。

---

# 第十二章：资料与页级证据

## 12.1 本教材采用的来源编号

| 编号 | 本地资料 | 本教材使用的页段 |
|---|---|---|
| BK01 | `materials/books/Introduction to Computational Chemistry.pdf` | 目录定位第 4、5、6、10、11、12 章；正文核对 PDF pp.113-118、124、129-131、200、259-260、280、283、287、340-350、391、410 |
| BK02 | `materials/books/Modern quantum chemistry  introduction to advanced electronic structure theory 1.pdf` | PDF pp.58-60、153、157、160、227-230 |
| LV | `materials/books/Quantum Chemistry Ira N_ Levine.pdf`，Ira N. Levine，第七版 | 目录 PDF pp.6-11；正文核对 PDF pp.494、510-511、566-567；其他节按目录定位，阅读时再核对正文 |
| SW01 | `materials/software/GBasis A Python library for evaluating functions, functionals, and integrals expressed with Gaussian basis functions.pdf` | PDF pp.6、10-11 |
| SW04 | `materials/software/Recent developments in the PySCF program package.pdf` | PDF p.5 |
| RV02 | `materials/reviews/Best-Practice DFT Protocols for Basic Molecular Computational Chemistry.pdf` | PDF pp.1-3、7、19-22 |
| CT | [入门 DFT 课程讲义](../入门DFT/入门DFT课程讲义.md) | 课件 1 p.16；课件 2 pp.5、17、46、62-63、68；课件 3 pp.43、47-48；课件 5 pp.11-13、43-45 |

CT 是逐页保真的 OCR/页面图组合。上表涉及的公式页已回看页面图；不要只复制 OCR 文本中的公式。

**软件官方资料**（2026-09-22 核对）：[ORCA 6.1 输入与任务](https://www.faccts.de/docs/orca/6.1/manual/contents/essentialelements/input.html)、[ORCA 坐标](https://www.faccts.de/docs/orca/6.1/manual/contents/essentialelements/coordinates.html)、[ORCA 频率](https://www.faccts.de/docs/orca/6.1/manual/contents/structurereactivity/frequencies.html)、[PySCF 分子输入](https://pyscf.org/user/gto.html)、[PySCF SCF](https://pyscf.org/user/scf.html)、[PySCF 激发态](https://pyscf.org/user/tddft.html)。Gaussian 在本教材以项目内真实输入片段和 FCHK 为例；不同版本的更多专有关键词应查相应版本手册。

## 12.2 ChemBlender 当前依据

本教材和格式图鉴按 ChemBlender 提交 `2e94f90419aab186f522fb3b701746839ee8c5c4` 核对，日期为 2026-09-20。能力可能随项目更新而变化。

- [格式与支持范围](../../../ChemBlender_2_x/docs/user/workflows/formats.md)
- [Reader 能力矩阵](../../../ChemBlender_2_x/docs/quantum-visualization/reader-capability-matrix.json)
- [波函数和网格设计](../../../ChemBlender_2_x/docs/quantum-visualization/plans/wavefunction-and-grids.md)
- `cbq_core/model/arrays.py`、`grids.py`、`wavefunction.py`
- `chemblender_prepare/core/wavefunction_grid.py`
- H₂O FCHK：`examples/scientific-visualization/inputs/wavefunction/water_sto3g_hf_g03.fchk`
- CH₃ FCHK：`examples/scientific-visualization/inputs/wavefunction/ch3_hf_sto3g.fchk`

---

## 附录：快速术语索引

| 缩写 | 全称 | 一句话作用 |
|---|---|---|
| AO | atomic orbital basis function | 展开 MO 的局部基函数 |
| MO | molecular orbital | AO 系数定义的单电子轨道 |
| HF | Hartree-Fock | 单行列式平均场方法 |
| SCF | Self-Consistent Field | 使有效算符和密度自洽的迭代 |
| DFT | Density Functional Theory | 以基态电子密度为基本变量的框架 |
| KS | Kohn-Sham | 用非相互作用参考轨道产生密度 |
| 1-RDM | one-particle reduced density matrix | 计算一电子性质的约化密度对象 |
| ESP | electrostatic potential | 单位正试探电荷的势能 |
| ECP | effective core potential | 用有效势替代部分核与芯电子作用 |
| PES | potential-energy surface | 核坐标变化时的能量函数 |
| ZPE | zero-point energy | 零振动量子数下的振动能量 |
| TDDFT | time-dependent density-functional theory | 求激发能与跃迁性质的一类响应方法 |
| \(f\) | oscillator strength | 无量纲跃迁强度指标 |
| FCHK | formatted checkpoint | Gaussian 格式化波函数/结果文件 |
| CBQ | ChemBlender project sidecar | 保存科学对象、数组、来源和 View 的项目边车 |
