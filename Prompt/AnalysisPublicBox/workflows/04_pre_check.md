# 前置预检工作流

## 输入输出契约

- 前置条件：workflows/01_repository_check.md 的仓库结论、workflows/02_domain_identification.md 的领域结论、workflows/03_analysis_planning.md 的规模策略
- 产出物：00_pre_check/pre_check.md（技能门槛、硬件、系统环境、软件依赖、风险）
- 消费者：workflows/05_quick_verify.md、workflows/08_learning_map.md

---

输出：

00_pre_check/pre_check.md

---

# 模块

## repository_validity_check

【重要度：5｜必须阅读】

本模块不重新执行仓库检查。

直接引用 workflows/01_repository_check.md 的检查结论，汇总到预检文档。

仅在前置检查结论缺失时，才补充执行。

---

## base_domain

【重要度：5｜必须阅读】

本模块不重新执行领域识别。

直接引用 workflows/02_domain_identification.md 的领域结论，汇总到预检文档。

仅在领域结论缺失时，才补充执行。

---

## personal_skill_threshold

【重要度：5｜必须阅读】

分析：

- 编程语言
- Linux
- 操作系统
- 网络
- 数据结构
- 编译
- 项目领域知识

输出：

必备：

建议：

高级：

零基础可行性：

学习难点：

---

## hardware_precondition

【重要度：5｜必须阅读】

分析：

- CPU 架构
- x86
- ARM
- RISC-V
- GPU
- NPU
- ISP
- 外设

无证据禁止提供：

- 最低内存
- CPU 核数
- FPS
- 性能指标

---

## system_env_precondition

【重要度：5｜必须阅读】

分析：

- OS
- Kernel
- 权限
- 系统组件
- 网络

---

## software_deps_precondition

【重要度：5｜必须阅读】

分析：

- Compiler
- Build System
- Runtime
- Library
- SDK
- Version

---

## project_risk_check

【重要度：4｜强烈建议】

分析：

- 构建风险
- 版本风险
- 平台限制
- 依赖冲突
- 文档问题
