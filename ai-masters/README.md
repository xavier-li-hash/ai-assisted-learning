# AI硕士预科 — 系统学习计划

> 策略：工程经验为锚，系统补全理论体系，提前掌握AI硕士所需全部基础

---

## 五阶段总览（36周）

```
Phase 1   Python科学计算 + 数学基础    Week  1– 6   ← 当前阶段
Phase 2   机器学习基础                Week  7–14
Phase 3   深度学习                    Week 15–22
Phase 4   NLP与大模型                 Week 23–30
Phase 5   AI系统工程 + 研究方法        Week 31–36
```

---

## Phase 1：Python科学计算 + 数学基础（Week 1–6）

> 优先级最高。你是Java主力，Python科学栈（NumPy/Pandas/PyTorch）需要快速上手。
> 数学通过代码来学，不死磕抽象公式。

| 周次 | 主题 | 核心内容 |
|------|------|---------|
| Week 1 | Python科学计算 | NumPy·Pandas·Matplotlib·scikit-learn初体验 |
| Week 2 | 线性代数核心 | 向量·矩阵·特征值·SVD（用NumPy验证） |
| Week 3 | 概率统计 | 分布·贝叶斯·期望方差·假设检验 |
| Week 4 | 微积分与优化 | 偏导数·链式法则·梯度下降（手推+代码） |
| Week 5 | 综合实战1 | 用数学工具实现线性回归（从零不用sklearn） |
| Week 6 | 综合实战2 + 阶段测试 | 矩阵分解入门·协同过滤初步·阶段自测 |

---

## Phase 2：机器学习基础（Week 7–14）

| 周次 | 主题 | 代码实践 |
|------|------|---------|
| Week 7 | 监督学习框架 + 线性回归 | sklearn实现 + 手推梯度下降 |
| Week 8 | 逻辑回归 + 分类评估指标 | 二分类任务（CTR预估场景） |
| Week 9 | 决策树 + 随机森林 | 特征重要性分析 |
| Week 10 | SVM + 核函数 | 对比线性/非线性分类 |
| Week 11 | 无监督：K-Means + 层次聚类 | 用户聚类demo |
| Week 12 | PCA降维 + 特征工程 | 高维数据可视化 |
| Week 13 | 模型评估：交叉验证·正则化 | 过拟合/欠拟合实验 |
| Week 14 | 集成学习：XGBoost / LightGBM | 排序模型（CTR预估） |

---

## Phase 3：深度学习（Week 15–22）

| 周次 | 主题 | 代码实践 |
|------|------|---------|
| Week 15 | 神经网络基础 + 反向传播 | 手写两层网络（NumPy） |
| Week 16 | PyTorch入门 + 自动求导 | 用PyTorch重写前一周的网络 |
| Week 17 | 优化器（Adam/SGD）+ BatchNorm | 训练稳定性实验 |
| Week 18 | Embedding层 + 类别特征 | 用户/物品Embedding |
| Week 19 | CNN（了解为主） | 图像分类入门 |
| Week 20 | RNN + LSTM | 序列行为建模 |
| Week 21 | Attention机制精读 | 自注意力理解与实现 |
| Week 22 | Transformer架构 | 实现mini-Transformer |

---

## Phase 4：NLP与大模型（Week 23–30）

| 周次 | 主题 | 代码实践 |
|------|------|---------|
| Week 23 | Word2Vec + FastText | 训练词向量 |
| Week 24 | BERT原理精读 | HuggingFace跑BERT推理 |
| Week 25 | GPT系列 + 预训练机制 | 对比BERT vs GPT |
| Week 26 | RAG系统理论化（强项） | 搭完整RAG pipeline |
| Week 27 | Fine-tuning（LoRA/PEFT） | 小数据集微调 |
| Week 28 | Prompt Engineering | 设计evaluation框架 |
| Week 29 | 向量数据库（Milvus/Faiss） | 语义检索demo |
| Week 30 | 大模型生产部署 | 量化·蒸馏·vLLM推理优化 |

---

## Phase 5：AI系统工程 + 研究方法（Week 31–36）

| 周次 | 主题 |
|------|------|
| Week 31 | 推荐系统理论化：协同过滤·矩阵分解 |
| Week 32 | 深度推荐模型：Wide&Deep·DIN·DIEN |
| Week 33 | MLOps：模型监控·AB测试·数据飞轮 |
| Week 34 | 如何读论文（SQ3R方法 + 精读框架） |
| Week 35 | 课程项目/论文结构设计 |
| Week 36 | 综合复习 + 入学前自测 |

---

## 每日学习结构

```
每天总计 1.5–2 小时

30分钟  — 理论学习（核心概念 + 直觉理解）
45分钟  — 代码实践（动手跑通，不只是看）
15分钟  — 练习题（检验理解，答案在文件末尾）
```

---

## 目录结构

```
ai-masters/
├── README.md                        ← 学习计划 + 核心背景（此文件）
├── training-guide.md                ← 每日操作说明
│
├── phase1-python-math/              ← 阶段1：Python + 数学基础
│   ├── Week1-Day1.md                ← NumPy入门
│   ├── Week1-Day2.md                ← 矩阵运算 + 线性代数
│   ├── Week1-Day3.md                ← 概率统计基础
│   ├── Week1-Day4.md                ← Pandas特征处理
│   └── Week1-Day5.md                ← 综合实战 + 周总结
│
├── phase2-ml-basics/                ← 阶段2：机器学习基础
│   └── Week1-Day1.md                ← 监督学习 + 线性回归
│
├── phase3-deep-learning/            ← 阶段3：深度学习（待创建）
├── phase4-nlp-llm/                  ← 阶段4：NLP与大模型（待创建）
└── phase5-ai-engineering/           ← 阶段5：AI系统工程（待创建）
```

**当前进度（Phase 1 · Week 1）：** 已完成 Day 1

---

# 核心背景档案

> Claude每次辅导前必读此文件

---

## 学员背景

- **工程实战**：推荐系统10年，ChatGPT+向量检索实战（荔枝集团）
- **语言**：Java主力，Python需要系统练习
- **数学基础**：有大致了解，需要结合代码系统化
- **硕士方向**：AI/ML授课型硕士（Taught Master），工程与应用方向

---

## 核心学习策略

1. **代码优先**：每个数学概念都用Python写出来，不只看公式
2. **工程类比**：用推荐系统/Java/后端经验类比解释新概念
3. **问"为什么设计"**：不背公式，理解设计动机
4. **答案放文件末尾**：做题时不看答案，做完再对照

---

## 工具栈

- **语言**：Python 3.10+
- **数学工具**：NumPy、SciPy、Pandas
- **ML框架**：scikit-learn（Phase 2）、PyTorch（Phase 3+）
- **NLP**：HuggingFace Transformers
- **环境**：Jupyter Notebook / Colab / VSCode
