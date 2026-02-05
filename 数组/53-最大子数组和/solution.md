# 53. 最大子数组和

## 📝 题目描述

给你一个整数数组 `nums`，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**子数组** 是数组中的一个连续部分。

**示例 1：**
```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6
```

**示例 2：**
```
输入：nums = [1]
输出：1
```

**示例 3：**
```
输入：nums = [5,4,-1,7,8]
输出：23
```

**约束条件：**
- `1 <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

---

## 💡 解法一：暴力枚举（会超时）

### 思路
枚举所有可能的子数组，计算它们的和，取最大值。

### 代码实现
```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        int maxSum = INT_MIN;
        
        for (int i = 0; i < n; i++) {
            int currentSum = 0;
            for (int j = i; j < n; j++) {
                currentSum += nums[j];
                maxSum = max(maxSum, currentSum);
            }
        }
        
        return maxSum;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n²)
- **空间复杂度**：O(1)

---

## ⭐ 解法二：动态规划（最优解）

### 思路

**核心思想**：对于位置 `i`，以 `nums[i]` 结尾的最大子数组和只有两种情况：
1. 接上前面的子数组：`dp[i-1] + nums[i]`
2. 重新开始：`nums[i]`

取两者中的较大值：
```
dp[i] = max(dp[i-1] + nums[i], nums[i])
```

**判断依据**：
- 如果 `dp[i-1] > 0`，说明前面的子数组有贡献，接上
- 如果 `dp[i-1] <= 0`，说明前面的子数组是负担，抛弃

### 代码实现（DP数组版）
```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n);
        
        dp[0] = nums[0];
        int maxSum = dp[0];
        
        for (int i = 1; i < n; i++) {
            dp[i] = max(dp[i-1] + nums[i], nums[i]);
            maxSum = max(maxSum, dp[i]);
        }
        
        return maxSum;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
- **空间复杂度**：O(n)

---

## 🚀 解法三：空间优化的动态规划

### 思路
观察状态转移方程，`dp[i]` 只依赖于 `dp[i-1]`，所以不需要整个 DP 数组，只需要一个变量记录前一个状态。

### 代码实现
```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int maxSum = nums[0];
        int currentSum = nums[0];
        
        for (int i = 1; i < nums.size(); i++) {
            // currentSum 表示以 nums[i] 结尾的最大子数组和
            currentSum = max(currentSum + nums[i], nums[i]);
            maxSum = max(maxSum, currentSum);
        }
        
        return maxSum;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
- **空间复杂度**：O(1)

---

## 🎯 核心知识点

### 1. 动态规划五步法

**① 确定状态**：
- `dp[i]`：以 `nums[i]` 结尾的最大子数组和

**② 状态转移方程**：
```
dp[i] = max(dp[i-1] + nums[i], nums[i])
```

**③ 初始化**：
```
dp[0] = nums[0]
```

**④ 遍历顺序**：
- 从左到右遍历

**⑤ 返回结果**：
```
max(dp[0], dp[1], ..., dp[n-1])
```

### 2. 贪心的角度理解

也可以用贪心思想理解：
- 维护当前子数组和 `currentSum`
- 如果 `currentSum < 0`，说明当前子数组是负担，直接抛弃，从下一个元素重新开始
- 否则继续累加

```cpp
int currentSum = 0;
int maxSum = INT_MIN;

for (int num : nums) {
    if (currentSum < 0) {
        currentSum = num;  // 重新开始
    } else {
        currentSum += num;  // 继续累加
    }
    maxSum = max(maxSum, currentSum);
}
```

### 3. Kadane's Algorithm（卡登算法）

解法三实际上是经典的 **Kadane's Algorithm**，专门用于解决最大子数组和问题。

---

## 🔍 图解过程

以 `nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]` 为例：

```
i    nums[i]   currentSum           maxSum
-    ------     ----------           ------
0      -2          -2                 -2
1       1           1    (重新开始)    1
2      -3          -2                  1
3       4           4    (重新开始)    4
4      -1           3                  4
5       2           5                  5
6       1           6                  6
7      -5           1                  6
8       4           5                  6

最终答案：6
```

**决策过程**：
- `i=1`：前面的 `-2` 是负数，不如重新开始
- `i=3`：前面的 `-2` 是负数，重新开始
- `i=4~6`：持续累加，达到最大值 6
- `i=7~8`：虽然和变小，但不影响已记录的最大值

---

## 🎓 常见变式

### 变式1：返回最大子数组本身
需要记录最大子数组的起始和结束位置：

```cpp
int start = 0, end = 0, tempStart = 0;
int maxSum = nums[0];
int currentSum = nums[0];

for (int i = 1; i < nums.size(); i++) {
    if (currentSum < 0) {
        currentSum = nums[i];
        tempStart = i;  // 重新开始的位置
    } else {
        currentSum += nums[i];
    }
    
    if (currentSum > maxSum) {
        maxSum = currentSum;
        start = tempStart;
        end = i;
    }
}
```

### 变式2：最大子数组乘积（LeetCode 152）
状态转移方程需要同时维护最大值和最小值（因为负数×负数可能变最大）。

### 变式3：环形数组最大子数组和（LeetCode 918）
分两种情况：
1. 最大子数组不跨越边界 → 正常 Kadane 算法
2. 最大子数组跨越边界 → `总和 - 最小子数组和`

---

## 📌 记忆要点

- **模式**：连续子数组问题 → 动态规划 / 贪心
- **状态定义**：以当前位置结尾的最优解
- **决策**：接上前面 or 重新开始
- **优化**：滚动变量代替 DP 数组
- **别名**：Kadane's Algorithm

---

**标签**：`数组` `动态规划` `贪心` `medium`
