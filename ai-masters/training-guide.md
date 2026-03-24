# AI授课硕士 — 学习说明

## 每次学习告诉Claude

```
"今天学AI硕士 模块X Week X Day X，主题是[XXX]。
我有推荐系统/大数据工程背景，
请用工程落地的视角帮我解释理论，
不需要简化数学，但要解释'为什么这么设计'。"
```

## Python环境搭建

```bash
# 推荐用conda管理环境
conda create -n ai-master python=3.10
conda activate ai-master
pip install numpy pandas scikit-learn matplotlib torch torchvision
pip install transformers datasets huggingface_hub
pip install jupyter notebook
```

## 学习态度

你是工程师，理论上有两个优势：
1. **直觉好**：你知道推荐系统的业务逻辑，很多算法你"用过"只是不知道背后数学
2. **工程能力强**：代码实现对你不是障碍

需要补的是：
- 数学公式→直觉的转化（不是背公式，是理解"这个设计解决了什么问题"）
- 系统性（你有碎片经验，需要连成体系）

## 论文精读模板

```
论文：[标题]
会议/年份：
一句话总结：

问题：这篇论文解决了什么问题？
方法：核心方法是什么？
结果：比之前好多少？
和我工作的关系：在推荐系统/标签系统中怎么用？
```
