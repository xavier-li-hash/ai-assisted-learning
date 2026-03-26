# Phase 1 · Week 1 · Day 2：矩阵运算深入 — 线性变换与SVD

> 今日主题：矩阵不只是"二维数组"，它是一种变换；SVD是推荐系统矩阵分解的核心

**预计时间**：1.5小时（理论30min + 代码45min + 练习15min）

---

## 1. 矩阵的几何意义：变换空间

矩阵乘法的本质是**对向量做线性变换**（旋转、缩放、投影）。

举个例子：把向量 `[1, 0]` 乘以矩阵 `[[2, 0], [0, 3]]`：

```
[[2, 0],   ×   [1]   =   [2]
 [0, 3]]       [0]        [0]
```

结果是把x方向拉伸2倍，y方向拉伸3倍。矩阵的每一列告诉你，基向量（x轴、y轴）被变换到哪里了。

**在ML里的意义**：
- 神经网络的权重矩阵 = 把输入空间变换到特征空间
- Embedding矩阵 = 把离散ID映射到连续语义空间
- 推荐系统的矩阵分解 = 在低维空间里找用户和物品的隐含特征

---

## 2. 关键操作

### 2.1 转置

```python
import numpy as np

A = np.array([[1, 2, 3],
              [4, 5, 6]])   # shape (2, 3)

A.T   # shape (3, 2)
# [[1, 4],
#  [2, 5],
#  [3, 6]]
```

**重要性质**：`(AB)^T = B^T A^T`（矩阵乘法转置要反序）

在ML里，转置决定了矩阵乘法能否成立：`(m×k) @ (k×n) → (m×n)`

### 2.2 逆矩阵

```python
A = np.array([[2., 1.], [5., 3.]])

A_inv = np.linalg.inv(A)
print(A_inv)

# 验证：A @ A_inv ≈ 单位矩阵
print(A @ A_inv)   # 应该接近 [[1,0],[0,1]]
```

**工程注意**：实际ML中很少直接求逆（计算量大，数值不稳定）。求解方程组用 `np.linalg.solve`。

### 2.3 特征值与特征向量

某些特殊向量 `v`，被矩阵 `A` 变换后，方向不变，只是被缩放了：

```
A @ v = λ × v
```

- `v` 是**特征向量**（eigenvector）
- `λ` 是**特征值**（eigenvalue）

```python
A = np.array([[4., 1.],
              [2., 3.]])

eigenvalues, eigenvectors = np.linalg.eig(A)
print("特征值:", eigenvalues)       # [5. 2.]
print("特征向量:\n", eigenvectors)   # 每列是一个特征向量
```

**直觉理解**：特征向量是矩阵变换下"方向不变"的轴，特征值是这个方向被拉伸的倍数。在PCA（降维）里，最大特征值对应的特征向量就是数据方差最大的方向。

### 2.4 SVD（奇异值分解）— 推荐系统的核心

任意矩阵 `M`（不需要是方阵）都可以分解为：

```
M = U Σ V^T
```

- `U`：用户特征矩阵（m×k）
- `Σ`：奇异值对角矩阵（k×k，重要性权重）
- `V^T`：物品特征矩阵的转置（k×n）

```python
# 用户-物品评分矩阵（4用户，5物品）
M = np.array([[4, 3, 0, 1, 0],
              [5, 0, 4, 0, 2],
              [0, 3, 0, 4, 4],
              [1, 1, 0, 5, 0]], dtype=float)

U, s, Vt = np.linalg.svd(M, full_matrices=False)

print(f"U shape: {U.shape}")    # (4, 4) 用户的隐因子
print(f"s shape: {s.shape}")    # (4,)   奇异值（按重要性排序）
print(f"Vt shape: {Vt.shape}")  # (4, 5) 物品的隐因子

print(f"\n奇异值: {s.round(2)}")
print("（奇异值越大，代表这个隐含维度越重要）")

# 用低秩近似（只保留前k=2个奇异值）来压缩矩阵
k = 2
M_approx = U[:, :k] @ np.diag(s[:k]) @ Vt[:k, :]
print(f"\n原矩阵:\n{M}")
print(f"\n低秩近似（k=2）:\n{M_approx.round(2)}")
```

**在推荐系统里的意义**：
- 原矩阵：4用户 × 5物品，稀疏且难以直接用于预测
- SVD分解后：每个用户和物品都被表示为k维隐向量（用户的兴趣、物品的特征）
- 对没有评分的位置预测：`user_vector @ item_vector`

---

## 3. 代码实践

```python
import numpy as np

# ===== 场景：用SVD做简单的协同过滤 =====

# 用户-物品评分矩阵（0表示未评分）
ratings = np.array([
    [5, 3, 0, 1],   # 用户0
    [4, 0, 4, 1],   # 用户1
    [1, 1, 0, 5],   # 用户2
    [0, 0, 5, 4],   # 用户3
    [0, 1, 5, 4],   # 用户4
], dtype=float)

print("原始评分矩阵:")
print(ratings)

# Step 1: SVD分解（取前k=2个隐因子）
U, s, Vt = np.linalg.svd(ratings, full_matrices=False)
k = 2

U_k  = U[:, :k]       # 用户隐因子 (5, 2)
s_k  = np.diag(s[:k]) # 奇异值对角矩阵 (2, 2)
Vt_k = Vt[:k, :]      # 物品隐因子 (2, 4)

print(f"\n用户隐因子 U_k (shape {U_k.shape}):")
print(U_k.round(3))

print(f"\n物品隐因子 Vt_k (shape {Vt_k.shape}):")
print(Vt_k.round(3))

# Step 2: 重建评分矩阵（包含对缺失值的预测）
predicted = U_k @ s_k @ Vt_k
print(f"\n预测评分矩阵:")
print(predicted.round(2))

# Step 3: 找出用户0未评分但预测分高的物品
user0_original = ratings[0]
user0_predicted = predicted[0]
unrated_mask = user0_original == 0
print(f"\n用户0的原始评分: {user0_original}")
print(f"用户0的预测评分: {user0_predicted.round(2)}")
print(f"用户0未评分的物品预测分: {user0_predicted[unrated_mask].round(2)}")
```

---

## 练习题

**Q1：验证矩阵乘法结合律。给定：**
```
A = [[1,2],[3,4]], B = [[5,6],[7,8]], C = [[1,0],[0,1]]
```
验证 `(A @ B) @ C == A @ (B @ C)`，并解释单位矩阵在这里的作用。

**Q2：给定矩阵 `A = [[3,1],[1,3]]`，手算预测一下它的特征值（提示：对称矩阵的特征值是实数），然后用NumPy验证。**

**Q3：shape运算练习。判断以下矩阵乘法是否合法，如果合法写出结果的shape：**
- `(3,4) @ (4,5)` → ?
- `(3,4) @ (3,4)` → ?
- `(1,4) @ (4,1)` → ?
- `(100,) @ (100,)` → ?（两个1D向量）

**Q4：用SVD压缩图像。** 图像可以看作一个矩阵（像素值）。用以下代码生成一个"假图像"，然后用k=5的低秩SVD近似，计算压缩比和近似误差：
```python
np.random.seed(0)
image = np.random.randn(50, 50)
```

**Q5（工程思考题）：** 在实际推荐系统中，SVD有一个明显的工程缺陷：新用户和新物品加入后，需要重新对整个矩阵做SVD（重新训练）。这叫做"冷启动问题"。你在做推荐系统时遇到过这个问题吗？工程上是怎么解决的？

---

## 明日预告

Day 3：**概率统计基础 — 所有ML模型的统计根基**
- 概率分布（正态分布、伯努利分布）
- 期望、方差、协方差的计算
- 贝叶斯定理：用代码理解

---

## 答案

### Q1
```python
A = np.array([[1,2],[3,4]])
B = np.array([[5,6],[7,8]])
C = np.eye(2)

print(np.allclose((A @ B) @ C, A @ (B @ C)))  # True
# 单位矩阵是矩阵乘法的"1"，乘上它不改变结果
```

### Q2
```python
A = np.array([[3., 1.], [1., 3.]])
eigenvalues, eigenvectors = np.linalg.eig(A)
# 特征值：[4., 2.]
# 手算：det(A - λI) = (3-λ)² - 1 = 0 → λ = 4 或 λ = 2
```

### Q3
- `(3,4) @ (4,5)` → **(3,5)** ✓（内维度匹配：4=4）
- `(3,4) @ (3,4)` → 不合法 ✗（4 ≠ 3）
- `(1,4) @ (4,1)` → **(1,1)** ✓
- `(100,) @ (100,)` → **标量** ✓（1D向量点积，返回一个数）

### Q4
```python
np.random.seed(0)
image = np.random.randn(50, 50)

U, s, Vt = np.linalg.svd(image, full_matrices=False)
k = 5
image_approx = U[:, :k] @ np.diag(s[:k]) @ Vt[:k, :]

# 压缩比：原来存 50*50=2500个数，压缩后存 50*k + k + k*50 = 505个数
compression_ratio = (50 * 50) / (50*k + k + k*50)  # ≈ 2.48x

# 近似误差（Frobenius范数）
error = np.linalg.norm(image - image_approx, 'fro')
print(f"压缩比: {compression_ratio:.2f}x, 误差: {error:.4f}")
```

### Q5（参考）
常见工程解法：
- **实时特征**：新用户用规则/内容相似度冷启动，不依赖SVD
- **增量更新**：用SGD/ALS做在线更新，不每次全量重算
- **两阶段**：第一阶段热门推荐（规则），积累行为数据后切换协同过滤
