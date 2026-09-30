# 深度源码分析工作流

## 输入输出契约

- 前置条件：workflows/04_pre_check.md 的前置结论、workflows/05_quick_verify.md 的运行验证结论
- 产出物：02_depth_analysis/depth_analysis.md（目录结构、技术栈、模块依赖、入口、初始化、调用链、数据结构、线程模型、阅读路线、改单指南）
- 消费者：workflows/07_domain_analysis.md、workflows/08_learning_map.md

---

输出：

02_depth_analysis/depth_analysis.md

---

## dir_struct_analysis

【重要度：5｜必须阅读】

分析：

- 顶层目录
- 核心目录
- 构建
- 测试
- 文档
- 示例

目录结构使用 Mermaid tree 输出。

每个目录：

职责：

是否核心：

阅读优先级：

---

## tech_stack_analysis

【重要度：4｜强烈建议】

输出：

| 技术 | 作用 | 为什么存在 | 是否核心 |
|---|---|---|---|

---

## module_dependency_graph

【重要度：4｜强烈建议】

分析：

模块依赖。

数据流。

控制流。

禁止根据目录猜依赖。

---

## program_entry_analysis

【重要度：5｜必须阅读】

定位：

- main
- daemon entry
- framework entry
- plugin entry

输出：

入口文件
↓
入口函数
↓
初始化
↓
主循环

---

## init_process_flow

【重要度：5｜必须阅读】

分析真实启动过程。

使用 Mermaid graph TD 输出。

例如：

参数
↓
配置
↓
资源
↓
模块
↓
线程
↓
运行

不存在的步骤删除。

---

## core_call_chain_analysis

【重要度：5｜必须阅读】

必须分析：

启动调用链。

核心业务调用链。

数据流调用链。

错误调用链。

每条链使用 Mermaid graph TD 输出：

文件
↓
函数
↓
输入
↓
处理
↓
数据变化
↓
下一模块

---

## core_data_struct

【重要度：4｜强烈建议】

分析：

- struct
- class
- context
- callback
- state machine

生命周期：

创建
↓
初始化
↓
使用
↓
销毁

---

## thread_process_model

【重要度：4｜强烈建议】

存在时分析，使用 Mermaid sequenceDiagram 或 graph TD 输出：

创建
↓
通信
↓
同步
↓
退出

---

## source_read_path

【重要度：5｜必须阅读】

必须给出真实阅读路线：

项目地图
↓
入口
↓
初始化
↓
核心调用链
↓
核心数据
↓
专项模块

---

## dev_modify_guide

【重要度：3｜按需查阅】

输出：

需求
↓
涉及模块
↓
修改点
↓
风险
↓
验证

---

## depth_qa

【重要度：2｜低优先级】
