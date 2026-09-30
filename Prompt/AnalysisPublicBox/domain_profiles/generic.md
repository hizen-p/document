# Generic Domain Profile

适用于：

- 项目领域暂时无法确认
- 混合型项目
- 工具类项目
- 框架类项目
- 新领域项目

---

# 核心原则

不使用固定领域假设。

从项目真实结构出发。

---

## project_core_problem

【重要度：5｜必须阅读】

分析：

项目解决什么问题。

---

## input_output_analysis

【重要度：5｜必须阅读】

分析：

输入：

↓

处理：

↓

输出：

---

## runtime_model

【重要度：5｜必须阅读】

分析：

项目如何运行。

可能包括：

CLI。

Daemon。

Library。

Plugin。

Service。

GUI。

但必须根据实际项目确认。

---

## module_boundary

【重要度：5｜必须阅读】

分析：

模块职责。

模块边界。

依赖关系。

---

## core_workflow

【重要度：5｜必须阅读】

分析：

核心工作流程。

---

## data_lifecycle

【重要度：4｜强烈建议】

分析：

数据创建。

数据传递。

数据修改。

数据释放。

---

## extension_point

【重要度：3｜按需查阅】

分析：

插件。

Hook。

Callback。

配置。

API。

如果项目存在。

---

# 禁止

禁止因为 Generic Profile 缺少专业规则，

就降低证据标准。

所有结论仍然遵守：

rules/anti_hallucination.md

rules/evidence_rules.md
