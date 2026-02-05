# 121. 买卖股票的最佳时机 - 渐进式学习指南

> 💡 **学习目标**：理解贪心思想，掌握股票问题的基本解法

---

## 🎯 问题理解

### 核心问题
给定每天的股票价格，选择一天买入、一天卖出，求最大利润。

### 示例分析
```
prices = [7, 1, 5, 3, 6, 4]

可能的交易：
第0天买入(7)，第2天卖出(5)：利润 = 5-7 = -2 ❌
第1天买入(1)，第2天卖出(5)：利润 = 5-1 = 4
第1天买入(1)，第4天卖出(6)：利润 = 6-1 = 5 ✅ 最大

答案：5
```

### 关键约束
1. ✅ 只能**先买后卖**（不能当天买卖）
2. ✅ 只能交易**一次**
3. ✅ 如果无法获利，返回0

---

## 💭 思考引导

在查看提示前，先思考以下问题：

### 问题1：暴力思路
最直接的方法是什么？

<details>
<summary>💡 提示</summary>

**枚举所有可能**：
- 枚举买入日：第i天
- 枚举卖出日：第j天（j > i）
- 计算利润：`prices[j] - prices[i]`

```cpp
for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
        int profit = prices[j] - prices[i];
        max_profit = max(max_profit, profit);
    }
}
```

**复杂度**：O(n²) → 会超时

</details>

### 问题2：如何优化？
有没有不需要双重循环的方法？

<details>
<summary>💡 提示</summary>

**关键观察**：
- 要使利润最大，应该在**最低点买入**，在**最高点卖出**
- 我们遍历时，只需要记住"到目前为止的最低价"

**优化思路**：
- 维护一个变量记录历史最低价
- 每天计算"如果今天卖出"的利润
- 更新最大利润

</details>

### 问题3：一个变量够吗？
需要记录哪些信息？

<details>
<summary>💡 提示</summary>

**需要两个变量**：
1. `min_price`：到目前为止遇到的最低价格
2. `max_profit`：到目前为止能获得的最大利润

**每天的操作**：
1. 更新 `min_price = min(min_price, today_price)`
2. 计算 `profit = today_price - min_price`
3. 更新 `max_profit = max(max_profit, profit)`

</details>

---

## 📚 渐进式提示

### Level 1：理解问题本质 ⭐

<details>
<summary>点击查看</summary>

#### 问题本质

**买卖股票 = 找最大差值**

给定数组 `[7, 1, 5, 3, 6, 4]`，找两个数 `prices[i]` 和 `prices[j]`，使得：
- `j > i`（后买后卖）
- `prices[j] - prices[i]` 最大

#### 直观理解

想象你是一个投资者：
```
第0天：7元  → "太贵了，观望"
第1天：1元  → "好便宜！买入" ✓
第2天：5元  → "涨到5元，卖吗？先等等"
第3天：3元  → "又跌了，继续等"
第4天：6元  → "涨到6元！卖出" ✓
第5天：4元  → "已经卖了"

利润 = 6 - 1 = 5
```

#### 图解

```
价格
 7 |  *
 6 |           *     ← 最高点（卖出）
 5 |     *
 4 |               *
 3 |        *
 2 |
 1 |  *              ← 最低点（买入）
   +------------------→ 时间
    0 1 2 3 4 5

最大利润 = 最高点 - 最低点 = 6 - 1 = 5
```

</details>

---

### Level 2：贪心思路 ⭐⭐

<details>
<summary>点击查看</summary>

#### 贪心策略

**核心思想**：
- 维护"历史最低价格"
- 每天假设"今天卖出"，计算利润
- 更新最大利润

#### 算法流程

```
初始化：
  min_price = +∞     (历史最低价)
  max_profit = 0     (最大利润)

遍历每一天的价格 price：
  1. 更新最低价：min_price = min(min_price, price)
  2. 计算利润：profit = price - min_price
  3. 更新最大利润：max_profit = max(max_profit, profit)

返回 max_profit
```

#### 手动模拟

```
prices = [7, 1, 5, 3, 6, 4]

第0天：price=7
  min_price = min(∞, 7) = 7
  profit = 7 - 7 = 0
  max_profit = max(0, 0) = 0

第1天：price=1
  min_price = min(7, 1) = 1  ← 更新最低价
  profit = 1 - 1 = 0
  max_profit = max(0, 0) = 0

第2天：price=5
  min_price = min(1, 5) = 1
  profit = 5 - 1 = 4         ← 如果今天卖出
  max_profit = max(0, 4) = 4

第3天：price=3
  min_price = min(1, 3) = 1
  profit = 3 - 1 = 2
  max_profit = max(4, 2) = 4

第4天：price=6
  min_price = min(1, 6) = 1
  profit = 6 - 1 = 5         ← 最大利润！
  max_profit = max(4, 5) = 5

第5天：price=4
  min_price = min(1, 4) = 1
  profit = 4 - 1 = 3
  max_profit = max(5, 3) = 5

最终答案：5
```

#### 为什么是贪心？

**贪心 = 每步都做局部最优决策**

- 每天都更新"到目前为止的最低价"
- 每天都计算"如果今天卖出的利润"
- 不需要回溯或重新计算
- **局部最优** → **全局最优**

</details>

---

### Level 3：代码实现 ⭐⭐⭐

<details>
<summary>点击查看</summary>

#### 完整代码

```cpp
int maxProfit(vector<int>& prices) {
    /*
    一次遍历 + 贪心算法
    */
    int min_price = INT_MAX;  // 历史最低价格
    int max_profit = 0;       // 最大利润
    
    for (int price : prices) {
        // 1. 更新最低价格
        min_price = min(min_price, price);
        
        // 2. 计算如果今天卖出的利润
        int profit = price - min_price;
        
        // 3. 更新最大利润
        max_profit = max(max_profit, profit);
    }
    
    return max_profit;
}
```

#### 代码详解

**关键点1**：初始化
```cpp
int min_price = INT_MAX;  // 用无穷大初始化
// 这样第一个价格一定会成为最低价
```

**关键点2**：更新顺序很重要
```cpp
// 先更新最低价
min_price = min(min_price, price);
// 再计算利润
int profit = price - min_price;
```

**为什么这个顺序？**
- 因为 `profit = price - min_price` 保证了 `price` 在 `min_price` 之后
- 满足"先买后卖"的约束

**关键点3**：简化写法
```cpp
int maxProfit(vector<int>& prices) {
    int min_price = INT_MAX;
    int max_profit = 0;
    
    for (int price : prices) {
        min_price = min(min_price, price);
        max_profit = max(max_profit, price - min_price);
    }
    
    return max_profit;
}
```

#### 执行轨迹

```text
prices = [7, 1, 5, 3, 6, 4]

price=7: min_price=7,  max_profit=0
price=1: min_price=1,  max_profit=0
price=5: min_price=1,  max_profit=4
price=3: min_price=1,  max_profit=4
price=6: min_price=1,  max_profit=5  ← 最终答案
price=4: min_price=1,  max_profit=5
```

</details>

---

### Level 4：扩展与优化 ⭐⭐⭐⭐

<details>
<summary>点击查看</summary>

#### 动态规划视角

这道题也可以用DP思想理解：

**状态定义**：
- `dp[i][0]`：第i天不持有股票的最大利润
- `dp[i][1]`：第i天持有股票的最大利润

**状态转移**：
```cpp
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i]);
         // 前一天就不持有  or  前一天持有今天卖出

dp[i][1] = max(dp[i-1][1], -prices[i]);
         // 前一天就持有  or  今天买入
```

**代码实现**：
```cpp
int maxProfit_dp(vector<int>& prices) {
    int n = prices.size();
    if (n == 0) return 0;
    
    int not_hold = 0;           // 不持有股票
    int hold = -prices[0];      // 持有股票
    
    for (int i = 1; i < n; ++i) {
        not_hold = max(not_hold, hold + prices[i]);
        hold = max(hold, -prices[i]);
    }
    
    return not_hold;
}
```

#### 找出买卖时机

如果需要返回具体哪天买卖：

```cpp
tuple<int, int, int> maxProfit_with_days(vector<int>& prices) {
    int min_price = INT_MAX;
    int max_profit = 0;
    int buy_day = -1, sell_day = -1;
    int min_day = -1;
    
    for (int i = 0; i < prices.size(); ++i) {
        if (prices[i] < min_price) {
            min_price = prices[i];
            min_day = i;
        }
        
        int profit = prices[i] - min_price;
        if (profit > max_profit) {
            max_profit = profit;
            buy_day = min_day;
            sell_day = i;
        }
    }
    
    return {max_profit, buy_day, sell_day};
}

// 测试
// auto [profit, buy, sell] = maxProfit_with_days({7,1,5,3,6,4});
// cout << "利润=" << profit << ", 第" << buy << "天买入, 第" << sell << "天卖出" << endl;
// 利润=5, 第1天买入, 第4天卖出
```

#### 多次交易（变式）

**问题**：如果允许多次买卖（但不能同时持有多笔），如何求最大利润？

```cpp
int maxProfit_multiple(vector<int>& prices) {
    /*
    允许多次交易：每次上涨都交易
    */
    int profit = 0;
    for (int i = 1; i < prices.size(); ++i) {
        if (prices[i] > prices[i-1]) {
            profit += prices[i] - prices[i-1];
        }
    }
    return profit;
}

// 测试
// cout << maxProfit_multiple({7,1,5,3,6,4}) << endl;  // 7
// 第1天买入(1)，第2天卖出(5)：利润+4
// 第3天买入(3)，第4天卖出(6)：利润+3
// 总利润 = 4 + 3 = 7
```

</details>

---

## 🔨 动手实践

### 任务1：手动模拟
在纸上模拟 `[2, 4, 1, 7, 5, 11]` 的求解过程，填写表格：

| 天数 | 价格 | min_price | profit | max_profit |
|------|------|-----------|--------|------------|
| 0    | 2    |           |        |            |
| 1    | 4    |           |        |            |
| 2    | 1    |           |        |            |
| ...  | ...  |           |        |            |

### 任务2：实现基础版本
```cpp
int maxProfit(vector<int>& prices) {
    // TODO: 实现贪心算法
    return 0;
}

// 测试
// assert(maxProfit({7,1,5,3,6,4}) == 5);
// assert(maxProfit({7,6,4,3,1}) == 0);
```

### 任务3：调试版本
```cpp
int maxProfit_debug(vector<int>& prices) {
    int min_price = INT_MAX;
    int max_profit = 0;
    
    cout << "天数 | 价格 | 最低价 | 利润 | 最大利润" << endl;
    cout << "---------------------------------------------" << endl;
    
    for (int i = 0; i < prices.size(); ++i) {
        // TODO: 添加详细输出
    }
    
    return max_profit;
}
```

### 任务4：找出买卖日期
```cpp
tuple<int, int, int> maxProfit_with_days(vector<int>& prices) {
    // TODO: 返回 {max_profit, buy_day, sell_day}
    return {0, -1, -1};
}
```

---

## ✅ 自我检查清单

完成以下检查后，再查看完整解答：

- [ ] 理解问题约束（先买后卖、只交易一次）
- [ ] 能画出价格曲线图
- [ ] 理解贪心策略（维护最低价）
- [ ] 能手动模拟算法过程
- [ ] 理解为什么是O(n)时间
- [ ] 能独立实现完整代码
- [ ] 理解两个关键变量的作用
- [ ] 知道如何扩展到多次交易

---

## 🎓 知识拓展

### 1. 股票问题系列

| 题目 | 约束 | 难度 | 解法 |
|------|------|------|------|
| 121 | 1次交易 | ⭐ | 贪心 |
| 122 | 无限次 | ⭐ | 贪心 |
| 123 | 2次 | ⭐⭐⭐ | DP |
| 188 | k次 | ⭐⭐⭐ | DP |
| 309 | 含冷冻期 | ⭐⭐ | DP |
| 714 | 含手续费 | ⭐⭐ | DP |

### 2. 贪心算法应用

- **区间问题**：活动选择
- **排序贪心**：分发饼干
- **双指针贪心**：盛最多水的容器

### 3. 实际应用

- **量化交易**：简单趋势策略
- **投资决策**：历史低点买入
- **风险控制**：止损止盈

---

## 🚀 进阶挑战

### 挑战1：手续费
每次交易需要支付固定手续费，如何求最大利润？

### 挑战2：冷冻期
卖出后需要冷冻1天才能再买入，如何求最大利润？

### 挑战3：最多k次交易
允许最多k次交易，如何求最大利润？

---

## 💡 常见错误

### 错误1：忘记先买后卖
```cpp
// ❌ 错误：可能出现"第5天卖，第1天买"
// max_profit = max(prices) - min(prices);

// ✅ 正确：维护历史最低价
min_price = min(min_price, price);
max_profit = max(max_profit, price - min_price);
```

### 错误2：更新顺序错误
```cpp
// ❌ 错误：先计算利润
int profit = price - min_price;
min_price = min(min_price, price);

// ✅ 正确：先更新最低价
min_price = min(min_price, price);
int profit = price - min_price;
```

### 错误3：初始值设置不当
```cpp
// ❌ 错误
int min_price = 0;  // 可能比所有价格都小

// ✅ 正确
int min_price = INT_MAX;  // 或 prices[0]
```

---

## 📖 学习建议

1. **画图理解**：在价格曲线上标出买卖点
2. **手动模拟**：完整走一遍算法流程
3. **理解贪心**：为什么维护最低价就够了
4. **对比DP**：理解不同思路
5. **练习变式**：理解股票问题系列

完成这些后，查看 `solution.md` 获取完整解答！🎉
