# Phase 1 · Week 1 · Day 1：NumPy入门 — Python科学计算的基础

> 阶段目标：掌握AI工程师的数学工具链
> 今日主题：从Java数组思维转换到NumPy向量化思维

**预计时间**：1.5小时（理论30min + 代码45min + 练习15min）

---

## 1. 工程师视角：为什么需要NumPy

你写Java时处理一组数用 `int[]` 或 `List<Integer>`，做求和要写循环：

```java
// Java
int sum = 0;
for (int x : arr) sum += x;
```

NumPy处理同样的事：

```python
# Python + NumPy
sum = arr.sum()   # 底层是C，比Python循环快100倍
```

这不只是语法糖。ML的核心操作（前向传播、梯度计算）都是**矩阵乘法**，向量化是必须的。一个神经网络层的计算本质是：

```
输出 = 权重矩阵 @ 输入向量 + 偏置
```

所以NumPy是所有ML代码的基础。

---

## 2. NumPy核心概念

### 2.1 ndarray：多维数组

```python
import numpy as np

# 1D数组 = 向量
v = np.array([1, 2, 3])          # shape: (3,)

# 2D数组 = 矩阵
M = np.array([[1, 2], [3, 4]])   # shape: (2, 2)

# 关键属性
print(M.shape)   # (2, 2) — 最重要，每次都要检查
print(M.dtype)   # float64 或 int64
print(M.ndim)    # 2（几维数组）
```

### 2.2 创建数组的常用方式

```python
np.zeros((3, 4))        # 全0矩阵，shape=(3,4)
np.ones((2, 3))         # 全1矩阵
np.eye(4)               # 4×4单位矩阵（对角线是1）
np.random.randn(3, 4)   # 标准正态分布随机矩阵
np.arange(0, 10, 2)     # [0, 2, 4, 6, 8]
np.linspace(0, 1, 5)    # [0.0, 0.25, 0.5, 0.75, 1.0]
```

### 2.3 元素级运算（不需要循环）

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

a + b          # [5, 7, 9]
a * b          # [4, 10, 18]  ← 元素乘，不是矩阵乘！
a ** 2         # [1, 4, 9]
np.sqrt(a)     # [1.0, 1.41, 1.73]
np.exp(a)      # [e^1, e^2, e^3]  ← sigmoid/softmax里用到
```

### 2.4 矩阵乘法（ML最重要的操作）

```python
A = np.array([[1, 2], [3, 4]])  # shape (2,2)
B = np.array([[5, 6], [7, 8]])  # shape (2,2)

C = A @ B   # 矩阵乘法，等价于 np.matmul(A, B)
# C = [[1*5+2*7, 1*6+2*8], [3*5+4*7, 3*6+4*8]]
# C = [[19, 22], [43, 50]]

# 向量点积
v1 = np.array([1, 2, 3])
v2 = np.array([4, 5, 6])
dot = v1 @ v2   # 1*4 + 2*5 + 3*6 = 32
```

**注意 `*` 和 `@` 的区别**：
- `A * B` = 对应位置相乘（Hadamard积）
- `A @ B` = 矩阵乘法

### 2.5 索引与切片

```python
M = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9]])

M[0]        # 第0行：[1, 2, 3]
M[:, 1]     # 第1列：[2, 5, 8]
M[0:2, 1:]  # 子矩阵：[[2,3],[5,6]]

# 布尔索引（很常用）
a = np.array([1, -2, 3, -4, 5])
a[a > 0]    # [1, 3, 5]  只取正数
```

---

## 3. 代码实践

复制下面代码到 Jupyter 或 Python 文件里运行：

```python
import numpy as np

# ===== 场景：推荐系统用户-物品评分矩阵 =====

np.random.seed(42)

# 5个用户，8个物品，评分1-5（0表示未评分）
ratings = np.random.randint(1, 6, size=(5, 8)).astype(float)

# 随机把40%的评分设为0（模拟稀疏矩阵）
mask = np.random.random((5, 8)) < 0.4
ratings[mask] = 0

print("评分矩阵（行=用户，列=物品）：")
print(ratings)
print(f"Shape: {ratings.shape}")

# --- 分析1：每个用户的平均有效评分 ---
# 注意：要排除0（未评分）
for i in range(5):
    row = ratings[i]
    valid = row[row > 0]   # 用布尔索引取非零评分
    mean = valid.mean() if len(valid) > 0 else 0
    print(f"用户{i} 平均评分: {mean:.2f}（有效评分{len(valid)}个）")

# --- 分析2：每个物品被评分次数 ---
item_counts = (ratings > 0).sum(axis=0)  # axis=0 表示按列压缩
print(f"\n各物品被评分次数: {item_counts}")

# --- 分析3：用户0和用户1的点积相似度 ---
u0 = ratings[0]
u1 = ratings[1]
similarity = u0 @ u1
print(f"\nuser0 和 user1 的点积相似度: {similarity:.2f}")
print("（思考：这个相似度度量有什么缺陷？）")

# --- 分析4：矩阵转置 ---
print(f"\n原矩阵 shape: {ratings.shape}")
print(f"转置后 shape: {ratings.T.shape}")
print("（在协同过滤里，转置让我们从'物品视角'看数据）")
```

---

## 练习题

**Q1：创建一个4×4矩阵，只有对角线是[2, 4, 6, 8]，其他位置全是0。**

**Q2：给定两个向量 `a = [1, 2, 3, 4, 5]`，`b = [5, 4, 3, 2, 1]`，计算：**
- 它们的点积（`a · b`）
- 欧氏距离（`||a - b||`）
- 余弦相似度（`(a·b) / (||a|| × ||b||)`）

**Q3：给定矩阵 `M = [[1,2,3],[4,5,6],[7,8,9]]`，用NumPy完成：**
- 取出第2行（索引从0开始）
- 取出第1列
- 取出右下角的2×2子矩阵
- 计算所有元素之和，以及每行的和

**Q4：向量化练习。不要用循环，用NumPy一行代码完成：**
给定数组 `x = np.array([1, -2, 3, -4, 5, -6])`：
- 将所有负数替换为0（ReLU激活函数！）
- 计算数组中正数的平均值

**Q5（工程思考题）：** 在推荐系统里，用户-物品评分矩阵通常有1亿用户 × 1000万物品。如果用NumPy的稠密矩阵存储，需要多少内存（float32，4字节）？这在工程上可行吗？

---

## 明日预告

Day 2：**矩阵运算深入 — 线性变换、特征值、SVD**
- 矩阵乘法的几何意义（变换空间）
- 特征值/特征向量：为什么推荐系统用SVD？
- 用NumPy做矩阵分解

---

## 答案

### Q1
```python
np.diag([2, 4, 6, 8])
```

### Q2
```python
a = np.array([1, 2, 3, 4, 5], dtype=float)
b = np.array([5, 4, 3, 2, 1], dtype=float)

dot = a @ b                                          # 35.0
euclidean = np.linalg.norm(a - b)                   # sqrt((4²+2²+0²+2²+4²)) = sqrt(40) ≈ 6.32
cosine = (a @ b) / (np.linalg.norm(a) * np.linalg.norm(b))  # 35 / (√55 × √55) = 35/55 ≈ 0.636
```

### Q3
```python
M = np.array([[1,2,3],[4,5,6],[7,8,9]])

M[2]          # [7, 8, 9]
M[:, 1]       # [2, 5, 8]
M[1:, 1:]     # [[5,6],[8,9]]
M.sum()       # 45
M.sum(axis=1) # [6, 15, 24]
```

### Q4
```python
x = np.array([1, -2, 3, -4, 5, -6])

relu = np.maximum(x, 0)      # [1, 0, 3, 0, 5, 0]
pos_mean = x[x > 0].mean()   # (1+3+5)/3 = 3.0
```

### Q5（参考答案）
```
存储量 = 1亿 × 1000万 × 4字节 = 4 × 10^15 字节 = 4 PB
```
完全不可行。实际工程方案：
- 用**稀疏矩阵**（`scipy.sparse`），只存非零值，评分矩阵通常99%是空的
- 用**分布式存储**（HBase、Redis）存稀疏特征
- 用**矩阵分解**（SVD/ALS）把大矩阵压缩成两个小矩阵相乘
