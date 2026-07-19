# neese_software_2025

BibTeX key：`neese_software_2025`

## 摘要

ABSTRACT Version 6.0 of the ORCA quantum chemistry program suite was released in July 2024. ORCA 6.0 is a major turning point in the history of the program since it represents a near complete rewrite of the code base that leads to: (1) major performance improvements, (2) a clean and highly efficient code base that greatly facilitates future development, (3) a large amount of new functionality, and (4) new interface capabilities that facilitate inter‐operability with other quantum chemistry program packages. The article describes the most salient features of the program.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> ORCA 6 更新与用户迁移</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>ORCA 6 相比 5.0 的变化会如何改变用户工作流、输出格式或硬件需求？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>工作流变化：</strong>
  <ul>
  <li>JSON 接口成为官方支持的输出格式（Section 2.6, p.6-7）。ORCA 团队明确声明"不再支持输出文件解析 (something that the ORCA team will no longer support)"。——这对依赖解析 .out 文件的工具（包括引擎无关可视化工具）有重大影响。</li>
  <li>orca_2json 程序可将计算结果转为 JSON 格式，并可输出几何、基组、各种 AO/MO 积分、生成 GBW 文件。ORCA 可作为"积分生成器"供外部程序使用（Section 2.6, p.7）。</li>
  <li>Compound 脚本语言大幅扩展（Section 2.6, p.6），支持变量声明、代数运算、for/if/while、&{} 变量置换，可通过 "read" 命令直接访问 ORCA 内部计算结果。</li>
  <li>新增 GOAT（全局构象优化）、DOCKER（分子对接）、SOLVATOR（溶剂壳生成）三个自动化工具（Section 3.6, p.8）。</li>
  <li>Property 文件（固定 ASCII 格式）取代了输出文件解析。</li>
  <li>自动生成 BibTeX 引文文件（Section 2.6, p.7）。</li>
  </ul>
</li>
<li><strong>输出格式变化：</strong>
  <ul>
  <li>JSON 格式成为官方互操作接口（Section 2.6, p.6-7）。</li>
  <li>Property 文件是固定格式的 ASCII 摘要。</li>
  <li>GBW 文件可编程读写。</li>
  <li>旧模块删除（Section 2.2, p.3）：orca_scf、orca_cpscf、orca_scfhess、orca_eprnmr、orca_soc、orca_gtoint、orca_anoint 均被替换或合并。</li>
  </ul>
</li>
<li><strong>硬件需求变化：</strong>
  <ul>
  <li>leanSCF 大幅降低内存需求（Section 2.3, p.4-5）：采用 "Forget/Sleep/WakeUp" 模式，最少矩阵驻留内存；共享内存存储只读矩阵（如 RI 度量矩阵）。</li>
  <li>支持 30,000-100,000 基函数的 SCF 计算（此前无法实现）。</li>
  <li>COSX 加速 2-4 倍（Section 2.5, p.5）。</li>
  <li>Split-RI-J 加速 20%（Section 2.5, p.5）。</li>
  <li>Group 并行化用于 Hessian 计算（Section 2.5, p.6）。</li>
  <li>SCF 收敛平均减少 20% 的迭代次数（Section 2.5, p.6）。</li>
  <li>快速多极方法 (FMM) 用于 QM/MM 点电荷计算（Section 3.6, p.8）。</li>
  </ul>
</li>
</ul>
<h3>问题 2</h3>
<p>版本升级是否可能破坏解析器或默认设置，工具应保存哪些兼容性信息？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>对解析器的破坏性：</strong>
  <ul>
  <li><strong>是</strong>。ORCA 6 明确宣布不再支持输出文件解析（Section 2.6, p.6："something that the ORCA team will no longer support"）。依赖 .out 文件正则匹配的工具将面临高风险。</li>
  <li>七个传统模块被删除（Section 2.2, p.3）：orca_scf → orca_guess + orca_leanscf；orca_cpscf → orca_scfresp；orca_scfhess/orca_eprnmr/orca_soc 功能并入 orca_prop；orca_gtoint → orca_startup；orca_anoint 不再需要。</li>
  <li>CASSCF 耦合系数计算大幅加速（Section 2.5, p.6），可能影响旧版脚本对 CASSCF 步骤行为的假设。</li>
  </ul>
</li>
<li><strong>工具应保存的兼容性信息：</strong>
  <ul>
  <li>ORCA 主版本号（5 vs 6）。</li>
  <li>方法关键词及近似级别（RIJCOSX、Split-RI-J、DLPNO 阈值）。</li>
  <li>积分网格（DefGrid1-3）。</li>
  <li>SCF 收敛设置（TightSCF、TRAH）。</li>
  <li>完整的 Property 文件内容（不仅仅是提取的数值）。</li>
  <li>JSON 接口输出的完整内容（若可用）。</li>
  <li>GBW 文件（二进制波函数）——这是最完整的波函数信息源。</li>
  </ul>
</li>
</ul>
<h3>问题 3</h3>
<p>问卷中"长期版本稳定"与"新方法覆盖"是否存在需要测量的权衡？</p>
<p><strong>回答：</strong></p>
<ul>
<li><strong>存在明确的权衡。</strong>ORCA 6 论文提供了直接证据：
  <ul>
  <li>ORCA 5（2021）到 ORCA 6（2024）仅间隔 3 年，且~90% 核心基础架构被重写（Section 4, p.9）。</li>
  <li>论文预期"更短的发布周期"（Section 4, p.9："shorter release cycles"），意味着更频繁的变更。</li>
  <li>尽管变化剧烈，用户群从 ~35,000（ORCA 5）增长到 80,000（2025 年 1 月，Section 1, p.2），表明用户对快速迭代的接受度较高。</li>
  <li>JSON 接口的引入实际上提高了长期稳定性——它取代了脆弱的输出文件解析，提供了稳定的机器可读接口。</li>
  </ul>
</li>
<li><strong>问卷建议测量的维度：</strong>
  <ul>
  <li>用户对"程序版本升级频率"的容忍度。</li>
  <li>用户是否愿意为性能/新方法接受工作流变更。</li>
  <li>工具链稳定性 vs 计算方法前沿——不同用户群体（生产型 vs 研究型）的偏好差异。</li>
  <li>JSON/Property 文件等标准化输出接口是否降低了升级风险感知。</li>
  </ul>
</li>
</ul>
<h2>阅读后回填</h2>
<ul>
<li>对问卷题目或选项的影响：
  <ul>
  <li>Q16（格式覆盖面）：必须包含 JSON、Property 文件格式；ORCA 6 的 JSON 接口可能成为行业标准，值得在问卷中单列。</li>
  <li>B 模块（信任度）：JSON 接口的"官方支持"承诺是重要的信任信号。</li>
  <li>D 模块（引擎无关）：ORCA 6 的 JSON 接口是"引擎无关工作流"的具体案例——它使得 ORCA 可作为积分生成器、几何优化器供外部工具使用。</li>
  </ul>
</li>
<li>可转化为访谈追问的内容：
  <ul>
  <li>用户是否了解 ORCA 6 的 JSON 接口？是否已迁移？</li>
  <li>用户是否曾因 ORCA 版本升级导致后处理脚本损坏？</li>
  <li>Compound 脚本用户是否认为它比 Python 脚本更好？</li>
  <li>leanSCF 的内存优化是否使用户能够在更大的体系上运行计算？</li>
  </ul>
</li>
<li>关键证据页码/图表：
  <ul>
  <li>Figure 1 (p.2)：ORCA 6 的壳层架构（SHARK → DRIVERS → Core → Compound/Property → GUI）。</li>
  <li>Figure 2 (p.5)：响应属性计算的信息流——密度容器 + 属性积分容器 → orca_prop。</li>
  <li>Figure 3 (p.7)：Compound 脚本示例——展示了与 ORCA 内部数据的深度集成。</li>
  <li>Section 2.2 (p.3)：已删除的遗留模块列表。</li>
  <li>Section 2.6 (p.6-7)：JSON 接口与官方接口策略。</li>
  <li>Section 2.3 (p.4-5)：leanSCF 内存优化。</li>
  </ul>
</li>
<li>仍未解决的问题：
  <ul>
  <li>orca_2json 是否支持所有方法的所有输出？有无遗漏？</li>
  <li>JSON 格式的 schema 是否公开和版本化？</li>
  <li>GBW 文件的二进制格式是否稳定？跨版本兼容吗？</li>
  <li>Compound 脚本能否触发 .cube 文件生成？</li>
  </ul>
</li>
</ul>
<p><strong>总结：</strong> ORCA 6 (Neese, 2025) 是一次近乎完整的代码重写（~90% 基础架构），对引擎无关可视化工具有重大启示：(1) ORCA 6 引入的 JSON 接口是"官方支持的机器可读输出"，使 ORCA 可作为积分生成器、几何优化器或外部轨道提供者被其他程序调用——这正是引擎无关工作流的理想接口模式；(2) 团队明确放弃对输出文件解析的支持，这意味着所有依赖 ORCA .out 文件解析的外部工具必须迁移到 JSON/Property 文件接口；(3) leanSCF 的低内存需求使 30,000-100,000 基函数的 SCF 成为可能，这意味需要处理更大规模三维场的可视化需求将显著增长；(4) 用户从 ~35,000 增长到 80,000 证明了 ORCA 社区的快速扩张，问卷必须覆盖这个日益重要的用户群体；(5) ORCA 的 Compound 脚本+Property 文件+JSON 接口的"深度集成"模式，与 PySCF 的纯 Python 生态形成有趣对比——两者分别代表了"自包含"和"可编程"两种不同的后处理哲学，问卷 D 模块应探索用户对这两种模式的偏好。</p>
