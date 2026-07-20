# Survey Option Fatigue Reduction Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将 18 题问卷的封闭题选项从 193 项压缩到 118 项，并让保留的细分信息直接服务于量子化学场可视化开发。

**Architecture:** 问卷 Markdown 继续作为题号、题型、变量、选项和分支的唯一来源。按共同开发含义合并选项；只有场类型、文件格式、可视化风险和功能优先级保留超过 6 项，ChemBlender 2.1 仅作为现有能力基线，不新增产品使用经历题。

**Tech Stack:** Markdown、Git、现有 Node.js 校验脚本

## Global Constraints

- 保持 Q01–Q18、18 个唯一变量和 Q03 到 Q10 的条件分支。
- Q01–Q07、Q09、Q12、Q14–Q16 各 6 项，Q17 为 5 项；Q08 为 10 项、Q10 为 12 项、Q11 为 9 项、Q13 为 10 项。
- 所有选项继续使用 `- 选项：` 下的缩进多行列表。
- Q13 最多选择 4 项；一般多选题最多选择 2–3 项；Q08 和 Q10 不设过低上限。
- 不修改 ChemBlender 仓库，不增加依赖，不跟踪 `tmp/`。

---

### Task 1: 压缩问卷选项

**Files:**
- Modify: `docs/survey/quantum_chemistry_software_survey_draft_2026-07-19.md`
- Test: `tmp/final_verify.mjs`

**Interfaces:**
- Consumes: `docs/superpowers/specs/2026-07-20-reduce-survey-option-fatigue-design.md`
- Produces: 18 题、118 个封闭题选项的问卷 Markdown

- [x] **Step 1: 记录修改前基线**

Run: `node tmp/final_verify.mjs`

Expected: 18 道题、18 个变量、17 个选项块，现有检查全部通过。

- [x] **Step 2: 合并辅助分析题选项**

将 Q01–Q07、Q09、Q12、Q14–Q16 压缩为每题 6 项，将 Q17 压缩为 5 项。合并规则：身份按工作职能、场景按计算对象、参与方式按工作流责任、成果按使用方式、痛点与信任依据按同一后续行动归组。

- [x] **Step 3: 保留开发核心题的必要粒度**

将 Q08 保留为 10 项、Q10 保留为 12 项、Q11 保留为 9 项、Q13 保留为 10 项。Q13 必须包含“量子化学场与现有分子/晶体结构、周期超胞和截面功能联动”，其余选项覆盖导入与对齐、交互显示、科学语义、周期/大网格、多帧、证据记录、分享和自动化。

- [x] **Step 4: 调整选择上限与预测试说明**

一般多选题使用“最多 2 项”或“最多 3 项”；Q13 改为最多 4 项。预测试说明改为：如完成时间仍超过 9 分钟，优先检查题干理解和分支配置，不再优先删除四道开发核心题的信息。

- [x] **Step 5: 更新并运行结构检查**

在 `tmp/final_verify.mjs` 中把封闭题总选项期望值设为 118，并检查只有 Q08、Q10、Q11、Q13 超过 6 项。

Run: `node tmp/final_verify.mjs`

Expected: `questionCount` 为 18、`variableCount` 为 18、`totalOptions` 为 118、`longOptionQuestions` 仅为 Q08/Q10/Q11/Q13、`failed` 为空数组。

### Task 2: 同步公开说明并完成交付

**Files:**
- Modify: `README.md`
- Modify: `docs/research/pyscf_quantum_chemistry_visualization_research_2026-07-19.md`
- Modify: `docs/superpowers/plans/2026-07-20-reduce-survey-option-fatigue.md`

**Interfaces:**
- Consumes: Task 1 的最终问卷和 ChemBlender 2.1 源码核查结论
- Produces: 可导航、可审计且不夸大现有能力的公开说明

- [x] **Step 1: 更新 README 导航与问卷摘要**

加入本设计稿和实施计划的内链，并说明 18 题问卷已将 17 道封闭题压缩为 118 个选项，详细信息集中在四道开发核心题。

- [x] **Step 2: 记录 ChemBlender 2.1 能力边界**

在研究说明中加入基于提交 `78c2d8d` 的简短核查：已有分子/晶体结构建模、周期结构和渲染能力；未发现 Gaussian Cube、体数据、等值面或 OpenVDB 导入实现；因此问卷重点测量场数据与现有结构能力的衔接需求。

- [x] **Step 3: 运行完整验证**

Run:

```powershell
node tmp/final_verify.mjs
node tmp/check_repo_layout.mjs
node tmp/verify_notes.mjs
git diff --check
```

Expected: 三个脚本均返回成功，`failed` 为空；61 篇 BibTeX 与 61 篇笔记保持一致；Git diff 无空白错误。

- [ ] **Step 4: 扫描隐私并提交**

暂存明确修改的 Markdown 文件，确认 `tmp/` 未暂存；扫描本机路径、身份、机构、凭据和 Sci-Hub 模式后提交：

```powershell
git commit -m "docs: streamline survey options"
```

- [ ] **Step 5: 推送并验证远端**

Run: `git push origin main`

Expected: 本地 `HEAD` 与 `origin/main` 一致，工作区干净。

### Task 3: 重写问卷开场说明

**Files:**
- Modify: `docs/survey/quantum_chemistry_software_survey_draft_2026-07-19.md:8`

**Interfaces:**
- Consumes: `docs/superpowers/specs/2026-07-20-reduce-survey-option-fatigue-design.md` 中已确认的两段融合开场文字
- Produces: 说明 ChemBlender 现状、后续更新方向和作答边界的问卷开场

- [x] **Step 1: 替换开场正文**

使用设计稿中已确认的两段融合文字：第一段说明调查目的、ChemBlender 当前结构能力，以及场/波函数文件导入、结构对齐、等值面与体渲染、相位与状态、周期/大网格、多帧、参数记录和协作交付等后续方向；第二段说明亲自计算、合作、委托代算和结果使用者均可按实际经历作答。

- [x] **Step 2: 删除题型规则段落**

删除“除开放题外，每题均标注为单选或多选……”整段，不改各题内部的题型和选择上限标注。

- [ ] **Step 3: 验证并交付**

Run:

```powershell
node tmp/final_verify.mjs
node tmp/check_repo_layout.mjs
git diff --check
```

Expected: 开场首句与确认文本一致，包含 ChemBlender 更新方向，不包含已删除的题型规则段落；结构、内链和隐私检查通过。提交后执行 `git push origin main`，并确认本地与远端 `HEAD` 一致。
