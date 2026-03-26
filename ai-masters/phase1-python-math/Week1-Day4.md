# Phase 1 · Week 1 · Day 4：Pandas — 特征工程的基础工具

> 今日主题：ML工程师花40%时间在数据处理上，Pandas是这个工作的核心工具

**预计时间**：1.5小时（理论30min + 代码45min + 练习15min）

---

## 1. 工程师视角：DataFrame是什么

你做推荐系统时，用户特征可能存在MySQL表里：

```sql
SELECT user_id, age, login_days, total_spend FROM user_features;
```

Pandas的DataFrame就是"内存里的SQL表"：
- 有列名（特征名）
- 有索引（行号或ID）
- 支持SQL风格的筛选、分组、聚合
- 比SQL更灵活，能做NumPy数学运算

ML流程里，Pandas负责：数据加载 → 清洗 → 特征工程 → 输出给sklearn/PyTorch

---

## 2. Pandas核心操作

### 2.1 创建和加载DataFrame

```python
import pandas as pd
import numpy as np

# 从字典创建
df = pd.DataFrame({
    'user_id': [1, 2, 3, 4, 5],
    'age':     [25, 32, 28, 45, 23],
    'gender':  ['M', 'F', 'M', 'F', 'M'],
    'login_days': [10, 5, 20, 3, 15],
    'total_spend': [500.0, 200.0, np.nan, 1200.0, 0.0]
})

# 从CSV加载（实际工作最常用）
# df = pd.read_csv('user_features.csv')

print(df)
print(f"\nShape: {df.shape}")      # (5, 5)
print(df.dtypes)                    # 每列的数据类型
print(df.describe())                # 数值列的统计摘要
```

### 2.2 选取和筛选

```python
# 选列（返回Series）
df['age']
df[['age', 'gender']]    # 多列，返回DataFrame

# 按条件筛选（类似SQL WHERE）
df[df['age'] > 28]
df[(df['age'] > 25) & (df['gender'] == 'M')]

# loc（按标签）和 iloc（按位置）
df.loc[0:2, 'age':'gender']    # 行0-2，age到gender列
df.iloc[0:2, 1:3]              # 行索引0-1，列索引1-2
```

### 2.3 处理缺失值

```python
# 检查缺失
df.isnull().sum()       # 每列缺失数量
df.isnull().mean()      # 每列缺失率

# 填充缺失（ML中必须处理，模型不接受NaN）
df['total_spend'].fillna(0, inplace=True)                      # 填0
df['total_spend'].fillna(df['total_spend'].median(), inplace=True)  # 填中位数
df.dropna(inplace=True)                                        # 删除含NaN的行
```

**策略选择**：
- 数值特征：填均值（正态分布）或中位数（有异常值）
- 类别特征：填众数或"unknown"
- 时序特征：前向填充（用上一个有效值）

### 2.4 分组聚合（GroupBy）

```python
# 类似SQL的 GROUP BY
df.groupby('gender')['total_spend'].mean()

# 多个聚合函数
df.groupby('gender').agg({
    'total_spend': ['mean', 'median', 'sum'],
    'login_days': 'mean'
})

# 这就是特征工程里的"用户分群统计"
```

### 2.5 特征工程常用操作

```python
# 1. 新建特征（列运算）
df['spend_per_day'] = df['total_spend'] / (df['login_days'] + 1)

# 2. 标准化（Z-score）
from sklearn.preprocessing import StandardScaler
# 或手动：
df['age_normalized'] = (df['age'] - df['age'].mean()) / df['age'].std()

# 3. One-Hot编码（类别特征转数值）
df_encoded = pd.get_dummies(df, columns=['gender'])
# gender_M和gender_F变成0/1列

# 4. 分箱（连续特征转离散）
df['age_group'] = pd.cut(df['age'], bins=[0, 25, 35, 50, 100],
                          labels=['<25', '25-35', '35-50', '50+'])
```

---

## 3. 代码实践

```python
import pandas as pd
import numpy as np

# ===== 场景：用户行为数据的特征工程 =====

np.random.seed(42)
n = 1000

# 生成模拟用户数据
df = pd.DataFrame({
    'user_id':       range(1000, 1000 + n),
    'age':           np.random.randint(18, 60, n),
    'gender':        np.random.choice(['M', 'F'], n),
    'login_days_30': np.random.randint(0, 31, n),
    'total_spend':   np.random.exponential(200, n).round(2),
    'item_views':    np.random.randint(0, 500, n),
    'purchases':     np.random.randint(0, 20, n),
})

# 随机引入10%的缺失值
df.loc[np.random.choice(n, 100, replace=False), 'total_spend'] = np.nan
df.loc[np.random.choice(n, 50, replace=False), 'age'] = np.nan

print("原始数据概览:")
print(df.head())
print(f"\n缺失值统计:\n{df.isnull().sum()}")

# --- Step 1：缺失值处理 ---
df['total_spend'].fillna(df['total_spend'].median(), inplace=True)
df['age'].fillna(df['age'].median(), inplace=True)
print("\n处理缺失值后，缺失数:", df.isnull().sum().sum())

# --- Step 2：派生特征 ---
df['conversion_rate'] = df['purchases'] / (df['item_views'] + 1)
df['spend_per_login']  = df['total_spend'] / (df['login_days_30'] + 1)
df['is_active']        = (df['login_days_30'] >= 15).astype(int)

# --- Step 3：分组统计特征 ---
gender_stats = df.groupby('gender')['total_spend'].agg(['mean', 'median'])
print("\n不同性别消费统计:")
print(gender_stats)

# --- Step 4：年龄分箱 ---
df['age_group'] = pd.cut(df['age'],
                          bins=[0, 25, 35, 45, 100],
                          labels=['<25', '25-35', '35-45', '45+'])
print("\n年龄分布:")
print(df['age_group'].value_counts())

# --- Step 5：One-Hot编码 ---
df_final = pd.get_dummies(df, columns=['gender', 'age_group'])
numeric_cols = ['login_days_30', 'total_spend', 'item_views',
                'purchases', 'conversion_rate', 'spend_per_login', 'is_active']

print(f"\n特征工程完成，最终特征矩阵 shape: {df_final[numeric_cols].shape}")
print("准备输入ML模型...")

# 导出为NumPy数组（sklearn/PyTorch所需的格式）
X = df_final[numeric_cols].values
print(f"X shape: {X.shape}, dtype: {X.dtype}")
```

---

## 练习题

**Q1：读取并探索数据。** 用以下代码生成数据，然后完成：
```python
np.random.seed(0)
data = pd.DataFrame({
    'score': np.random.randint(0, 100, 200),
    'category': np.random.choice(['A', 'B', 'C'], 200),
    'value': np.where(np.random.random(200) > 0.1,
                      np.random.normal(50, 10, 200), np.nan)
})
```
- 查看 `data.describe()` 的输出，解释各行的含义
- 统计 `category` 列中每个类别的数量
- 找出 `score` 大于90的所有行

**Q2：缺失值处理。** 对上面数据的 `value` 列：
- 计算缺失率（百分比）
- 用均值填充，和用中位数填充，各自填充后再次调用 `describe()`，对比有什么不同

**Q3：特征工程。** 对上面的 `data`：
- 新建一列 `score_level`：score < 60 为"低"，60-80 为"中"，> 80 为"高"（用 `pd.cut` 或 `np.select`）
- 计算每个 `category` 的平均 `score`（用 `groupby`）
- 对 `category` 做One-Hot编码

**Q4：数据合并。** 给定两个DataFrame：
```python
users = pd.DataFrame({'user_id': [1,2,3,4], 'name': ['A','B','C','D']})
orders = pd.DataFrame({'user_id': [1,1,2,3,5], 'amount': [100,200,150,300,50]})
```
- 做 `inner join`（只保留两边都有的用户）
- 做 `left join`（保留所有用户，即使没有订单）
- 计算每个用户的总消费金额（包括没有订单的用户，金额为0）

**Q5（工程思考题）：** 在实际特征工程中，One-Hot编码有一个问题：如果某个类别特征有1000个取值（如城市），One-Hot会生成1000列，导致维度爆炸。你在推荐系统工程中是怎么处理高基数类别特征的？

---

## 明日预告

Day 5：**综合实战 + 周总结**
- 用本周所学（NumPy + Pandas + 概率统计）做一个完整的用户画像分析
- 从数据清洗到特征工程到初步统计洞察
- 周复习检查表

---

## 答案

### Q1
```python
# describe的各行含义：
# count: 非空值数量
# mean: 均值
# std: 标准差
# min/max: 最小/最大值
# 25%/50%/75%: 四分位数

data['category'].value_counts()                    # 各类别数量
data[data['score'] > 90]                           # score>90的行
```

### Q2
```python
missing_rate = data['value'].isnull().mean() * 100
print(f"缺失率: {missing_rate:.1f}%")

# 均值填充
data_mean = data.copy()
data_mean['value'].fillna(data_mean['value'].mean(), inplace=True)

# 中位数填充
data_median = data.copy()
data_median['value'].fillna(data_median['value'].median(), inplace=True)

# 当数据分布有长尾/异常值时，中位数填充更稳健
```

### Q3
```python
# score_level（用pd.cut）
data['score_level'] = pd.cut(data['score'],
                              bins=[-1, 59, 80, 100],
                              labels=['低', '中', '高'])

# 每个category的平均score
data.groupby('category')['score'].mean()

# One-Hot编码
pd.get_dummies(data, columns=['category'])
```

### Q4
```python
users = pd.DataFrame({'user_id': [1,2,3,4], 'name': ['A','B','C','D']})
orders = pd.DataFrame({'user_id': [1,1,2,3,5], 'amount': [100,200,150,300,50]})

# Inner join（user_id=5的订单被丢弃，user_id=4没有订单也被丢弃）
inner = pd.merge(users, orders, on='user_id', how='inner')

# Left join（user_id=4保留，amount=NaN）
left = pd.merge(users, orders, on='user_id', how='left')

# 每个用户总消费（含无订单用户金额为0）
total = left.groupby(['user_id', 'name'])['amount'].sum().fillna(0).reset_index()
```

### Q5（参考）
高基数类别特征的处理方式：
1. **Target Encoding（目标编码）**：用该类别的目标变量均值替换（如城市平均CTR）
2. **Embedding（嵌入层）**：深度学习里把类别ID映射到低维向量（最常用）
3. **Frequency Encoding（频率编码）**：用类别出现频率替换
4. **Hash Trick（哈希技巧）**：把类别哈希到固定维度的向量，牺牲一点精度换速度
5. **Top-N截断**：只保留出现频率最高的N个类别，其余归为"其他"
