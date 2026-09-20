# ChemBlender量子化学可视化学习包

检索截止：2026-09-20。用途：先阅读定向调研，按需合法取得教材与论文，再由本地Codex使用personal-tutor进行交互训练。

## 从这里开始

先读 [REPORT.md](REPORT.md) 的第0–4节了解学习范围与资料选择，再读第7–9节查看计划与最终任务。取得所需资料后，在本地Codex中让它读取 [CODEX_HANDOFF.md](CODEX_HANDOFF.md) 并从LX00开始。

首轮教材见 [QUANTUM_CHEMISTRY_DATA_SEMANTICS.md](QUANTUM_CHEMISTRY_DATA_SEMANTICS.md)。它按当前 ChemBlender 工作区的真实数据模型整理必学内容：计算架构、最小理论、核心格式、物理量语义、Grid3D、CBQ、验证与 H₂O/CH₃ 实例；不包含 LX00 标准答案。

建议第一次只准备：BK01英文主教材、BK03中文桥梁、RV01实空间可视化综述、RV02方法选择文章，以及DT01–DT04公开文档。不是下载全部22项后才允许学习。

## 文件

| 文件 | 用途 |
|---|---|
| REPORT.md | 完整调研、知识边界、5部教材/13篇文章/4份官方文档、4小时起步与8周计划 |
| QUANTUM_CHEMISTRY_DATA_SEMANTICS.md | 首轮教材：量子化学数据语义、核心格式、Grid3D、CBQ和最小验证 |
| DOWNLOAD_CHECKLIST.md | 合法获取入口、权限边界、建议文件名 |
| reading_manifest.json | 结构化书目、选读范围、优先级、后续本地文件和hash登记 |
| CODEX_HANDOFF.md | 交给本地Codex的启动任务及教学规则 |
| exercises/EXERCISES.md | LX00–LX08练习与迁移任务；不提前附答案 |
| learning_state.json | 初始学习状态，能力均未验证 |
| evidence/TEMPLATE.md | 一次训练的最小证据模板 |
| PACKAGE_CHECKS.json | 文件完整性与结构检查记录，不是科学实验结果 |

materials/下的子目录用于保存你合法取得的材料；目录当前没有教材/论文全文。本包也不含项目输入、第三方skill副本或已测试的代码补丁。

## 给Codex的第一句话

> 请读取这个学习包中的CODEX_HANDOFF.md，结合实际ChemBlender工作区加载personal-tutor并开始训练。先核验最小必要环境，然后只提出LX00，不要替我回答，也不要一次讲完整个课程。

4小时是起步训练；8周64小时是可调整的建议预算，首个4小时包含其中。能力是否达到目标取决于独立完成任务和迁移证据，而不是阅读时长或Codex代做后的测试通过。
