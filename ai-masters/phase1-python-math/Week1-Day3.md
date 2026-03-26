# Phase 1 · Week 1 · Day 3：概率统计基础 — ML模型的统计根基

> 今日主题：所有ML模型背后都是概率，学会用NumPy/SciPy做统计推断

**预计时间**：1.5小时（理论30min + 代码45min + 练习15min）

---

## 1. 为什么ML需要概率论

你在推荐系统里做过CTR预估：预测用户点击某个物品的概率。这个"概率"不是玄学，是数学定义的。

ML模型本质上是在学习：**给定输入X，输出y的条件概率 P(y | X)**

- 分类模型：输出每个类别的概率（softmax层就是在算概率分布）
- 回归模型：假设预测误差服从正态分布
- 贝叶斯模型：直接用概率更新信念

---

## 2. 核心概念

### 2.1 随机变量与概率分布

**离散型**：取值是有限个（如骰子点数、用户是否点击）

```python
import numpy as np

# 伯努利分布：用户是否点击（0或1）
# P(X=1) = p, P(X=0) = 1-p
p = 0.3  # CTR = 30%
samples = np.random.binomial(n=1, p=p, size=1000)
print(f"模拟CTR: {samples.mean():.3f}（期望: {p}）")
```

**连续型**：取值是连续的（如用户停留时长、评分）

```python
# 正态分布：N(μ, σ²)
mu, sigma = 5.0, 1.5
samples = np.random.normal(loc=mu, scale=sigma, size=1000)
print(f"均值: {samples.mean():.2f}（期望: {mu}）")
print(f"标准差: {samples.std():.2f}（期望: {sigma}）")
```

### 2.2 期望、方差、标准差

- **期望 E[X]**：随机变量的"平均值"（加权平均，权重是概率）
- **方差 Var[X] = E[(X - μ)²]**：衡量数据的分散程度
- **标准差 σ = √Var[X]**：和原始数据同单位，更直观

```python
data = np.array([2.0, 4.0, 4.0, 4.0, 5.0, 5.0, 7.0, 9.0])

mean = data.mean()          # 5.0
var = data.var()            # 4.0（默认除以N）
std = data.std()            # 2.0
# 注：统计学里除以(N-1)，用 ddof=1
std_unbiased = data.std(ddof=1)   # 2.138

print(f"均值: {mean}, 方差: {var}, 标准差: {std}")
```

**ML中的意义**：特征标准化（减均值除标准差）让所有特征处于同一尺度，梯度下降收敛更快。

### 2.3 协方差与相关系数

- **协方差 Cov(X,Y)**：两个变量一起变化的程度
- **相关系数 ρ = Cov(X,Y) / (σ_X × σ_Y)**：标准化后的协方差，范围[-1, 1]

```python
x = np.array([1., 2., 3., 4., 5.])
y = np.array([2., 4., 5., 4., 5.])   # 和x正相关

cov = np.cov(x, y)   # 2×2协方差矩阵
print("协方差矩阵:\n", cov)
# [[2.5,  1.75],
#  [1.75, 1.7 ]]

corr = np.corrcoef(x, y)  # 相关系数矩阵
print("\n相关系数矩阵:\n", corr)
```

**ML中的意义**：特征选择时，高相关性的特征往往冗余；PCA降维就是找协方差矩阵的特征向量。

### 2.4 贝叶斯定理

```
P(A|B) = P(B|A) × P(A) / P(B)
```

- `P(A)` = 先验概率（不看数据，之前就有的信念）
- `P(B|A)` = 似然（在A成立的情况下，观测到B的概率）
- `P(A|B)` = 后验概率（看到数据B后，更新的信念）

**具体例子**：垃圾邮件过滤

```
P(垃圾|含"免费") = P(含"免费"|垃圾) × P(垃圾) / P(含"免费")
```

- 先验：所有邮件中垃圾邮件占20%
- 似然：垃圾邮件里80%含"免费"，正常邮件里10%含"免费"
- 后验：看到"免费"这个词，这封邮件是垃圾的概率？

```python
p_spam = 0.20           # 先验：20%邮件是垃圾
p_free_given_spam = 0.80
p_free_given_ham = 0.10

p_free = p_free_given_spam * p_spam + p_free_given_ham * (1 - p_spam)
p_spam_given_free = (p_free_given_spam * p_spam) / p_free

print(f"P(垃圾|含'免费') = {p_spam_given_free:.3f}")  # 0.667
```

---

## 3. 代码实践

```python
import numpy as np
import matplotlib.pyplot as plt

# ===== 场景：分析CTR数据的统计特性 =====

np.random.seed(42)

# 模拟10000次广告展示，每次记录是否点击和停留时长
n = 10000
click_prob = 0.05                                      # 5% CTR
clicks = np.random.binomial(1, click_prob, n)          # 是否点击
dwell_time = np.random.exponential(scale=30, size=n)   # 停留时长（秒）

# --- 分析1：点击率的置信区间（正态近似）---
ctr = clicks.mean()
se = np.sqrt(ctr * (1 - ctr) / n)                     # 标准误
ci_lower = ctr - 1.96 * se
ci_upper = ctr + 1.96 * se
print(f"CTR: {ctr:.4f}")
print(f"95%置信区间: [{ci_lower:.4f}, {ci_upper:.4f}]")

# --- 分析2：点击用户 vs 未点击用户的停留时长对比 ---
dwell_clicked    = dwell_time[clicks == 1]
dwell_not_clicked = dwell_time[clicks == 0]
print(f"\n点击用户平均停留: {dwell_clicked.mean():.1f}秒")
print(f"未点击用户平均停留: {dwell_not_clicked.mean():.1f}秒")

# --- 分析3：简单的A/B测试（两样本z检验思路）---
# 组A（老算法）vs 组B（新算法）
group_a = np.random.binomial(1, 0.05, 5000)   # 5% CTR
group_b = np.random.binomial(1, 0.06, 5000)   # 6% CTR（新算法提升1%）

ctr_a = group_a.mean()
ctr_b = group_b.mean()

# 计算z统计量
se_ab = np.sqrt(ctr_a*(1-ctr_a)/5000 + ctr_b*(1-ctr_b)/5000)
z = (ctr_b - ctr_a) / se_ab
print(f"\nCTR A: {ctr_a:.4f}, CTR B: {ctr_b:.4f}")
print(f"z统计量: {z:.2f}")
print(f"结论: {'B显著优于A（p<0.05）' if abs(z) > 1.96 else 'B未显著优于A'}")

# --- 分析4：绘制正态分布 ---
x = np.linspace(-4, 4, 200)
pdf = (1 / np.sqrt(2 * np.pi)) * np.exp(-0.5 * x**2)  # 标准正态PDF公式

plt.figure(figsize=(8, 4))
plt.plot(x, pdf, 'b-', linewidth=2)
plt.fill_between(x, pdf, where=(x > -1.96) & (x < 1.96), alpha=0.3, label='95%区间')
plt.title('标准正态分布 N(0,1)')
plt.xlabel('x')
plt.ylabel('概率密度')
plt.legend()
plt.tight_layout()
plt.savefig('normal_distribution.png', dpi=100)
print("\n图已保存为 normal_distribution.png")
```

---

## 练习题

**Q1：计算下面数据集的均值、方差、标准差，并说明哪组数据更分散：**
```
A = [5, 5, 5, 5, 5]
B = [1, 3, 5, 7, 9]
C = [4, 4.5, 5, 5.5, 6]
```

**Q2：用贝叶斯定理解决以下问题：**
一款APP有1%的用户是高价值用户（月消费>1000元）。高价值用户中80%会在7天内二次打开APP，普通用户中只有20%会在7天内二次打开APP。现在某用户在7天内二次打开了APP，请计算他是高价值用户的概率。

**Q3：正态分布的经验法则（68-95-99.7法则）。** 用NumPy生成10000个N(100, 15²)的样本（均值100，标准差15），验证：
- 落在 [85, 115] 区间（均值±1σ）的比例是否接近68%
- 落在 [70, 130] 区间（均值±2σ）的比例是否接近95%

**Q4：计算以下两对特征的相关系数，并解释含义：**
```python
用户年龄 = [22, 25, 28, 31, 35, 40, 45, 50]
消费金额 = [100, 150, 200, 250, 300, 280, 260, 200]
购买次数 = [12, 15, 18, 16, 14, 10, 8, 6]
```

**Q5（工程思考题）：** 在做AB测试时，你的产品经理问你"新算法的CTR从5%提升到5.1%，有统计显著性吗？需要多少样本？"。这个问题叫做**统计功效分析（Power Analysis）**。基于你的经验，这个提升在业务上值得推吗？统计显著性和业务显著性有什么区别？

---

## 明日预告

Day 4：**Pandas — 特征工程的基础工具**
- DataFrame操作：筛选、分组、聚合
- 处理缺失值和异常值
- 用Pandas做基本的特征工程

---

## 答案

### Q1
```python
A = np.array([5., 5., 5., 5., 5.])
B = np.array([1., 3., 5., 7., 9.])
C = np.array([4., 4.5, 5., 5.5, 6.])

for name, arr in [('A', A), ('B', B), ('C', C)]:
    print(f"{name}: 均值={arr.mean()}, 方差={arr.var():.2f}, 标准差={arr.std():.2f}")

# A: 方差=0（完全没有分散）
# B: 方差=8，标准差=2.83（最分散）
# C: 方差=0.5，标准差=0.71（介于AB之间）
```

### Q2
```python
p_high = 0.01                        # 先验：1%是高价值用户
p_reopen_high = 0.80                 # 高价值用户7天内二次打开率
p_reopen_normal = 0.20               # 普通用户7天内二次打开率

p_reopen = p_reopen_high * p_high + p_reopen_normal * (1 - p_high)
p_high_given_reopen = (p_reopen_high * p_high) / p_reopen
print(f"P(高价值 | 7天内再次打开) = {p_high_given_reopen:.4f}")  # ≈ 0.0388 (约3.9%)
```
虽然概率从1%提升到3.9%，但因为先验概率很低（基准率低），即使有正向信号，后验概率也不高。

### Q3
```python
np.random.seed(42)
samples = np.random.normal(100, 15, 10000)

in_1sigma = ((samples >= 85) & (samples <= 115)).mean()  # ≈ 0.683
in_2sigma = ((samples >= 70) & (samples <= 130)).mean()  # ≈ 0.954
print(f"均值±1σ内: {in_1sigma:.3f}（理论: 0.683）")
print(f"均值±2σ内: {in_2sigma:.3f}（理论: 0.954）")
```

### Q4
```python
age = np.array([22., 25., 28., 31., 35., 40., 45., 50.])
spend = np.array([100., 150., 200., 250., 300., 280., 260., 200.])
purchase = np.array([12., 15., 18., 16., 14., 10., 8., 6.])

print(f"年龄-消费相关系数: {np.corrcoef(age, spend)[0,1]:.3f}")    # ≈ 0.74 正相关
print(f"年龄-购买次数相关系数: {np.corrcoef(age, purchase)[0,1]:.3f}")  # ≈ -0.71 负相关
# 解释：年龄增长，消费金额先升后降（相关性强但非线性）
# 购买次数随年龄增长而减少（中年用户消费单价高但频次低）
```

### Q5（参考）
```
统计显著性 ≠ 业务显著性：
- 样本量够大时，0.1%的提升也可能统计显著
- 5% → 5.1%（相对提升2%）是否值得？要考虑：
  - 推全量的开发/维护成本
  - 对其他指标的影响（留存、时长）
  - 这1%在日活1000万的APP上 = 每天多1000次点击，价值多大？

功效分析（需要的样本量估算）：
效应量 = 0.001（绝对差异），显著性水平0.05，功效0.8
约需每组：~260,000 样本
```
