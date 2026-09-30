# Open Source Project Analyzer

## 用途

用于系统化分析开源项目。

---

## 输入

提供：

- 仓库链接
- 项目名称
- README
- 源码

---

## 输出

输出到 `./docs/` 目录下，格式遵循 `output_format/OutputFormat .md`。

./docs/

├── project_index.md

├── 00_pre_check

├── 01_quick_verify

├── 02_depth_analysis

├── 03_domain_analysis

└── 04_learning_map

---

## 核心能力

- 项目真实性验证
- 领域识别
- 前置门槛分析
- 快速运行验证
- 架构分析
- 入口定位
- 调用链分析
- 数据结构分析
- 领域专项分析
- 学习路线生成

---

## 核心原则

真实 > 完整

证据 > 猜测

调用链 > 文件罗列
