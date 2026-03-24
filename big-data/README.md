# 大数据进阶 — 16周学习计划

> 目标：从"会用"升级到"能主导架构设计"

---

## 整体路线图

```
Week 1–3    Phase 1：架构全景认知
Week 4–7    Phase 2：Flink深入进阶
Week 8–11   Phase 3：Spark深化
Week 12–14  Phase 4：实时数仓设计
Week 15–16  Phase 5：数据治理
```

---

## Phase 1：架构全景（Week 1–3）

> 先建立"地图"，知道整个大数据生态的定位

| 周次 | 主题 | 核心问题 |
|------|------|---------|
| Week 1 | Lambda vs Kappa架构 | 批+流的设计哲学；他现有系统是哪种？ |
| Week 2 | 大数据组件生态图谱 | Kafka/Flink/Spark/Hive/Iceberg各自定位 |
| Week 3 | 存储层选型 | HDFS vs 对象存储 vs 列式存储（Parquet/ORC） |

### Week 1 详细（Day by Day）

| 天 | 内容 | 实践任务 |
|----|------|---------|
| Day 1 | Lambda架构：批处理层+速度层+服务层 | 画出他当前交易所标签系统的架构图 |
| Day 2 | Kappa架构：用流统一批 | 对比Lambda和Kappa的适用场景 |
| Day 3 | 他当前系统架构分析 | 画出Kafka→Flink→存储的数据流 |
| Day 4 | 实时vs批量一致性问题 | 分析他遇到过的数据延迟/不一致案例 |
| Day 5 | 架构选型决策框架 | 写一个架构选型checklist |

---

## Phase 2：Flink深入进阶（Week 4–7）

> 他有Flink实战经验，现在深入原理和高级特性

| 周次 | 主题 | 实践 |
|------|------|------|
| Week 4 | 时间语义：Event Time/Processing Time/Watermark | 写watermark处理延迟数据的demo |
| Week 5 | 状态管理：Keyed State + Operator State | 实现有状态的用户行为统计 |
| Week 6 | Checkpoint机制 + Exactly-Once语义 | 分析Checkpoint失败的排查思路 |
| Week 7 | Flink CEP（复杂事件处理） + 生产调优 | 用CEP实现欺诈检测模式 |

### Flink核心概念速查

```
时间类型：
  EventTime   → 事件真实发生时间（推荐，但需处理乱序）
  ProcessingTime → Flink处理时间（简单，但不准）
  IngestionTime  → 进入Flink的时间

Watermark：
  解决乱序问题，告诉Flink"这个时间点之前的数据都到齐了"
  BoundedOutOfOrdernessWatermarks(Duration.ofSeconds(5))

State类型：
  ValueState      → 单值
  ListState       → 列表
  MapState        → 键值对（他的标签系统很可能用这个）
  ReducingState   → 聚合
```

---

## Phase 3：Spark深化（Week 8–11）

> 他用过Spark，现在学调优和SQL优化

| 周次 | 主题 | 实践 |
|------|------|------|
| Week 8 | Spark内存模型 + GC调优 | 分析OOM案例，调整spark.executor.memory |
| Week 9 | Catalyst优化器 + Tungsten引擎 | 解释执行计划（explain()），理解优化过程 |
| Week 10 | Spark SQL高级 + 窗口函数 | 用Spark SQL实现画像指标计算 |
| Week 11 | Spark Streaming vs Structured Streaming | 对比Flink和Structured Streaming的适用场景 |

### Spark调优常用参数速查

```
spark.executor.memory         → Executor内存（通常4–8G）
spark.executor.cores          → 每个Executor的核数（2–4）
spark.sql.shuffle.partitions  → Shuffle分区数（默认200，需调整）
spark.default.parallelism     → 默认并行度
spark.sql.adaptive.enabled    → AQE自适应查询（Spark 3.x，必开）
```

---

## Phase 4：实时数仓设计（Week 12–14）

> 这是他作为Principal Engineer最需要的架构能力

| 周次 | 主题 | 输出 |
|------|------|------|
| Week 12 | 数仓分层：ODS→DWD→DWS→ADS | 设计交易所标签系统的数仓分层方案 |
| Week 13 | Table Format：Iceberg vs Delta Lake vs Hudi | 选型对比文档 |
| Week 14 | 实时数仓完整链路设计 | 输出一份可复用的架构设计文档 |

### 数仓分层说明

```
ODS（原始层）    → 原始数据，1:1同步，不加工
DWD（明细层）    → 清洗、标准化、去重
DWS（汇总层）    → 按主题聚合（用户/物品/行为）
ADS（应用层）    → 直接服务业务，推荐/标签/报表
```

---

## Phase 5：数据治理（Week 15–16）

| 周次 | 主题 |
|------|------|
| Week 15 | 元数据管理（Apache Atlas）+ 数据血缘 |
| Week 16 | 数据质量监控 + 告警体系设计 |

---

## 每日学习节奏

```
每天 45–60 分钟

15分钟  — 理论学习（文档/书）
20分钟  — 代码实践（本地Flink/Spark环境）
10分钟  — 和工作场景对比（今天学的，在生产中是怎么用的？）
10分钟  — 记录问题（发给Claude讨论）
```

---

## 本地环境搭建

```bash
# Docker启动Flink
docker run -d --name flink-jobmanager \
  -p 8081:8081 flink:latest jobmanager

# Docker启动Spark
docker run -d --name spark \
  -p 4040:4040 bitnami/spark:latest

# Kafka（已有经验，跳过）
```


---

# 大数据学习 — 核心背景档案

> Claude每次辅导前必读此文件

---

## 学员背景

- **已有实战**：Kafka、Flink、Spark、Hadoop、Redis，均有生产环境经验
- **当前项目**：交易所千万级DAU推荐系统，标签系统，实时数据处理
- **级别判断**：实战经验扎实，但缺乏系统化理论架构认知
- **学习目标**：
  - 从"会用"升级到"能设计架构"
  - 能主导大数据平台从0到1建设
  - 深化Flink状态管理/Spark调优等进阶能力

---

## 能力差距分析

| 能力项 | 当前水平 | 目标水平 |
|--------|---------|---------|
| Kafka | 生产使用，消费者组/分区 | 深入原理：ISR/HW/Exactly-Once/调优 |
| Flink | 实时计算上手 | 状态管理/CEP/Checkpoint/生产调优 |
| Spark | 批处理使用 | SQL优化/内存调优/Spark Streaming对比 |
| 数仓架构 | 了解Lambda | 能设计实时数仓：ODS/DWD/DWS/ADS |
| 数据治理 | 参与过 | 能主导：血缘/质量/元数据 |

---

## 学习阶段规划

```
Phase 1（Week 1–3）   架构全景 — Lambda vs Kappa，实时vs批处理设计
Phase 2（Week 4–7）   Flink进阶 — 状态/时间/Checkpoint/CEP/调优
Phase 3（Week 8–11）  Spark深化 — Catalyst优化/内存模型/Spark SQL/MLlib
Phase 4（Week 12–14） 实时数仓 — 分层设计/Iceberg/Delta Lake/Hudi
Phase 5（Week 15–16） 数据治理 — 元数据/数据质量/血缘追踪
```

---

## 与工作结合的学习方向

他在交易所做推荐+标签系统，重点结合：
- **实时特征工程**：Flink → 实时用户行为特征计算
- **标签系统架构**：批+流融合的标签生产链路
- **数据血缘**：标签从哪里来，影响什么
- **性能调优**：高并发下的Kafka消费/Flink背压处理

---

## 每次练习结构

每个主题按以下顺序学：
1. **原理**：这个技术解决什么问题，为什么这么设计
2. **对比**：和他已知的技术有什么区别
3. **代码**：写一个实际可运行的demo
4. **生产问题**：这个技术在生产中常见的坑是什么

---

## 推荐资源

- Flink官方文档 + Apache Flink实战（书）
- Spark权威指南（Spark: The Definitive Guide）
- 数仓建模：Kimball方法论
- 论文：Google Bigtable/Spanner/Dremel（架构级理解）
