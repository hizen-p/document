# Database Domain Profile

适用：

- 数据库管理系统（DBMS）
- 存储引擎
- 数据库中间件
- 分库分表中间件
- 数据同步工具
- 数据库驱动/客户端
- 缓存数据库

---

## storage_engine_analysis

【重要度：5｜必须阅读】

分析实际存储结构：

数据
↓
Page/Block
↓
Segment/Extent
↓
文件
↓
磁盘

只保留项目真实存在的层级。

---

## query_processing

【重要度：5｜必须阅读】

分析查询处理流程：

SQL/查询输入
↓
词法分析
↓
语法分析
↓
语义分析
↓
查询优化
↓
执行计划
↓
执行

不存在的阶段删除。

---

## transaction_management

【重要度：5｜必须阅读】

分析：

- 事务生命周期：开始 → 执行 → 提交/回滚
- 隔离级别实现
- 并发控制机制（锁 / MVCC / 时间戳）
- WAL/Redo Log
- Undo Log
- 崩溃恢复流程

仅存在事务机制时分析。

---

## index_analysis

【重要度：5｜必须阅读】

分析：

- 索引类型（B+Tree / Hash / LSM / Bitmap）
- 索引结构
- 索引构建流程
- 索引维护
- 查询是否命中索引

仅存在索引机制时分析。

---

## replication_analysis

【重要度：4｜强烈建议】

分析：

- 主从复制流程
- 复制协议
- 一致性保障
- 故障切换

仅存在复制机制时分析。

---

## memory_pool_analysis

【重要度：4｜强烈建议】

分析：

- 内存分配
- 缓冲区管理（Buffer Pool）
- 淘汰策略
- 刷盘策略

仅存在内存管理时分析。

---

## persistence_model

【重要度：5｜必须阅读】

分析：

- 数据持久化方式
- 日志系统（WAL / Redo / Undo / Binlog）
- Checkpoint 机制
- 崩溃恢复流程

---

## client_protocol

【重要度：4｜强烈建议】

分析：

- 通信协议
- 连接管理
- 认证流程
- 命令解析

仅存在网络协议层时分析。
