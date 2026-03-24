# 大数据进阶 — 学习说明

## 每次学习告诉Claude

```
"今天学大数据 Phase X Week X Day X，主题是[XXX]。
我在交易所做推荐+标签系统，有Flink/Spark/Kafka生产经验，
请从架构设计角度帮我深入，结合生产场景解释。"
```

## 本地环境

```bash
# Flink本地集群（Docker）
docker run -d \
  --name flink-jobmanager \
  -p 8081:8081 \
  -e FLINK_PROPERTIES="jobmanager.rpc.address: jobmanager" \
  flink:1.18 jobmanager

# Flink Web UI
open http://localhost:8081

# 提交作业
./bin/flink run examples/streaming/WordCount.jar
```

## 学习最高优先级

1. **Phase 2 Flink进阶** — 和你当前工作最直接相关
2. **Phase 4 实时数仓** — Principal Engineer必备的架构能力
3. Phase 1 架构全景 — 已有感性认识，系统化即可

## 每次学完记录（发给Claude讨论）

```
今天学了：[主题]
我在生产中见过类似问题：[描述]
我当时的处理方式是：[方案]
现在学完理论后，我认为更好的方式是：[新想法]

Claude帮我确认或纠正我的理解。
```

## 架构设计练习（每周一次）

每周结束时，做一个架构设计练习：
> "基于本周所学，重新设计/优化我们的标签系统中的[某个模块]，
> 要求：写出数据流、技术选型理由、关键风险点。"
