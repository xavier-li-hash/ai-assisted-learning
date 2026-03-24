# AI硕士 Week 1 - Day 1：监督学习框架 + 线性回归

> 目标：建立ML的核心框架认知，用工程师视角理解"模型训练"是什么
> 前置：Python + NumPy已安装（`pip install numpy pandas scikit-learn matplotlib`）

---

## 工程师视角：ML是什么

你已经理解：
- 推荐系统 = 预测用户对物品的兴趣分数
- 标签系统 = 把用户分类到某个标签

**ML本质**：从数据中学习一个函数 `f`，使得 `f(输入特征) ≈ 目标值`

```
训练阶段：给定 (X, y)，学习参数 θ，使得 f(X; θ) ≈ y
推理阶段：给定新的 X，用学好的 θ 预测 y
```

类比你熟悉的系统：
- X = 用户特征（年龄、行为、标签）
- y = 点击率/留存率/交易量
- θ = 模型参数（你在调优的那些系数）

---

## 线性回归：最简单的监督学习

**公式**：`y = θ₀ + θ₁x₁ + θ₂x₂ + ... + θₙxₙ`

**目标**：找到一组 θ，让预测值和真实值差距最小。

**损失函数（MSE）**：
```
L(θ) = (1/n) * Σ(y_pred - y_true)²
```

---

## 今日代码实践

### 任务：预测用户活跃度分数

假设你有用户数据，要预测他们的7日留存率：

```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score

# 模拟用户特征数据（类似你在荔枝做的画像）
np.random.seed(42)
n_users = 1000

# 特征：登录天数、消费金额、好友数
login_days = np.random.randint(1, 30, n_users)
spend_amount = np.random.uniform(0, 1000, n_users)
friend_count = np.random.randint(0, 200, n_users)

# 目标：7日留存率（0-1之间）
# 真实关系：留存率和登录天数、好友数正相关，消费适度相关
retention = (
    0.3 * (login_days / 30) +
    0.4 * (friend_count / 200) +
    0.2 * (spend_amount / 1000) +
    np.random.normal(0, 0.05, n_users)  # 噪声
)
retention = np.clip(retention, 0, 1)

# 组装特征矩阵
X = np.column_stack([login_days, spend_amount, friend_count])
y = retention

# 划分训练集和测试集（8:2）
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 训练模型
model = LinearRegression()
model.fit(X_train, y_train)

# 预测
y_pred = model.predict(X_test)

# 评估
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

print(f"MSE（均方误差）: {mse:.4f}")
print(f"R²（解释方差）: {r2:.4f}")
print(f"\n模型系数:")
print(f"  登录天数影响: {model.coef_[0]:.4f}")
print(f"  消费金额影响: {model.coef_[1]:.6f}")
print(f"  好友数影响:   {model.coef_[2]:.4f}")
print(f"  截距: {model.intercept_:.4f}")
```

**运行后思考以下问题：**

---

## 理解性问题（写下你的答案）

**Q1：R²是0.XX，这意味着什么？**
```
（你的答案）
```

**Q2：登录天数的系数是正数还是负数？这符合你在推荐系统里的直觉吗？**
```
（你的答案）
```

**Q3：如果我们把消费金额从 0–1000 变成 0–1（归一化），系数会怎么变？为什么？**
```
（你的答案）
```

---

## 手推梯度下降（重要！）

sklearn帮你做了数学，但你需要理解背后发生了什么：

```python
# 手动实现梯度下降（只用NumPy）
def gradient_descent(X, y, learning_rate=0.001, epochs=1000):
    n, m = X.shape
    theta = np.zeros(m + 1)  # m个特征系数 + 1个截距

    # 在X前加一列1（对应截距）
    X_b = np.column_stack([np.ones(n), X])

    loss_history = []

    for epoch in range(epochs):
        # 预测
        y_pred = X_b @ theta  # 矩阵乘法

        # 计算损失（MSE）
        loss = np.mean((y_pred - y) ** 2)
        loss_history.append(loss)

        # 计算梯度
        gradient = (2/n) * X_b.T @ (y_pred - y)

        # 更新参数
        theta -= learning_rate * gradient

    return theta, loss_history

# 先归一化特征（梯度下降需要）
X_normalized = (X - X.mean(axis=0)) / X.std(axis=0)

# 训练
theta, losses = gradient_descent(X_normalized, y, learning_rate=0.01, epochs=500)

print(f"手动梯度下降结果: {theta}")
print(f"最终损失: {losses[-1]:.4f}")
```

---

## 今日完成检查

- [ ] sklearn代码跑通，理解了输入/输出
- [ ] 能解释R²的含义
- [ ] 梯度下降代码跑通了
- [ ] 能用自己的话解释"训练=最小化损失函数"

---

## 发给Claude的问题模板

```
今天学了线性回归，我有个问题：
[你的具体问题]

我的理解是：[你的理解]
请帮我确认是否正确，并补充我遗漏的部分。
```
