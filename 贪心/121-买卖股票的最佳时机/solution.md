# 121. 买卖股票的最佳时机

## 题目描述

给定一个数组 `prices`，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。

你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0`。

**示例 1：**
```
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）买入，在第 5 天（股票价格 = 6）卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

**示例 2：**
```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下，没有交易完成，所以最大利润为 0。
```

---

## 解法：一次遍历（贪心）

### 核心思路
我们想要利润最大化，其实就是想在**最低点买入**，在**最高点卖出**。
由于我们必须先买后卖，所以当我们遍历到第 `i` 天时，如果我们知道**前 `i-1` 天的最低价格**，那么我们就能算出如果在第 `i` 天卖出能赚多少钱。

具体策略：
1. 维护一个变量 `minPrice`，记录历史最低价格，初始化为无穷大。
2. 维护一个变量 `maxProfit`，记录最大利润，初始化为 0。
3. 遍历价格数组：
   - 更新 `minPrice`：`minPrice = min(minPrice, currentPrice)`
   - 更新 `maxProfit`：`maxProfit = max(maxProfit, currentPrice - minPrice)`

### 代码实现

```cpp
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int minPrice = INT_MAX;
        int maxProfit = 0;
        
        for (int price : prices) {
            // 记录历史最低价格
            if (price < minPrice) {
                minPrice = price;
            } 
            // 计算当前价格卖出的利润，并更新最大利润
            else if (price - minPrice > maxProfit) {
                maxProfit = price - minPrice;
            }
        }
        
        return maxProfit;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，只需要遍历一次数组。
- **空间复杂度**：O(1)，只使用了常数个变量。

### 易错点
- 初始化 `minPrice` 时要设为一个较大的数（如 `INT_MAX` 或数组第一个元素）。
- 如果数组为空或只有一个元素，利润应为 0（本题题目保证 `1 <= prices.length`，所以不用特判空）。

---

## 总结
- 这是一道经典的**贪心**入门题，也可以看作是**动态规划**的简化版（状态只跟前一天的最小值有关）。
- 关键在于：**在遍历过程中动态维护最小值**。
