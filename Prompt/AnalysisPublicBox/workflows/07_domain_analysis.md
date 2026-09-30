# 领域专项分析工作流

## 输入输出契约

- 前置条件：workflows/02_domain_identification.md 的领域结论、workflows/06_depth_analysis.md 的架构与调用链分析
- 产出物：03_domain_analysis/domain_analysis.md（按领域 Profile 加载的专项分析）
- 消费者：workflows/08_learning_map.md

---

## 目标

根据项目实际所属领域，加载对应 Domain Profile，
对项目进行专项技术分析。

本工作流不得脱离项目实际源码进行固定模板填充。

---

# 执行步骤

## 第一步：确认领域

读取：

- 项目 README
- 构建系统
- 顶层目录
- 核心源码
- 官方文档

确认：

主领域：

子领域：

辅助领域：

---

## 第二步：选择 Profile

根据领域加载：

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

未知或混合：

domain_profiles/generic.md

---

## 第三步：多领域项目

如果项目同时涉及多个领域：

例如：

Embedded + Multimedia

允许：

同时加载：

embedded.md

multimedia.md

---

## 第四步：筛选专项模块

Domain Profile 中的模块不是强制全部输出。

必须根据项目实际情况判断：

存在：

输出分析。

不存在：

明确输出：

本项目无相关内容。

无法确认：

明确输出：

当前资料不足，无法确认。

---

## 第五步：领域专项输出

输出文件：

03_domain_analysis/domain_analysis.md

每个模块必须包含：

### 全局总览

说明该模块在当前项目中的真实作用。

---

### 实际实现位置

输出：

目录：

文件：

核心符号：

---

### 工作流程

条件允许时：

使用 Mermaid。

例如：

输入
↓
模块
↓
处理
↓
输出

---

### 细分结构

使用 Mermaid tree 输出：

模块

├── 子模块
│   ├── 功能
│   └── 功能
│
└── 子模块

---

### 证据

说明：

源码位置。

README。

官方文档。

---

### 置信度

高 / 中 / 低。

---

## 禁止

禁止：

因为加载了 Domain Profile，

就假设项目一定存在：

- DMA
- GPU
- NPU
- 多线程
- 网络协议
- 数据库
- 缓存
- Device Tree

Domain Profile 只是：

分析维度候选集合。

不是：

项目事实。
