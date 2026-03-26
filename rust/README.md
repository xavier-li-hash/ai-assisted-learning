# Java转Rust — 16周学习计划

> 目标：从Java老手平滑过渡到能写生产级Rust代码

---

## 整体路线图

```
Week 1–4    Phase 1：所有权与借用（最难，必须真正理解）
Week 5–8    Phase 2：类型系统与错误处理
Week 9–12   Phase 3：异步与并发（Tokio生态）
Week 13–16  Phase 4：项目实战（高性能后端服务）
```

---

## Phase 1：所有权与借用（Week 1–4）

> Java 10年最难适应的就是这里。不要跳过，每个概念都要写代码验证。

| 周次 | 主题 | Java对比 |
|------|------|---------|
| Week 1 | 所有权三原则 + Move语义 | Java引用 vs Rust所有权 |
| Week 2 | 借用（&）与可变借用（&mut） | Java的final / 并发锁 |
| Week 3 | 生命周期（Lifetime）入门 | GC自动管理 vs 手动标注 |
| Week 4 | Slice、String vs &str、综合练习 | Java String/StringBuilder |

### Week 1 详细（Day by Day）

| 天 | 内容 | 代码任务 |
|----|------|---------|
| Day 1 | 安装Rust + Hello World + Cargo基础 | 跑通第一个程序 |
| Day 2 | 所有权三原则：每个值有一个所有者 | 写代码触发编译错误，理解为什么 |
| Day 3 | Move语义：赋值后原变量失效 | Java vs Rust对比代码 |
| Day 4 | Clone vs Copy | 实现Copy和Clone的struct |
| Day 5 | 函数传值 vs 传引用的所有权变化 | 写5个函数，测试所有权转移 |

---

## Phase 2：类型系统与错误处理（Week 5–8）

| 周次 | 主题 | Java对比 |
|------|------|---------|
| Week 5 | Struct、Enum、impl块 | Java Class + 方法 |
| Week 6 | Trait（接口） + 泛型 | Java Interface + Generics |
| Week 7 | Option<T> + Result<T,E> | Java null + Exception |
| Week 8 | 模式匹配 match + ? 操作符 | Java switch + try/catch |

---

## Phase 3：异步与并发（Week 9–12）

> 这是他工作最相关的部分（高并发系统工程师）

| 周次 | 主题 | Java对比 |
|------|------|---------|
| Week 9 | 线程 + Arc<Mutex<T>> | Java Thread + synchronized |
| Week 10 | async/await + Future | Java CompletableFuture |
| Week 11 | Tokio运行时 + 异步IO | Java NIO / Netty概念 |
| Week 12 | Channel通信 + 无锁并发 | Java BlockingQueue / CAS |

---

## Phase 4：项目实战（Week 13–16）

**目标项目：用Rust实现一个高性能标签查询服务**
（贴合他在交易所做的标签系统工作）

| 周次 | 任务 |
|------|------|
| Week 13 | 设计服务架构，用Axum搭HTTP服务骨架 |
| Week 14 | 实现标签存储层（内存HashMap + 序列化） |
| Week 15 | 实现查询接口 + 并发压测 |
| Week 16 | 和Java版本做性能对比，写总结文档 |

---

## 每日练习节奏

```
30–45分钟/天

10分钟  — 阅读今日主题概念（the Book对应章节）
20分钟  — 写代码练习（每天至少20行真实代码）
10分钟  — 故意让编译器报错，读懂错误信息
```

---

## Rustlings 配合使用

安装：`cargo install rustlings`
每天做2–3道 Rustlings 练习，配合主题学习。

| Phase 1对应 | rustlings/variables、rustlings/ownership |
| Phase 2对应 | rustlings/structs、rustlings/traits、rustlings/error_handling |
| Phase 3对应 | rustlings/threads、rustlings/smart_pointers |

---

## 工程目录结构

```
rust/
├── README.md                    ← 学习计划 + 核心背景（此文件）
├── training-guide.md            ← 每日训练操作指南
└── phase1-ownership/            ← Phase 1：所有权与借用（Week 1–4）
    ├── Week1-Day1.md            ← Day 1: Rust安装 + Hello World + Cargo基础
    └── Week1-Day2.md            ← Day 2: 所有权三原则
```

**当前进度（Phase 1 · Week 1）：** 已完成 Day 1–2

---

# Java转Rust学习 — 核心背景档案

> Claude每次辅导前必读此文件

---

## 学员背景

- **Java经验**：10年+，资深级别，熟悉高并发、分布式、JVM调优
- **Rust现状**：已列在简历，属于初步接触阶段，需系统深化
- **学习动机**：
  - 当前在交易所做性能敏感系统，Rust在低延迟/内存安全场景有优势
  - 简历已有Rust，需要真正能用、能写生产代码
  - 系统级编程能力提升，区别于普通Java工程师

---

## Java→Rust 核心心智转换

Java开发者学Rust最难的三个障碍（按优先级训练）：

| 障碍 | Java思维 | Rust思维 |
|------|---------|---------|
| 内存管理 | GC自动管理，不需要关心 | 所有权系统，编译期决定 |
| 错误处理 | throw Exception + try/catch | Result<T,E> + Option<T>，强制处理 |
| 并发 | synchronized/locks，运行时检查 | 编译期检查数据竞争，Send/Sync trait |

---

## 训练阶段规划

```
Phase 1（Week 1–4）   所有权与借用 — Rust最核心，必须真正理解
Phase 2（Week 5–8）   类型系统与错误处理 — Trait/泛型/Result/Option
Phase 3（Week 9–12）  并发与异步 — Tokio生态，与Java多线程类比
Phase 4（Week 13–16） 项目实战 — 用Rust重写一个他熟悉的后端组件
```

---

## 学习资源参考

- **首选**：《The Rust Programming Language》（官方书，the Book）
- **Java对比学习**：每个概念都和Java对应，降低陌生感
- **练习平台**：Rustlings（每天小练习）
- **实战方向**：高性能HTTP服务 / 数据处理管道（贴近他的工作场景）

---

## 辅导风格说明

- 直接用工程师语言，不用过度简化
- 每个Rust概念，先问"Java里这个是怎么做的"，再解释Rust的方式
- 重点强调**为什么Rust这么设计**（编译器是你的同事，不是敌人）
- 练习以代码为主，每天至少写10–20行真实Rust代码
