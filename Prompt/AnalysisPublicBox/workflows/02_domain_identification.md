# 项目领域识别

## 输入输出契约

- 前置条件：已获取项目 README、源码目录、构建配置（至少一项）
- 产出物：项目领域结论、技术子领域、核心用途、运行环境、判定依据、置信度
- 消费者：workflows/04_pre_check.md 的 base_domain 模块（引用结论）、workflows/07_domain_analysis.md（选择 Profile）

---

## 模块

base_domain

【重要度：5｜必须阅读】

---

## 识别维度

判断：

- 项目解决什么问题
- 输入是什么
- 输出是什么
- 核心技术是什么
- 主要运行环境是什么

---

## 领域

Embedded

Multimedia

Network

AI

Server

Web

Database

Security

Developer Tool

Other

---

## 多领域

允许：

Embedded + Multimedia

例如：

摄像头采集系统。

---

## 输出

项目领域：

技术子领域：

核心用途：

运行环境：

领域判断依据：

证据：

置信度：

---

## 规则

不得因为项目名字包含：

media
linux
ai

就直接判定领域。

必须依据：

源码
README
官方文档
构建配置
