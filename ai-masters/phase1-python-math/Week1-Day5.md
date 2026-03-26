# Phase 1 · Week 1 · Day 5：综合实战 + 周总结

> 今日主题：用本周所有工具（NumPy + Pandas + 概率统计 + 可视化）做一个完整的数据分析项目

**预计时间**：2小时（项目实战60min + 可视化30min + 周总结20min + 自测10min）

---

## 本周学习回顾

| 天 | 主题 | 核心技能 |
|----|------|---------|
| Day 1 | NumPy入门 | 数组创建、向量化运算、矩阵乘法 |
| Day 2 | 矩阵运算 | 特征值、SVD、低秩近似 |
| Day 3 | 概率统计 | 分布、期望方差、贝叶斯定理 |
| Day 4 | Pandas | 数据清洗、特征工程、GroupBy |
| Day 5 | 综合实战 | 把以上全部串联起来 |

---

## 综合项目：音频直播用户价值分析

> 场景背景：你在荔枝做推荐系统，现在需要对用户进行价值分析和分层，为差异化运营提供数据支持。

### 任务目标
1. 加载并清洗用户行为数据
2. 做特征工程（派生特征 + 统计特征）
3. 用统计方法识别高价值用户
4. 用矩阵运算做用户相似度计算
5. 可视化关键发现

---

## 完整代码

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib
matplotlib.rcParams['font.sans-serif'] = ['SimHei', 'Arial Unicode MS']
matplotlib.rcParams['axes.unicode_minus'] = False

# ==========================================
# Step 1：生成模拟数据
# ==========================================

np.random.seed(42)
n = 2000

# 模拟直播平台用户行为（30天数据）
df = pd.DataFrame({
    'user_id':       range(10001, 10001 + n),
    'age':           np.random.randint(18, 55, n),
    'gender':        np.random.choice(['M', 'F'], n, p=[0.55, 0.45]),
    'city_tier':     np.random.choice([1, 2, 3], n, p=[0.3, 0.4, 0.3]),

    # 行为特征
    'listen_hours':  np.random.exponential(5, n).round(1),      # 收听时长
    'send_gifts':    np.random.poisson(3, n),                   # 送礼次数
    'gift_amount':   np.random.exponential(50, n).round(2),     # 送礼金额
    'follow_count':  np.random.randint(0, 50, n),               # 关注主播数
    'login_days':    np.random.randint(1, 31, n),               # 登录天数
    'comments':      np.random.poisson(8, n),                   # 评论次数
})

# 引入缺失值
df.loc[np.random.choice(n, 80, replace=False), 'gift_amount'] = np.nan
df.loc[np.random.choice(n, 40, replace=False), 'age'] = np.nan

print("=" * 50)
print("Step 1: 数据概览")
print("=" * 50)
print(f"数据形状: {df.shape}")
print(f"\n缺失值:\n{df.isnull().sum()}")
print(f"\n基本统计:\n{df.describe().round(2)}")


# ==========================================
# Step 2：数据清洗
# ==========================================

print("\n" + "=" * 50)
print("Step 2: 数据清洗")
print("=" * 50)

# 填充缺失值
df['gift_amount'].fillna(df['gift_amount'].median(), inplace=True)
df['age'].fillna(df['age'].median(), inplace=True)

# 处理异常值：gift_amount超过99分位数的截断
q99 = df['gift_amount'].quantile(0.99)
df['gift_amount'] = df['gift_amount'].clip(upper=q99)
print(f"礼物金额99分位数: {q99:.2f}，已截断异常值")


# ==========================================
# Step 3：特征工程
# ==========================================

print("\n" + "=" * 50)
print("Step 3: 特征工程")
print("=" * 50)

# 派生特征
df['avg_daily_hours']    = df['listen_hours'] / df['login_days']
df['gift_per_listen']    = df['gift_amount'] / (df['listen_hours'] + 0.1)
df['engagement_score']   = (df['comments'] + df['send_gifts'] * 3) / (df['login_days'] + 1)
df['loyalty_score']      = df['login_days'] / 30.0    # 登录活跃度

# 用户价值打分（简单加权）
df['ltv_score'] = (
    0.4 * (df['gift_amount'] / df['gift_amount'].max()) +
    0.3 * df['loyalty_score'] +
    0.2 * (df['listen_hours'] / df['listen_hours'].max()) +
    0.1 * (df['engagement_score'] / df['engagement_score'].max())
)

# 用户分层（基于LTV分数分位数）
df['user_tier'] = pd.qcut(df['ltv_score'], q=4,
                            labels=['低价值', '一般', '中高', '高价值'])

print("用户分层分布:")
print(df['user_tier'].value_counts())


# ==========================================
# Step 4：统计分析（用概率统计知识）
# ==========================================

print("\n" + "=" * 50)
print("Step 4: 各层用户统计特征")
print("=" * 50)

tier_stats = df.groupby('user_tier').agg({
    'gift_amount':     ['mean', 'median'],
    'listen_hours':    'mean',
    'loyalty_score':   'mean',
    'engagement_score':'mean',
}).round(3)
print(tier_stats)

# 高价值用户 vs 普通用户的LTV差距
high_value  = df[df['user_tier'] == '高价值']['gift_amount']
low_value   = df[df['user_tier'] == '低价值']['gift_amount']
print(f"\n高价值用户平均礼物金额: {high_value.mean():.2f}")
print(f"低价值用户平均礼物金额: {low_value.mean():.2f}")
print(f"倍数差距: {high_value.mean() / low_value.mean():.1f}x")


# ==========================================
# Step 5：矩阵运算——用户相似度（NumPy）
# ==========================================

print("\n" + "=" * 50)
print("Step 5: 用户相似度（余弦相似度）")
print("=" * 50)

# 取数值特征，计算前10个用户的两两相似度
feature_cols = ['listen_hours', 'gift_amount', 'follow_count',
                'login_days', 'comments']
X = df[feature_cols].values[:10].astype(float)

# 标准化（每行除以L2范数）
norms = np.linalg.norm(X, axis=1, keepdims=True)
X_normalized = X / (norms + 1e-8)

# 余弦相似度矩阵 = X_normalized @ X_normalized.T
sim_matrix = X_normalized @ X_normalized.T
print("前10用户的余弦相似度矩阵:")
print(sim_matrix.round(3))
print("\n（对角线是1.0，表示自己和自己的相似度）")


# ==========================================
# Step 6：可视化
# ==========================================

fig, axes = plt.subplots(2, 2, figsize=(12, 8))
fig.suptitle('直播平台用户价值分析', fontsize=14)

# 图1：用户LTV分数分布
axes[0, 0].hist(df['ltv_score'], bins=50, color='steelblue', alpha=0.7)
axes[0, 0].set_title('用户LTV分数分布')
axes[0, 0].set_xlabel('LTV Score')
axes[0, 0].set_ylabel('用户数')

# 图2：各层用户送礼金额箱线图
df.boxplot(column='gift_amount', by='user_tier', ax=axes[0, 1])
axes[0, 1].set_title('各层用户送礼金额分布')
axes[0, 1].set_xlabel('用户分层')
axes[0, 1].set_ylabel('礼物金额')

# 图3：收听时长 vs 送礼金额 散点图（按分层着色）
colors = {'低价值': 'gray', '一般': 'blue', '中高': 'orange', '高价值': 'red'}
for tier, color in colors.items():
    subset = df[df['user_tier'] == tier]
    axes[1, 0].scatter(subset['listen_hours'], subset['gift_amount'],
                       c=color, label=tier, alpha=0.3, s=10)
axes[1, 0].set_title('收听时长 vs 送礼金额')
axes[1, 0].set_xlabel('收听时长（小时）')
axes[1, 0].set_ylabel('送礼金额')
axes[1, 0].legend()

# 图4：城市等级 × 用户分层 热力图
cross_tab = pd.crosstab(df['city_tier'], df['user_tier'], normalize='index')
im = axes[1, 1].imshow(cross_tab.values, cmap='Blues', aspect='auto')
axes[1, 1].set_xticks(range(len(cross_tab.columns)))
axes[1, 1].set_xticklabels(cross_tab.columns)
axes[1, 1].set_yticks(range(len(cross_tab.index)))
axes[1, 1].set_yticklabels([f'一线({t})' if t==1 else f'二线({t})' if t==2 else f'三线({t})' for t in cross_tab.index])
axes[1, 1].set_title('城市等级 × 用户分层分布')
plt.colorbar(im, ax=axes[1, 1])

plt.tight_layout()
plt.savefig('user_value_analysis.png', dpi=100, bbox_inches='tight')
print("\n可视化结果已保存为 user_value_analysis.png")
```

---

## 周总结自测

完成以下自测（不看答案，靠记忆回答）：

**概念检查（用自己的话解释）：**

- [ ] **NumPy广播**：`(3,4) + (4,)` 为什么可以相加？结果shape是什么？
- [ ] **矩阵乘法**：`@` 和 `*` 的区别是什么？各用在什么场景？
- [ ] **SVD分解**：`M = U Σ V^T` 中，U、Σ、V分别代表什么？在推荐系统里对应什么？
- [ ] **标准差 vs 方差**：你更常用哪个？为什么？
- [ ] **贝叶斯定理**：先验、似然、后验分别是什么？用一句话解释。
- [ ] **One-Hot编码的问题**：为什么不能直接用字符串类别？高基数情况下怎么办？

**代码检查（不看代码，能默写出来吗）：**

- [ ] 用NumPy创建一个5×5单位矩阵：`_______`
- [ ] 用布尔索引取出数组中大于0的元素：`_______`
- [ ] 计算两向量的余弦相似度（三行以内）：`_______`
- [ ] 用Pandas填充某列的缺失值为中位数：`_______`
- [ ] 用groupby计算每个类别的均值：`_______`

---

## 下周预告：Week 2 — 线性代数核心

| 天 | 主题 |
|----|------|
| Day 1 | 向量空间与线性变换（几何直觉） |
| Day 2 | 矩阵的秩·行列式·迹 |
| Day 3 | 正定矩阵·协方差矩阵·PCA |
| Day 4 | 梯度·雅可比矩阵（反向传播的前置） |
| Day 5 | 综合实战：用NumPy实现PCA降维 |

---

## 答案

### 自测概念参考答案

**NumPy广播**：`(3,4) + (4,)` — 形状为 `(4,)` 的向量自动扩展为 `(3,4)`，每行都加这个向量，结果shape是 `(3,4)`。规则：从右往左对齐，维度为1或缺失的可以广播。

**矩阵乘法**：`@` 是矩阵乘法（行×列求和），`*` 是元素对应相乘（Hadamard积）。`@` 用于神经网络层运算，`*` 用于逐元素缩放/掩码。

**SVD分解**：U是用户/行的隐因子矩阵，Σ是奇异值（重要性权重），V是物品/列的隐因子矩阵。在推荐系统里：用户偏好 = U矩阵，物品特征 = V矩阵，Σ表示每个隐含特征维度的重要性。

**标准差 vs 方差**：更常用标准差，因为它和原始数据同单位（方差是平方单位）。方差在数学推导（计算梯度）时更方便。

**贝叶斯定理**：`P(假设|数据) = P(数据|假设) × P(假设) / P(数据)`。先验是"没看数据前的信念"，似然是"假设成立时看到这个数据的概率"，后验是"看到数据后更新的信念"。

**One-Hot编码问题**：ML模型接受数字不接受字符串；高基数用embedding映射到低维稠密向量。

### 代码默写参考
```python
np.eye(5)                                              # 单位矩阵
arr[arr > 0]                                           # 布尔索引
(a @ b) / (np.linalg.norm(a) * np.linalg.norm(b))    # 余弦相似度
df['col'].fillna(df['col'].median(), inplace=True)    # 填充中位数
df.groupby('cat')['val'].mean()                        # groupby均值
```
