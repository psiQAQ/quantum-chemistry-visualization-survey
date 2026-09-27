# ChemBlender 输入格式图鉴

## 从文件片段判断物理量、单位、计算条件与转换边界

**核对快照**：ChemBlender `2e94f90419aab186f522fb3b701746839ee8c5c4`，2026-09-20

**用途**：配合 [量子化学数据语义核心教材](QUANTUM_CHEMISTRY_DATA_SEMANTICS.md) 查阅，不要求在 12 小时内背诵全部格式

**覆盖原则**：按用户可见格式族编排；同一格式的多个 reader 或可选适配器只写一张卡

> 文件扩展名只帮助选择解析器，不自动证明数据的物理语义。
> 每次导入都要结合文件内容、reader 诊断、单位、来源和当前运行时能力判断。

---

## 1. 怎么读这本图鉴

### 1.1 六个固定问题

1. 文件直接记录的是结构、拓扑、计算设置、波函数、结果还是网格？
2. 坐标、能量和属性采用什么单位；单位是显式字段还是格式约定？
3. 哪些计算条件写在文件中，哪些完全缺失？
4. 当前 ChemBlender reader 会生成哪些科学实体？
5. 哪些内容只保存在源信封或诊断记录中？
6. 导出或转换会丢失哪些信息，如何验证？

### 1.2 图鉴常用术语

**拓扑**（topology）：原子以及它们之间连接关系构成的图；仅有坐标不一定含拓扑。

**具类型属性**（typed property）：同时规定属性名称、数据类型、分量数及作用对象的数据列。

**形式电荷**（formal charge）：按成键记账规则分配给原子的整数电荷，不等于量化计算得到的部分电荷。

**立体化学**（stereochemistry，stereo）：原子或键的空间排列及手性、顺反等标记。

**连接表记录**（connection-table record，CT record）：保存原子表、键表和相关属性的一条化学结构记录。

**结构清洗**（sanitize）：软件检查并补全价态、芳香性等化学规则的处理；失败不等于文件无法逐字读取。

**构象**（conformer）：连接关系相同而三维坐标不同的一组结构。

**占位率**（occupancy）：晶体学位置被指定原子占据的比例。

**原子位移参数**（atomic displacement parameter，ADP）：描述晶体中原子位置热振动或统计分散程度的参数。

**源信封**（source envelope）：为追溯性保留的源文件片段或原始记录包装。

**诊断记录**（diagnostics）：reader 对警告、缺失字段和解析选择的结构化说明。

**笛卡尔坐标**（Cartesian coordinates）：相对固定坐标轴给出的位置；**分数坐标**（fractional coordinates）：以晶胞三条基矢为基底给出的位置。

**数据集**（dataset）：同一容器或网格中的一个独立数值场；**轨迹**（trajectory）：按步或时间排列的一系列结构帧。

### 1.3 当前运行时边界

| reader 家族 | 当前依赖方式 | 说明 |
|---|---|---|
| XYZ、extXYZ、Cube、Gaussian/ORCA input、MOL2、PDB、POSCAR、PQR、CJSON、QCSchema | built-in | 不等于支持格式的全部方言 |
| MOL/SDF/SMILES | RDKit | Windows Release 随附；仍需检查 sanitize 与可表示性 |
| CIF | Gemmi | Windows Release 随附；symmetry/occupancy/ADP 有专门语义 |
| FCHK/Molden | IOData | 可选 wavefunction runtime |
| Gaussian/ORCA output | cclib | 可选 scientific runtime |
| ASE alternative | ASE | 可选；与内置 extXYZ/POSCAR 卡合并说明 |
| VASP grid/vasprun | pymatgen | 可选 scientific runtime |
| phonopy YAML | phonopy | 可选 scientific runtime |

**F0** 表示没有 Project Browser exporter；**F4** 表示有受控但明显有损的导出；**F5** 表示具备成熟预览/损失声明流程。导出成熟度不等于科学内容“绝对正确”。

---

# 第一类：分子与原子结构

## 2. XYZ 与 extXYZ

真实 XYZ 片段：

```text
3
water
O 0.000000 0.000000 0.000000
H 0.758602 0.000000 0.504284
H -0.758602 0.000000 0.504284
```

真实 extXYZ 片段：

```text
1
Lattice="4 0 0 0 4 0 0 0 4" Properties=species:S:1:pos:R:3 pbc="T T T"
C 0 0 0
```

| 项 | XYZ | extXYZ |
|---|---|---|
| 物理量 | 元素、Cartesian 坐标；重复 block 可作 frames | frames、cell/PBC、`Properties` schema、逐原子/逐帧属性 |
| 单位/坐标 | 通常按 Å 解释，但基础 XYZ 自身没有可靠单位字段 | 坐标通常为 Å；具体属性单位仍需 metadata/来源 |
| 计算条件 | 通常无 | 可有 metadata，但不保证 method/basis/state |
| ChemBlender | `Structure`、trajectory；extXYZ 还有 typed properties 和 masks | 内置 XYZ/extXYZ；ASE 是可选替代路径 |
| 损失/歧义 | 无 topology、cell、PBC、typed property | 不支持 metadata 或缺失值会归一化/省略 |
| 验证 | 原子数、帧数、元素、坐标单位 | 再查 `Lattice`、`pbc`、schema 列数和属性单位 |

**导出**：XYZ F4，只写单个 Structure 坐标；extXYZ F5。

**Fixtures**：`examples/user-workflows/inputs/xyz/water.xyz`、`examples/user-workflows/inputs/extxyz/carbon-trajectory.extxyz`。

## 3. MOL V2000/V3000

```text
water V2000
ChemBlender

  3  2  0  0  0  0  0  0  0  0  0 V2000
    0.0000    0.0000    0.0000 O   0  0  0  0  0  0  0  0  0  0  0  0
    0.7586    0.0000    0.5043 H   0  0  0  0  0  0  0  0  0  0  0  0
   -0.7586    0.0000    0.5043 H   0  0  0  0  0  0  0  0  0  0  0  0
  1  2  1  0  0  0  0
  1  3  1  0  0  0  0
M  END
```

```text
M  V30 BEGIN CTAB
M  V30 COUNTS 3 2 0 0 0
M  V30 BEGIN ATOM
M  V30 1 O 0.0000 0.0000 0.0000 0
M  V30 2 H 0.7586 0.0000 0.5043 0
M  V30 3 H -0.7586 0.0000 0.5043 0
M  V30 END ATOM
M  V30 BEGIN BOND
M  V30 1 1 1 2
M  V30 2 1 1 3
M  V30 END BOND
M  V30 END CTAB
M  END
```

| 项 | 说明 |
|---|---|
| 物理量 | atoms、bonds、2D/3D coordinates、formal charge、isotope、stereo、CT record |
| 单位/坐标 | 坐标通常按 Å 的 MDL 约定解释；文件不保存量化计算方法 |
| 计算条件 | 可记录结构维度、价态和属性，但通常没有 method、basis、电子态或收敛条件 |
| ChemBlender | `Structure`、`Topology`、`AtomicIdentity`、`MolecularRecord`；RDKit sanitize 结果单独记录 |
| 缺失/歧义 | 未知 CT 字段、sanitize 失败、V2000/V3000 表达能力差异 |
| 转换损失 | 目标版本不支持的字段进入 preview；不能把 2D 坐标当实验 3D 几何 |
| 验证 | atom/bond counts、形式电荷、stereo、isotope、坐标维度和 sanitize diagnostics |

**导出**：MOL F5。

**Fixtures**：`examples/user-workflows/inputs/mol/water-v2000.mol`、`examples/user-workflows/inputs/mol/water-v3000.mol`。

## 4. SDF

```text
water V2000
ChemBlender

  3  2  0  0  0  0  0  0  0  0  0 V2000
...
M  END
>  <State>  (1)
solid

>  <Flag>  (1)
true

$$$$
```

| 项 | 说明 |
|---|---|
| 物理量 | 一个或多个 MOL record、coordinates、topology、record properties |
| 单位/坐标 | 坐标沿用 MOL 约定；property 是文本字段，单位必须由字段定义或来源说明 |
| 计算条件 | 自定义 property 可以携带条件，但 SDF 不强制统一的 method/basis/state 字段 |
| ChemBlender | 多 `Structure`、`Topology`、`MolecularRecord`；record property 为 partial support |
| 缺失/歧义 | 坏 record、属性顺序、多个 conformer 是否属于同一分子 |
| 转换损失 | 目标不表示的 properties 和 malformed records 会在 preview/diagnostics 中出现 |
| 验证 | record 数、每条 atom/bond count、property 原值、坏记录隔离策略 |

**导出**：SDF F5。

**Fixture**：`examples/user-workflows/inputs/sdf/mixed-properties.sdf`。

## 5. SMILES

```text
CCO
```

| 项 | 说明 |
|---|---|
| 物理量 | molecular graph、bond order、formal charge、isotope、stereo |
| 单位/坐标 | 通常没有三维坐标，也没有坐标单位 |
| 计算条件 | 普通 SMILES 不保存量子化学方法、基组、电子态或构象生成条件 |
| ChemBlender | `AtomicIdentity`、`Topology`、`MolecularRecord`；确定性生成的平面 2D `Structure` 是派生数据 |
| 缺失/歧义 | 质子化状态、互变异构、构象、计算方法和真实几何均不能从普通字符串确定 |
| 转换损失 | 导出会省略 coordinates/conformer/title/properties；canonicalization 可能改变原子顺序 |
| 验证 | 图连接、形式电荷、立体标记、isomeric 模式和派生几何 provenance |

**导出**：SMILES F5。

**Fixture**：`examples/user-workflows/inputs/smiles/ethanol.smi`。

## 6. MOL2

```text
@<TRIPOS>MOLECULE
two residues
3 2 2 0 0
BIOPOLYMER
GASTEIGER
@<TRIPOS>ATOM
11 N1 0.0000 0.0000 0.0000 N.am 5 RES_A -0.3000
@<TRIPOS>BOND
80 11 22 am
@<TRIPOS>SUBSTRUCTURE
5 RES_A 11 RESIDUE 0 A ALA 1 ROOT
```

| 项 | 说明 |
|---|---|
| 物理量 | atom/bond types、coordinates、partial charge、substructure、多 molecule |
| 单位/坐标 | 坐标通常按 Å；partial charge 通常按 elementary charge，但需要 charge model 名称 |
| 计算条件 | molecule header 可写 charge model；通常不完整保存量子化学 method/basis/state |
| ChemBlender | 多 `Structure`、`Topology`、atomic property、Tripos metadata、substructure |
| 缺失/歧义 | Tripos atom type/bond type 是化学语义，不等同量子化学 AO/MO；未知 sections 只保留或诊断 |
| 转换损失 | normalized export 不复刻原排版，未知 sections/字段可能省略 |
| 验证 | section counts、非连续 atom IDs、bond references、charge 总和、substructure membership |

**导出**：normalized MOL2 F5。

**Fixture**：`examples/user-workflows/inputs/mol2/substructure.mol2`。

## 7. PDB

```text
MODEL        1
ATOM      1  CA AALA A   1      11.000  12.000  13.000  0.60 20.00           C
ATOM      2  CA BALA A   1      11.100  12.100  13.100  0.40 21.00           C
ENDMDL
MODEL        2
...
ENDMDL
```

| 项 | 说明 |
|---|---|
| 物理量 | atom identity、chain/residue、coordinates、MODEL、altloc、occupancy、B-factor、CONECT、CRYST1 |
| 单位/坐标 | Cartesian Å；B-factor 通常 Å²；occupancy 无量纲 |
| 计算条件 | 可有实验模型、晶胞和备注；通常没有完整量子化学方法、基组与电子态 |
| ChemBlender | `Structure`、`BiologicalHierarchy`、atomic properties；兼容 MODEL 可成 trajectory；crystal/topology partial |
| 缺失/歧义 | altloc 选择、缺失氢、元素列、CONECT 不完整、MODEL 原子身份不一致 |
| 转换损失 | unsupported records 和 normalized columns 进入 preview |
| 验证 | fixed columns、chain/residue IDs、altloc occupancy、MODEL identity、CONECT 和 CRYST1 |

**导出**：PDB F5。

**Fixture**：`examples/user-workflows/inputs/pdb/model-trajectory.pdb`。

## 8. PQR

```text
ATOM 1 N ARG A 1A 1.000 2.000 3.000 -0.3000 1.5500
HETATM 2 O HOH W 2 4.000 5.000 6.000 -0.5500 1.4000
```

| 项 | 说明 |
|---|---|
| 物理量 | PDB-like identity/hierarchy、coordinates、partial charge、radius |
| 单位/坐标 | Cartesian Å；charge 常为 elementary charge；radius 常为 Å |
| 计算条件 | charge/radius 依赖质子化、力场或连续介质准备流程，文件本身通常不写全 |
| ChemBlender | `Structure`、`AtomicIdentity`、`BiologicalHierarchy`、charge/radius `AtomicProperty` |
| 缺失/歧义 | 通常没有标准 bond records；charge/radius 取决于准备流程和参数化模型 |
| 转换损失 | topology 和其他 PDB annotations 不在 PQR 表达范围 |
| 验证 | 每原子 charge/radius、总电荷、坐标列、chain/residue 解析 |

**导出**：PQR F5。

**Fixture**：`examples/user-workflows/inputs/pqr/with-chain.pqr`。

---

# 第二类：周期结构

## 9. CIF

```text
data_nacl
_cell_length_a 5.6402
_cell_length_b 5.6402
_cell_length_c 5.6402
_space_group_name_H-M_alt 'F m -3 m'
loop_
_atom_site_type_symbol
_atom_site_fract_x
_atom_site_fract_y
_atom_site_fract_z
_atom_site_occupancy
Na 0.0 0.0 0.0 1.0
Cl 0.5 0.5 0.5 1.0
```

| 项 | 说明 |
|---|---|
| 物理量 | cell、fractional sites、occupancy、symmetry、ADP、data blocks、tags |
| 单位/坐标 | cell lengths 常为 Å、angles 为 degree、site coordinates 为 fractional、occupancy 无量纲 |
| 计算条件 | CIF tags 可携带实验温度、精修和来源；通常没有统一的电子结构 method/basis/state |
| ChemBlender | periodic `Structure`、crystal/site metadata、`CIFEnvelope` |
| 缺失/歧义 | declared 与 derived symmetry、disorder、partial occupancy、ADP 类型、多 block 选择 |
| 转换损失 | normalized 输出可能改变 block/tags/排版；symmetry/occupancy/ADP 修改需逐项预览 |
| 验证 | cell 非退化、fractional sites、occupancy、space group、block 和原始 tag 保留 |

**依赖/导出**：Gemmi；CIF F5，支持 Preserve/Normalized。

**Fixture**：`examples/user-workflows/inputs/cif/nacl.cif`。

## 10. POSCAR / CONTCAR

```text
Silicon diamond
1.0
0.000000000 2.715000000 2.715000000
2.715000000 0.000000000 2.715000000
2.715000000 2.715000000 0.000000000
Si
2
Direct
0.000000000 0.000000000 0.000000000
0.250000000 0.250000000 0.250000000
```

| 项 | 说明 |
|---|---|
| 物理量 | lattice、species/count、Direct/Cartesian coordinates、Selective Dynamics、velocity |
| 单位/坐标 | scale 和 Cartesian 坐标通常对应 Å；Direct 是 fractional；负 scale 有特殊体积语义 |
| 计算条件 | 只含结构与约束；INCAR、KPOINTS、POTCAR 和计算历史不在 POSCAR/CONTCAR 内 |
| ChemBlender | periodic `Structure`、crystal、Selective Dynamics、受支持 velocity property |
| 缺失/歧义 | 一般 occupancy、ADP 和声明 symmetry 不在格式中；VASP 4 元素身份可能依赖外部上下文 |
| 转换损失 | scale、coordinate mode、constraints 和 velocity policy 必须预览 |
| 验证 | determinant/cell、species counts、Direct→Cartesian、constraints、velocity units |

**导出**：内置 POSCAR/CONTCAR F5；ASE 是可选替代 reader，不另成格式。

**Fixture**：`examples/user-workflows/inputs/poscar/si.POSCAR`。

---

# 第三类：计算设置与结果信封

## 11. Gaussian / ORCA 输入

```text
%chk=water.chk
#p hf/sto-3g

water

0 1
O 0.000000 0.000000 0.000000
H 0.758602 0.000000 0.504284
H -0.758602 0.000000 0.504284
```

```text
! HF STO-3G TightSCF

* xyz 0 1
O 0.000000 0.000000 0.000000
H 0.758602 0.000000 0.504284
H -0.758602 0.000000 0.504284
*
```

| 项 | 说明 |
|---|---|
| 物理量/设置 | task keywords、method、basis、charge/multiplicity、embedded geometry、SCF/integration settings |
| 单位/坐标 | 示例为 Cartesian Å；实际程序允许其他语法，必须看输入指令 |
| 计算条件 | route/keyword 行与相邻设置块共同定义任务；不能只读 method/basis 两个词 |
| ChemBlender | 当前内置 reader 只接受严格内嵌 Cartesian `Structure` 和明确 charge/multiplicity |
| 拒绝范围 | Gaussian Z-matrix/freeze/ONIOM/fragments/Link1/multiple blocks；ORCA xyzfile/internal/multiple blocks/额外坐标列 |
| 转换损失 | F0，没有内置导出；当前 reader 只标准化受支持结构，不宣称可往返全部输入指令 |
| 不能保证 | 输入存在不代表作业运行、SCF 收敛或结果场存在 |
| 验证 | route/keywords、method/basis、charge/multiplicity、坐标单位和拒绝诊断 |

**导出**：F0。

**Fixtures**：`examples/user-workflows/inputs/gaussian/water.gjf`、`examples/user-workflows/inputs/orca/water.inp`。

## 12. Gaussian / ORCA `.log` / `.out`

```text
SCF Done:  E(RB3LYP) =  -382.308266602 A.U. after 1 cycles
Frequencies --- 53.1981 84.7415 149.4005 ...
```

```text
FINAL SINGLE POINT ENERGY      -382.055108614160
VIBRATIONAL FREQUENCIES
****ORCA TERMINATED NORMALLY****
```

| 项 | 说明 |
|---|---|
| 物理量 | structures/trajectory、energies、gradients、vibrations、excited states、atomic properties |
| 单位/坐标 | 程序各段可能使用 hartree、eV、cm⁻¹、Å/bohr；必须随字段保存 |
| 计算条件 | 输出可含程序版本、method/basis、收敛状态和 job type，但不同版本/任务差异很大 |
| ChemBlender | cclib reader：structure、energy、atomic property、trajectory、vibration、excited state |
| 缺失/歧义 | “normal termination”不等于目标态正确；不支持的程序段不能因文件可读而宣称已解析 |
| 转换损失 | F0，没有内置导出；标准化对象不会保留任意程序输出的全部文本与私有段落 |
| 验证 | parser metadata、final geometry、energy unit、termination、SCF/optimization convergence、mode/eigenvector shapes |

**依赖/导出**：cclib optional runtime；F0。

**Fixtures**：`examples/scientific-visualization/inputs/cclib/Gaussian/basicGaussian16/dvb_ir.out`、`examples/scientific-visualization/inputs/cclib/ORCA/basicORCA5.0/dvb_ir.out`。

## 13. QCSchema

```json
{
  "schema_name": "qcschema_atomic_result",
  "schema_version": 2,
  "input_data": {
    "molecule": {
      "symbols": ["H", "H"],
      "geometry": [0.0, 0.0, -0.7, 0.0, 0.0, 0.7],
      "molecular_charge": 0.0,
      "molecular_multiplicity": 1
    },
    "specification": {
      "driver": "gradient",
      "model": {"method": "B3LYP", "basis": "def2-svp"}
    }
  }
}
```

| 项 | 说明 |
|---|---|
| 物理量 | Molecule、AtomicInput/AtomicResult、model、driver、properties、return_result、provenance、native/raw fields |
| 单位/坐标 | QCSchema Molecule geometry 默认按 schema 的 bohr 语义；return_result 单位由 driver/schema 定义 |
| 计算条件 | `specification.model`、driver、keywords、protocols 和 provenance 按实际 schema 对象读取 |
| ChemBlender | `Structure`；`CalculationRecord`、energy/gradient 为 partial；完整 `QCSchemaEnvelope` 保留 |
| 缺失/歧义 | envelope 保留字段不等于字段已标准化成可计算实体 |
| 转换损失 | UI 不编辑/重建任意 QCSchema result；core source-envelope exporter F5 |
| 验证 | schema name/version、driver、model、result shape/unit、success/error、provenance |

**Fixture**：`examples/user-workflows/inputs/qcschema/atomic-result.json`。

## 14. CJSON

```json
{
  "chemicalJson": 1,
  "name": "Water result fixture",
  "atoms": {
    "elements": {"number": [8, 1, 1]},
    "coords": {"3d": [0.0, 0.0, 0.0, 0.7586, 0.0, 0.5043, -0.7586, 0.0, 0.5043]},
    "partialCharges": {"Mulliken": [-0.64, 0.32, 0.32]}
  }
}
```

| 项 | 说明 |
|---|---|
| 物理量 | structure、bonds、properties、trajectory、vibration、spectrum、excited state、grid/orbital envelope |
| 单位/坐标 | 取决于字段和来源；没有显式单位时不得自行补成确定事实 |
| 计算条件 | `properties` 或扩展字段可携带 method、charge/multiplicity；生产者并不保证字段集合一致 |
| ChemBlender | `Structure`、`Topology`、AtomicIdentity/Property；部分 result types 为 partial；完整 `CJSONEnvelope` 保留 |
| 缺失/歧义 | 不同生产者使用的扩展字段和单位可能不同 |
| 转换损失 | core controlled-envelope exporter F5；Project Browser 没有通用 writer |
| 验证 | schema/version、数组长度、坐标/属性单位、envelope 与已标准化实体的边界 |

**Fixture**：`examples/user-workflows/inputs/cjson/water-results.cjson`。

---

# 第四类：波函数与三维场

## 15. FCHK / FCH

```text
SP        RHF                                                         STO-3G
Number of atoms                            I                3
Charge                                     I                0
Multiplicity                               I                1
Number of electrons                        I               10
Number of basis functions                  I                7
Current cartesian coordinates              R   N=           9
...
```

| 项 | 说明 |
|---|---|
| 物理量 | structure、nuclear charges、basis shells/primitives、MO energies/coefficients、occupations、可选 density matrices |
| 单位/坐标 | Gaussian formatted checkpoint 通常采用原子单位：coordinates 为 bohr、energies 为 hartree |
| 计算条件 | title/header 可给 method/basis/run type；程序版本和更完整设置不一定存在 |
| ChemBlender | IOData reader → `Structure`、`BasisSet`、`OrbitalSet`、`DensityMatrix`、atomic properties |
| 缺失/歧义 | 文件可能缺 density；pure/Cartesian、AO order、normalization、SCF/post-SCF density 身份必须核对 |
| 转换损失 | F0，没有内置导出；转成 Grid3D/CBQ 后不等于保留原始 checkpoint 的全部字段 |
| 验证 | atom/electron/basis counts、channel occupations、coefficient shapes、`C†SC`、`Tr(PS)`、source hash |

**依赖/导出**：IOData optional runtime；F0。

**Fixture**：`examples/scientific-visualization/inputs/wavefunction/water_sto3g_hf_g03.fchk`。

## 16. Molden / `.input`

```text
[Molden Format]
[Atoms] AU
O 1 8 0.0000000000 0.1582268966 0.1582268966
[GTO]
1 0
s 6 1.0
8588.5000000000 1.2050128859
...
[MO]
```

| 项 | 说明 |
|---|---|
| 物理量 | atoms、basis shells、MO energies/spins/occupations/coefficients |
| 单位/坐标 | `[Atoms] AU` 表示 bohr；`Angs` 表示 Å；能量和系数按 Molden/生产者约定 |
| 计算条件 | 通常不足以完整保存 functional、SCF thresholds 或程序所有设置 |
| ChemBlender | 与 FCHK 共用 IOData wavefunction reader，生成 Structure/BasisSet/OrbitalSet 等 |
| 缺失/歧义 | 程序间 pure/Cartesian 标记、AO ordering、normalization、spin 标签有历史差异 |
| 转换损失 | F0，没有内置导出；跨程序转换最易丢失 AO 约定、自旋标签和生产者私有段落 |
| 验证 | section 完整性、原子单位、shell counts、MO coefficient count、occupations 和 alpha/beta labels |

**依赖/导出**：IOData optional runtime；F0。

**Fixture**：`examples/scientific-visualization/inputs/wavefunction/h2o.molden.input`。

## 17. Cube

```text
two datasets
orbital identifiers without inferred semantics
   -1    0.100000    0.200000    0.300000
   -2    1.000000    0.000000    0.000000
    2    0.000000    1.000000    0.000000
    1    0.000000    0.000000    1.000000
    2    5    7
 10.0 100.0 11.0 101.0 ...
```

| 项 | 说明 |
|---|---|
| 物理量 | atom coordinates、grid origin/axes/counts、voxel values、可选多 dataset IDs |
| 单位/坐标 | 轴 count 符号和生产者约定影响 bohr/Å；value unit/semantic 往往需外部证据 |
| 计算条件 | 通常不完整；comment、生成命令和配套 metadata 很重要 |
| ChemBlender | `Structure`、`Grid3D`、atomic properties、多 dataset values |
| 缺失/歧义 | 不能从 `.cube` 或文件名断定 density/MO/ESP；多 dataset 必须选 index |
| 转换损失 | Cube F5，一次导出一个明确 dataset；语义 round-trip，不保证源字节相同 |
| 验证 | atom/grid counts、origin/step/shape/unit、dataset IDs、值总数、finite 和适用积分 |

**Fixture**：`examples/user-workflows/inputs/cube/two-datasets.cube`。

---

# 第五类：VASP、能带与声子

## 18. CHGCAR / PARCHG / ELFCAR / LOCPOT

```text
unknown system
1.00000000000000
  2.969072 -0.000523 -0.000907
 -0.987305  2.800110  0.000907
 -0.987305 -1.402326  2.423654
Li
1
Direct
0.000000 0.000000 0.000000

32 32 32
0.44062142953E+00 0.44635237036E+00 ...
```

| 文件 | 主要场 | 值语义注意事项 |
|---|---|---|
| CHGCAR | charge-related density | 原始归一化与单位按 VASP/pymatgen 约定解释，必须做积分检查 |
| PARCHG | partial/band-decomposed charge density | 依赖选定 band/k-point/spin 条件 |
| ELFCAR | electron localization function | 通常为无量纲派生指标 |
| LOCPOT | local potential | 通常以 eV 表示，但势零点和分量需说明 |

| 项 | 说明 |
|---|---|
| 物理量 | 周期电荷相关密度、部分电荷密度、ELF 或局域势；具体角色由格式族和元数据共同确定 |
| 空间 | POSCAR-like cell/structure + 周期三维网格；网格轴随晶格而非固定 Cartesian 盒 |
| 单位/坐标 | 晶胞通常为 Å；各场的值单位、归一化和势零点按文件类型与 VASP/pymatgen 约定解释 |
| 计算条件 | ENCUT、k mesh、spin、band/k-point 选择和生成任务不一定写全，必须连接配套输入/输出 |
| ChemBlender | pymatgen reader → crystal `Structure` + `Grid3D` |
| 缺失/歧义 | extension/basename 不是全部物理条件；spin/dataset、归一化、势零点仍需 metadata |
| 转换损失 | F0，没有内置导出；转换成通用 Grid3D 时必须另存 VASP 场角色与归一化证据 |
| 验证 | cell、grid dimensions、dataset/spin、value unit、周期积分和 pymatgen 解析结果 |

**依赖/导出**：pymatgen optional runtime；F0。

**Fixture**：`examples/tutorials/2.5.0/inputs/li-chgcar/CHGCAR`。

## 19. `vasprun.xml` / `.gz`

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<modeling>
 <generator>
  <i name="program" type="string">vasp</i>
  <i name="version" type="string">5.2.11</i>
 </generator>
 <incar>
  <i type="int" name="ISPIN">2</i>
  <i name="ENCUT">520.00000000</i>
  <i type="int" name="NBANDS">13</i>
 </incar>
 <kpoints>...</kpoints>
</modeling>
```

| 项 | 说明 |
|---|---|
| 物理量 | structure、k points、eigenvalues/bands、DOS、Fermi energy、可选 projections |
| 单位/坐标 | band energies 常为 eV；k 点通常为 reciprocal fractional coordinates；投影和 occupations 需按字段定义 |
| 计算条件 | VASP version、INCAR、k mesh/path、spin、smearing、ENCUT、NBANDS 等 |
| ChemBlender | pymatgen reader → `BandStructure`、DOS、Structure；projection 为 partial |
| 缺失/歧义 | `.xml/.gz` 扩展名本身不够，reader 仍需确认是 vasprun；不同 calculation stages 可能共存 |
| 转换损失 | F0，没有内置导出；标准化 band/DOS 对象不等于保留完整 XML 计算历史和所有投影 |
| 验证 | program/version、k-point count/path、spin channels、energy reference、Fermi energy、band/DOS shapes |

**依赖/导出**：pymatgen optional runtime；F0。

**Fixtures**：`examples/scientific-visualization/inputs/silicon/bands/vasprun.xml.gz`、`examples/scientific-visualization/inputs/silicon/dos/vasprun.xml.gz`。

## 20. phonopy YAML / FORCE_SETS

```yaml
phonopy:
  version: 2.7.0
  frequency_unit_conversion_factor: 15.633302
physical_unit:
  atomic_mass: AMU
  length: angstrom
  force_constants: eV/angstrom^2
supercell_matrix:
- [2, 0, 0]
- [0, 2, 0]
- [0, 0, 2]
primitive_cell:
  lattice: ...
  points: ...
```

| 项 | 说明 |
|---|---|
| 物理量 | primitive/supercell、displacements、forces、masses、force constants、phonon modes |
| 单位/坐标 | YAML 可显式给 length/mass/force-constant unit；频率还依赖 conversion factor |
| 计算条件 | phonopy version、symmetry tolerance、primitive axes、supercell matrix |
| ChemBlender | phonopy reader → `Structure` + phonon mode data |
| 缺失/歧义 | 单独 YAML 可能依赖 FORCE_SETS/BORN/POSCAR；eigenvector normalization 和 phase 需保留 |
| 转换损失 | F0，没有内置导出；缺少配套文件时不能重建完整力常数与非解析项设置 |
| 验证 | structure identity、supercell mapping、atom/mode counts、frequency unit、displacement/force shapes |

**依赖/导出**：phonopy optional runtime；F0。

**Fixture**：`examples/scientific-visualization/inputs/phonopy/NaCl/phonopy_disp.yaml`。

---

# 第六类：ChemBlender 项目持久化

## 21. CBQ 与 `.npy`

```json
{
  "format": "chemblender.cbq",
  "manifest_version": "1.0",
  "project": {
    "datasets": {
      "semantic_role": "electron_density",
      "coordinate_unit": "bohr",
      "origin": [-5.9075, -5.9275, -6.7025],
      "step_vectors": [[0.25, 0, 0], [0, 0.25, 0], [0, 0, 0.25]],
      "data": {
        "unit": "electron_per_cubic_bohr",
        "path": "arrays/0095d7....npy",
        "shape": [56, 49, 60]
      }
    }
  }
}
```

| 项 | 说明 |
|---|---|
| 物理量 | 标准化 Structure/BasisSet/OrbitalSet/DensityMatrix/Grid3D/Property、来源、revision、View 关系 |
| `.npy` | 保存 dtype、shape 和数组值；hash 与路径在 manifest 中引用 |
| 单位/坐标 | 由 `ArrayData.unit`、dims、Grid3D origin/step/coordinate_unit 显式给出 |
| 计算条件 | provenance parent IDs、producer/version、operation 和 parameters 记录派生链 |
| ChemBlender | 项目持久化层按 ID 恢复科学对象、数组与 View 绑定；不是外部量化程序 reader |
| 缺失/歧义 | 单独复制 `.npy` 会丢失语义；CBQ 也不必复制量化程序所有私有字段 |
| 转换损失 | 保存/重开应保持 CBQ 语义；回写原始 FCHK、CIF 或程序输出不属于该格式承诺 |
| 验证 | manifest/schema、content/file hash、ID 引用、array shape/dtype、cold reopen、缓存删除后重建 |

**Fixture**：`examples/scientific-visualization/output/molecular/scenes/water.cbq/manifest.json`。

## 22. `.blend` 不是量子化学交换格式

`.blend` 保存 Blender 场景、对象、材质和插件属性。ChemBlender 2.1/2.2 旧工程走显式 Legacy Migration：detect、preview、diagnostics、backup、migration、save/reopen。

- 不把 `.blend` 列入 Reader F0-F5 交换格式矩阵。
- 不从显示对象反推权威科学数组。
- `.blend` 与 `.cbq` 应作为场景和科学项目的配对生命周期检查。
- 迁移技术检查不等于独立人工科学验收。

---

# 第七类：覆盖与选择检查

## 23. 22 个 reader 条目的覆盖映射

| reader_id | 本图鉴条目 |
|---|---|
| `ase-structure` | 2 XYZ/extXYZ、10 POSCAR；可选替代路径 |
| `cclib_output` | 12 Gaussian/ORCA output |
| `cif` | 9 CIF |
| `cjson` | 14 CJSON |
| `cube` | 17 Cube |
| `extxyz` | 2 XYZ/extXYZ |
| `gaussian-input` | 11 Gaussian/ORCA input |
| `iodata_wavefunction` | 15 FCHK、16 Molden |
| `mol` | 3 MOL |
| `mol-v2000` | 3 MOL；兼容 reader，不重复成卡 |
| `mol2` | 6 MOL2 |
| `orca-input` | 11 Gaussian/ORCA input |
| `pdb` | 7 PDB |
| `phonopy-file` | 20 phonopy |
| `poscar` | 10 POSCAR/CONTCAR |
| `pqr` | 8 PQR |
| `pymatgen-vasp-grid` | 18 VASP grids |
| `pymatgen-vasprun-electronic` | 19 vasprun |
| `qcschema` | 13 QCSchema |
| `sdf` | 4 SDF |
| `smiles` | 5 SMILES |
| `xyz` | 2 XYZ/extXYZ |

## 24. 快速选择规则

- 只交换坐标且接受无 topology/cell：XYZ；需要 frames、cell/PBC 或 typed property：extXYZ。
- 分子 graph、stereo、records/properties：MOL/SDF；只有 graph 表达式：SMILES。
- 晶体需要 symmetry/occupancy/ADP：CIF；面向 VASP 结构和约束/velocity：POSCAR/CONTCAR。
- 生物 hierarchy：PDB；明确 partial charge/radius：PQR。
- AO/MO/密度矩阵：FCHK/Molden；已经采样的规则三维场：Cube 或 VASP grid。
- 量化程序结果：cclib output、QCSchema 或 CJSON；先看已标准化能力，不把 envelope 当全部支持。
- ChemBlender 项目恢复：CBQ + `.npy`；`.blend` 只承担 Blender 场景生命周期。

## 25. 通用导入验收卡

```text
源文件与 SHA-256：
reader_id / reader_version / dependency version：
格式族与真实语法片段：
Structure / Topology / BasisSet / OrbitalSet / DensityMatrix / Grid3D：
坐标系与 coordinate unit：
value unit 与 semantic role：
method / basis / charge / multiplicity / boundary：
dataset / frame / spin / k-point / mode index：
保留在 envelope 的字段：
明确未支持或被拒绝的字段：
导出损失和 preview 决策：
至少两个数值/结构检查：
事实 / 推断 / 未知：
```

---

## 26. 当前来源

- ChemBlender `docs/user/workflows/formats.md`
- ChemBlender `docs/quantum-visualization/reader-capability-matrix.json`
- 本图鉴引用的 `examples/user-workflows/inputs/` 与 `examples/scientific-visualization/inputs/` fixtures
- [量子化学数据语义核心教材](QUANTUM_CHEMISTRY_DATA_SEMANTICS.md)

格式支持会随代码演进；使用本图鉴前应把提交号与当前工作区重新核对。
