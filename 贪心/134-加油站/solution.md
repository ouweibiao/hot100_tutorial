# 134. 加油站

## 题目描述

在一条环路上有 `n` 个加油站，其中第 `i` 个加油站有汽油 `gas[i]` 升。
你有一辆油箱容量无限的汽车，从第 `i` 个加油站开往第 `i+1` 个加油站需要消耗汽油 `cost[i]` 升。你从其中的一个加油站出发，开始时油箱为空。

给定两个整数数组 `gas` 和 `cost` ，如果你可以绕环路行驶一周，则返回出发时加油站的编号，否则返回 `-1` 。如果存在解，则 **保证** 它是 **唯一** 的。

**示例 1:**
```
输入: gas = [1,2,3,4,5], cost = [3,4,5,1,2]
输出: 3
```

---

## 解法：一次遍历（贪心）

### 核心思路
1. **全局可行性**：如果所有油站的油量总和 `sum(gas)` 小于消耗总和 `sum(cost)`，那一定跑不完一圈。
2. **局部可行性**：
   - 我们尝试从下标 `0` 开始出发，记录当前的油箱余额 `current_tank`。
   - 如果在到达某一点 `i` 时，`current_tank < 0`，说明从之前的起点无法到达 `i`。
   - 更重要的是，这说明**之前的起点到 `i` 之间的任何一点都无法作为起点**。
   - 因此，我们直接把起点设为 `i + 1`，并清空 `current_tank`，重新开始尝试。

### 代码实现

```cpp
#include <vector>
#include <numeric>
using namespace std;

class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int n = gas.size();
        int total_tank = 0;
        int current_tank = 0;
        int start_index = 0;
        
        for (int i = 0; i < n; ++i) {
            int diff = gas[i] - cost[i];
            total_tank += diff;
            current_tank += diff;
            
            // 如果当前累积油量小于0，说明从 start_index 到 i 都无法作为起点
            if (current_tank < 0) {
                // 尝试下一个点作为起点
                start_index = i + 1;
                // 重置当前油量
                current_tank = 0;
            }
        }
        
        // 如果总油量不够，一定无法跑完一圈
        if (total_tank < 0) {
            return -1;
        }
        
        // 题目保证如果有解则是唯一的，且如果 total_tank >= 0，一定有解
        return start_index;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，只需要遍历一次数组。
- **空间复杂度**：O(1)。

### 易错点
- 需要维护 `total_tank` 来判断是否存在解。
- `current_tank` 小于 0 时，起点是 `i + 1`，而不是 `start_index + 1`。

---

## 总结
- 经典的贪心题目。
- 关键在于理解：如果你从 A 无法走到 B，那么 A 和 B 之间的任何一点 C 也无法走到 B。
