# 322. 零钱兑换 - 编程指导

## 🎯 学习目标
- 理解完全背包问题
- 掌握最优化DP的设计
- 学会处理无解情况

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：暴力搜索
<details>
<summary>点击查看提示1</summary>

**最简单的方法**：递归尝试所有组合

```cpp
int coinChange(vector<int>& coins, int amount) {
    if (amount == 0) return 0;
    if (amount < 0) return -1;
    
    int minCoins = INT_MAX;
    for (int coin : coins) {
        int res = coinChange(coins, amount - coin);
        if (res >= 0) {
            minCoins = min(minCoins, res + 1);
        }
    }
    
    return minCoins == INT_MAX ? -1 : minCoins;
}
```

**问题**：大量重复计算

**示例**：`coins=[1,2,5], amount=11`
```
coinChange(11)
  ├─ coinChange(10)
  │   ├─ coinChange(9)
  │   │   ├─ coinChange(8)  ← 重复
  │   └─ coinChange(8)      ← 重复
  └─ coinChange(6)
      └─ coinChange(5)
```

</details>

---

### 🌟 提示 Level 2：动态规划思路
<details>
<summary>点击查看提示2</summary>

**状态定义**：
```
dp[i] = 凑成金额 i 所需的最少硬币数
```

**状态转移**：
```
对于金额 i，可以使用任意硬币 coin：
  使用 coin: dp[i] = dp[i - coin] + 1

取所有可能中的最小值：
  dp[i] = min(dp[i], dp[i - coin] + 1)
```

**示例**：`coins=[1,2,5], amount=5`
```
dp[5] 可以从哪里转移？
  - 使用硬币1: dp[4] + 1
  - 使用硬币2: dp[3] + 1
  - 使用硬币5: dp[0] + 1 ← 最优
```

</details>

---

### 🌟 提示 Level 3：DP实现
<details>
<summary>点击查看提示3</summary>

```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount + 1, INT_MAX);
    dp[0] = 0;  // 金额0不需要硬币
    
    for (int i = 1; i <= amount; i++) {
        for (int coin : coins) {
            if (coin <= i && dp[i - coin] != INT_MAX) {
                dp[i] = min(dp[i], dp[i - coin] + 1);
            }
        }
    }
    
    return dp[amount] == INT_MAX ? -1 : dp[amount];
}
```

**关键点**：
1. 初始化为 `INT_MAX` 表示不可达
2. 只有 `dp[i-coin]` 可达才能转移
3. 最后判断 `dp[amount]` 是否可达

</details>

---

### 🌟 提示 Level 4：完全背包模板
<details>
<summary>点击查看提示4</summary>

**完全背包标准模板**：

```cpp
// 求最小值
vector<int> dp(amount + 1, INT_MAX);
dp[0] = 0;

for (int i = 1; i <= amount; i++) {      // 遍历容量
    for (int coin : coins) {             // 遍历物品
        if (coin <= i && dp[i-coin] != INT_MAX) {
            dp[i] = min(dp[i], dp[i-coin] + 1);
        }
    }
}

// 求最大值
vector<int> dp(amount + 1, INT_MIN);
dp[0] = 0;

for (int i = 1; i <= amount; i++) {
    for (int coin : coins) {
        if (coin <= i && dp[i-coin] != INT_MIN) {
            dp[i] = max(dp[i], dp[i-coin] + 1);
        }
    }
}
```

</details>

---

## 🔨 动手实践

### 任务1：手动模拟
对 `coins=[1,2,5], amount=11`，填写DP数组：

| i | dp[i] | 决策 |
|---|-------|------|
| 0 | 0     | -    |
| 1 | ?     | ?    |
| 2 | ?     | ?    |
| 5 | ?     | ?    |
| 11| ?     | ?    |

### 任务2：实现代码
```cpp
int coinChange(vector<int>& coins, int amount) {
    // TODO: 实现完全背包
}
```

### 任务3：测试用例
```cpp
coins = [1,2,5], amount = 11  // 预期: 3
coins = [2], amount = 3       // 预期: -1
coins = [1], amount = 0       // 预期: 0
coins = [1,2,5], amount = 100 // 预期: 20
```

---

## 🎯 完全背包 vs 0-1背包

| 特性 | 0-1背包 | 完全背包 |
|------|---------|----------|
| 物品使用 | 每种最多1次 | 无限次 |
| 遍历顺序 | 从大到小 | 从小到大 |
| 应用场景 | 选择物品 | 零钱兑换、切割 |

---

## ✅ 自检清单

- [ ] 我理解了完全背包的概念
- [ ] 我知道为什么初始化为INT_MAX
- [ ] 我能推导状态转移方程
- [ ] 我理解了如何处理无解情况
- [ ] 我能独立实现代码

---

**下一步**：独立实现并对照 `solution.md`！
