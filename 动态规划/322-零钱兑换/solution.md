# 322. 零钱兑换

## 📝 题目描述

给你一个整数数组 `coins`，表示不同面额的硬币；以及一个整数 `amount`，表示总金额。

计算并返回可以凑成总金额所需的**最少的硬币个数**。如果没有任何一种硬币组合能组成总金额，返回 `-1`。

你可以认为每种硬币的数量是无限的。

**示例 1：**
```
输入：coins = [1, 2, 5], amount = 11
输出：3 
解释：11 = 5 + 5 + 1
```

**示例 2：**
```
输入：coins = [2], amount = 3
输出：-1
```

**示例 3：**
```
输入：coins = [1], amount = 0
输出：0
```

**约束条件：**
- `1 <= coins.length <= 12`
- `1 <= coins[i] <= 2^31 - 1`
- `0 <= amount <= 10^4`

---

## ⭐ 解法：动态规划（完全背包）

### 思路

**状态定义**：
- `dp[i]`：凑成金额 i 所需的最少硬币数

**状态转移方程**：
对于金额 i，遍历所有硬币 coin：
- 如果 `coin <= i`，可以使用这枚硬币
- `dp[i] = min(dp[i], dp[i - coin] + 1)`

**初始化**：
```
dp[0] = 0  (金额为0，不需要硬币)
其他 = INF (表示暂时无法凑成)
```

### 代码实现
```cpp
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount + 1, INT_MAX);
        dp[0] = 0;
        
        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                if (coin <= i && dp[i - coin] != INT_MAX) {
                    dp[i] = min(dp[i], dp[i - coin] + 1);
                }
            }
        }
        
        return dp[amount] == INT_MAX ? -1 : dp[amount];
    }
};
```

### 复杂度分析
- **时间复杂度**：O(amount × n)，n 是硬币种类数
- **空间复杂度**：O(amount)

---

## 🎯 核心知识点

### 1. 完全背包问题

**特点**：
- 每种物品可以使用无限次
- 求最少/最多使用物品数

**与 0-1 背包的区别**：
```
0-1 背包：每种物品只能用一次
完全背包：每种物品可以用无限次
```

### 2. 状态转移方程推导

```
假设要凑成金额 i：
- 可以使用硬币 coin1, coin2, ..., coinn

对于每个硬币 coin：
  如果使用这枚硬币：
    dp[i] = dp[i - coin] + 1
    (凑成 i-coin 的最少硬币数 + 1枚当前硬币)

取所有可能中的最小值：
  dp[i] = min(dp[i], dp[i - coin] + 1)
```

### 3. 为什么用 INT_MAX？

```cpp
dp[i] = INT_MAX  // 表示金额 i 暂时无法凑成

if (dp[i - coin] != INT_MAX) {
    dp[i] = min(dp[i], dp[i - coin] + 1);
}
// 只有 dp[i - coin] 可达，才能转移
```

---

## 🔍 图解过程

以 `coins = [1, 2, 5], amount = 11` 为例：

```
dp[0] = 0

dp[1]:
  - 使用硬币1: dp[1-1] + 1 = 0 + 1 = 1
  dp[1] = 1

dp[2]:
  - 使用硬币1: dp[2-1] + 1 = 1 + 1 = 2
  - 使用硬币2: dp[2-2] + 1 = 0 + 1 = 1
  dp[2] = 1

dp[5]:
  - 使用硬币1: dp[5-1] + 1 = dp[4] + 1 = 3
  - 使用硬币2: dp[5-2] + 1 = dp[3] + 1 = 3
  - 使用硬币5: dp[5-5] + 1 = 0 + 1 = 1
  dp[5] = 1

...

dp[11]:
  - 使用硬币1: dp[10] + 1 = 2 + 1 = 3
  - 使用硬币2: dp[9] + 1 = 3 + 1 = 4
  - 使用硬币5: dp[6] + 1 = 2 + 1 = 3
  dp[11] = 3

结果：11 = 5 + 5 + 1 (3枚硬币)
```

---

## 🎓 变式题目

### 1. 零钱兑换 II（LeetCode 518）
求凑成金额的**组合数**。

**区别**：
- 本题求最少硬币数
- 变式求有多少种组合

### 2. 完全平方数（LeetCode 279）
给定整数 n，返回和为 n 的完全平方数的最少数量。

**类比**：硬币 → 完全平方数

### 3. 单词拆分（LeetCode 139）
判断字符串能否拆分成字典中的单词。

---

## 📌 记忆要点

- **模式**：完全背包（无限使用）
- **状态**：`dp[i]` = 凑成金额 i 的最少硬币数
- **转移**：`min(dp[i], dp[i-coin] + 1)`
- **初始化**：`dp[0] = 0`, 其他 = INF
- **返回**：`dp[amount] == INF ? -1 : dp[amount]`

---

## 🔨 常见错误

### 错误1：忘记判断可达性
```cpp
// ❌ 错误：dp[i-coin] 可能是 INT_MAX
dp[i] = min(dp[i], dp[i - coin] + 1);

// ✅ 正确
if (dp[i - coin] != INT_MAX) {
    dp[i] = min(dp[i], dp[i - coin] + 1);
}
```

### 错误2：初始化错误
```cpp
// ❌ 错误：dp[0] 应该是 0
vector<int> dp(amount + 1, INT_MAX);

// ✅ 正确
vector<int> dp(amount + 1, INT_MAX);
dp[0] = 0;
```

### 错误3：返回值处理
```cpp
// ❌ 错误：直接返回可能是 INT_MAX
return dp[amount];

// ✅ 正确
return dp[amount] == INT_MAX ? -1 : dp[amount];
```

---

**标签**：`动态规划` `完全背包` `medium`
