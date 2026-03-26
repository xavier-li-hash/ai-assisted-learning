# AI硕士预科 — 每日训练说明

---

## 每天开始学习，告诉Claude

```
今天学AI硕士预科 Phase X Week X Day X，主题是[XXX]。
我有Java后端和推荐系统背景，
请用工程师视角解释理论，并帮我检查代码和习题答案。
```

---

## 每日学习步骤

```
1. 打开当天的 WeekX-DayX.md 文件
2. 阅读理论部分（30分钟）
3. 动手跑代码（45分钟）——不要只看代码，必须自己跑
4. 做练习题，不要偷看答案（15分钟）
5. 对照文件末尾的答案
6. 把不懂的地方发给Claude问
```

---

## Python环境搭建

```bash
# 推荐用conda管理环境
conda create -n ai-master python=3.10
conda activate ai-master

# Phase 1 需要的包
pip install numpy pandas matplotlib scipy scikit-learn

# Phase 3+ 需要的包（提前装好也无妨）
pip install torch torchvision
pip install transformers datasets huggingface_hub

# 开发工具
pip install jupyter notebook ipykernel
```

---

## 当你遇到不懂的公式

```
我在学 [主题]，遇到公式：[公式]
我的理解是：[你的理解]
这个公式在[推荐系统/神经网络/XX场景]里的作用是什么？
```

---

## 当你想确认代码是否正确

```
我写了这段代码实现[功能]：
[你的代码]
请帮我检查是否正确，并解释输出结果的含义。
```

---

## 当你看完一周想总结

```
我完成了 Phase X Week X，主题是[XXX]。
请帮我：
1. 梳理本周核心概念
2. 指出我还需要强化的地方
3. 预告下周内容
```

---

## 论文精读模板（Phase 4+ 用）

```
论文：[标题]
会议/年份：
一句话总结：

问题：这篇论文解决了什么问题？
方法：核心方法是什么？
结果：比之前好多少？
和我工作的关系：在推荐系统/LLM工程中怎么用？
```

---

## 学习原则

1. **代码跑通才算学会** — 理解了但没跑代码等于没学
2. **工程师的优势** — 你懂业务，每个算法都问"在推荐系统里怎么用"
3. **数学不需要背** — 理解"这个设计解决了什么问题"就够了
4. **答案在文件末尾** — 做完题再看，不要提前翻
