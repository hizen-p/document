---
name: open-source-project-analyzer
version: 3.0.0
description: >
  对 GitHub、GitLab、Gitee 等开源项目进行系统化拆解，
  包括仓库真实性验证、项目领域识别、学习门槛评估、
  快速验证、架构分析、核心调用链追踪、领域专项分析
  和源码学习路线生成。
---

# 开源项目结构化拆解分析器

## Skill 目标

将陌生开源项目转换为：

- 可验证
- 可运行
- 可理解
- 可追踪
- 可逐步深入
- 可进行二次开发

的系统化学习体系。

---

# 输出格式主标准

本 Skill 的所有最终输出格式以 `output_format/OutputFormat .md` 为唯一主标准。

`output_format/OutputFormat .md` 已经规定：

- 所有输出文档必须输出到 `./docs/` 目录下
- Markdown 基础规范、表格、Mermaid、公式、代码、JSON/YAML、证据、不确定性等全部格式要求

本 Skill 自身不再重复定义上述格式规则。本 Skill 中与 `output_format/OutputFormat .md` 冲突的格式约定一律失效。仅在涉及本 Skill 专属内容（如重要度标签、阶段完成标准、分析边界等）时补充说明。

---

# 输入

支持：

- GitHub/GitLab/Gitee 仓库链接
- 项目名称
- 项目简介
- README
- 源码目录
- 源码文件
- 项目附件

---

# 核心执行流程

必须执行。下表为 15 步流程与工作流文件的映射关系。

| 步骤 | 内容 | 对应工作流 |
| --- | --- | --- |
| 1 | 项目输入识别 | workflows/00_project_intake.md |
| 2 | 信息完整性检查 | workflows/00_project_intake.md |
| 3 | 仓库真实性检查 | workflows/01_repository_check.md |
| 4 | 项目领域识别 | workflows/02_domain_identification.md |
| 5 | 项目规模评估 | workflows/03_analysis_planning.md |
| 6 | 分析计划生成 | workflows/03_analysis_planning.md |
| 7 | 前置环境分析 | workflows/04_pre_check.md |
| 8 | 快速验证 | workflows/05_quick_verify.md |
| 9 | 架构分析 | workflows/06_depth_analysis.md |
| 10 | 程序入口定位 | workflows/06_depth_analysis.md |
| 11 | 初始化流程分析 | workflows/06_depth_analysis.md |
| 12 | 核心调用链追踪 | workflows/06_depth_analysis.md |
| 13 | 核心数据结构分析 | workflows/06_depth_analysis.md |
| 14 | 领域专项分析 | workflows/07_domain_analysis.md |
| 15 | 学习路线生成 | workflows/08_learning_map.md |

禁止跳过前置验证直接分析源码架构。

---

# 工作流

按顺序读取：

workflows/00_project_intake.md

↓

workflows/01_repository_check.md

↓

workflows/02_domain_identification.md

↓

workflows/03_analysis_planning.md

↓

workflows/04_pre_check.md

↓

workflows/05_quick_verify.md

↓

workflows/06_depth_analysis.md

↓

workflows/07_domain_analysis.md

↓

workflows/08_learning_map.md

---

# 必须加载的规则

执行过程中始终遵守：

rules/evidence_rules.md

rules/anti_hallucination.md

rules/confidence_rules.md

rules/importance_scoring.md

rules/mermaid_rules.md

rules/large_project_strategy.md

rules/quality_checklist.md

---

# 领域 Profile

根据项目领域选择：

Embedded：

domain_profiles/embedded.md

Multimedia：

domain_profiles/multimedia.md

Network：

domain_profiles/network.md

AI：

domain_profiles/ai.md

Server：

domain_profiles/server.md

Web：

domain_profiles/web.md

Database：

domain_profiles/database.md

Security：

domain_profiles/security.md

Developer Tool：

domain_profiles/developer_tool.md

无法分类或混合领域：

domain_profiles/generic.md

允许同时加载多个 Profile。

---

# 输出

输出到 `./docs/` 目录下。根目录必须是 `./docs/`，可在其下组织子目录。

./docs/
├── project_index.md              # 分析总索引（全规模输出）
├── 00_pre_check/
│   └── pre_check.md
├── 01_quick_verify/
│   └── quick_verify.md
├── 02_depth_analysis/
│   └── depth_analysis.md
├── 03_domain_analysis/
│   └── domain_analysis.md
└── 04_learning_map/
    └── learning_map.md

> 输出格式（Markdown、表格、Mermaid、代码块、公式等）遵循 `output_format/OutputFormat .md` 的规定，本 Skill 不再重复。

## project_index.md

由 workflows/03_analysis_planning.md 阶段生成，是整个分析的总索引。

包含：

- 项目基本信息
- 领域结论
- 规模与分析策略
- 文档索引（5 个分析文件的状态）
- 模块分析索引
- 大型项目分批计划
- 当前分析边界（已分析 / 待分析 / 无法确认）
- 证据覆盖率

大型项目必须在第一批输出 project_index.md，后续每批分析完成后更新索引状态。

## 按规模的输出策略

小型项目：

完整输出全部 6 个文件。

中型项目：

先输出 project_index、pre_check、quick_verify；depth_analysis 可分批。

大型项目：

第一批只输出：

- project_index.md（含分批计划）
- 项目地图
- 顶层模块
- 核心调用链候选

然后按分批计划逐步深入，每批完成后更新 project_index.md。

---

# 最终原则

真实 > 完整

证据 > 猜测

调用链 > 文件罗列

实际项目 > 固定模板

学习路线 > 信息堆砌
