# 300. 最长递增子序列

## 题目描述

给你一个整数数组 `nums`，找到其中最长严格递增子序列的长度。

**子序列**是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列。

### 示例

**示例 1：**
```
输入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]，因此长度为 4。
```

**示例 2：**
```
输入：nums = [0,1,0,3,2,3]
输出：4
```

**示例 3：**
```
输入：nums = [7,7,7,7,7,7,7]
输出：1
```

**提示：**
- `1 <= nums.length <= 2500`
- `-10^4 <= nums[i] <= 10^4`

**进阶**：你能将算法的时间复杂度降低到 `O(n log n)` 吗？

---

## 解法一：动态规划 O(n²)

### 核心思路

**DP定义**：
- `dp[i]`：以 `nums[i]` 结尾的最长递增子序列长度

**状态转移**：
```
dp[i] = max(dp[j] + 1)  (0 <= j < i 且 nums[j] < nums[i])
```

**含义**：
- 遍历所有在i之前的元素j
- 如果 `nums[j] < nums[i]`，说明可以把nums[i]接到dp[j]后面
- 取所有可能的最大值

### 图解过程

```
nums = [10, 9, 2, 5, 3, 7, 101, 18]

初始：dp = [1, 1, 1, 1, 1, 1, 1, 1]
(每个元素自己构成长度为1的子序列)

i=0: dp[0]=1 (只有10)

i=1: nums[1]=9
  j=0: nums[0]=10 > 9，不更新
  dp[1]=1

i=2: nums[2]=2
  j=0: nums[0]=10 > 2，不更新
  j=1: nums[1]=9 > 2，不更新
  dp[2]=1

i=3: nums[3]=5
  j=0: nums[0]=10 > 5
  j=1: nums[1]=9 > 5
  j=2: nums[2]=2 < 5 ✓ dp[3] = dp[2]+1 = 2
  dp[3]=2  (子序列：2,5)

i=4: nums[4]=3
  j=2: nums[2]=2 < 3 ✓ dp[4] = dp[2]+1 = 2
  dp[4]=2  (子序列：2,3)

i=5: nums[5]=7
  j=2: nums[2]=2 < 7 ✓ dp[5] = dp[2]+1 = 2
  j=3: nums[3]=5 < 7 ✓ dp[5] = max(2, dp[3]+1) = 3
  j=4: nums[4]=3 < 7 ✓ dp[5] = max(3, dp[4]+1) = 3
  dp[5]=3  (子序列：2,5,7 或 2,3,7)

i=6: nums[6]=101
  可以接到所有元素后面
  dp[6]=4  (子序列：2,5,7,101 或 2,3,7,101)

i=7: nums[7]=18
  dp[7]=4  (子序列：2,5,7,18)

最终：max(dp) = 4
```

### 代码实现

```cpp
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        
        // dp[i]: 以nums[i]结尾的最长递增子序列长度
        vector<int> dp(n, 1);
        int maxLen = 1;
        
        for (int i = 1; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                if (nums[j] < nums[i]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
            maxLen = max(maxLen, dp[i]);
        }
        
        return maxLen;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n²)
  - 外层循环n次
  - 内层循环最多n次
- **空间复杂度**：O(n)
  - dp数组

---

## 解法二：贪心 + 二分查找 O(n log n) ⭐

### 核心思路

维护一个**递增序列** `tails`：
- `tails[i]`：长度为 `i+1` 的递增子序列的**最小末尾元素**

**关键观察**：
- 要使子序列尽可能长，末尾元素应该尽可能小
- 这样后面的元素更容易接上来

**算法流程**：
1. 遍历每个元素 `num`
2. 如果 `num > tails[-1]`：直接追加（长度+1）
3. 否则：二分查找，替换第一个 >= num 的元素

### 图解过程

```
nums = [10, 9, 2, 5, 3, 7, 101, 18]

初始：tails = []

num=10:
  tails为空，追加
  tails = [10]

num=9:
  9 < 10，替换10
  tails = [9]  (长度为1的子序列，9是更好的选择)

num=2:
  2 < 9，替换9
  tails = [2]

num=5:
  5 > 2，追加
  tails = [2, 5]  (长度为2的子序列：2,5)

num=3:
  3 在[2,5]之间，替换5
  tails = [2, 3]  (长度为2的子序列，3是更好的末尾)

num=7:
  7 > 3，追加
  tails = [2, 3, 7]  (长度为3的子序列：2,3,7)

num=101:
  101 > 7，追加
  tails = [2, 3, 7, 101]  (长度为4)

num=18:
  18 在[7,101]之间，替换101
  tails = [2, 3, 7, 18]

答案：len(tails) = 4
```

### 代码实现

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        if (nums.empty()) return 0;
        
        vector<int> tails;
        for (int num : nums) {
            // 如果 tails 为空或 num 大于 tails 的最后一个元素，直接追加
            if (tails.empty() || num > tails.back()) {
                tails.push_back(num);
            } else {
                // 否则，找到第一个大于等于 num 的元素并替换
                // lower_bound 返回第一个 >= num 的迭代器
                auto it = lower_bound(tails.begin(), tails.end(), num);
                *it = num;
            }
        }
        
        return tails.size();
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n log n)
  - 遍历n个元素
  - 每次二分查找O(log n)
- **空间复杂度**：O(n)
  - tails数组

---

## 方法对比

| 方法 | 时间复杂度 | 空间复杂度 | 优点 | 缺点 |
|------|-----------|-----------|------|------|
| DP | O(n²) | O(n) | 易理解，可打印路径 | 较慢 |
| 贪心+二分 | O(n log n) | O(n) | 高效 | 难理解 |

---

## 打印完整子序列

### 方法1：回溯（基于DP）

```cpp
vector<int> findLIS(vector<int>& nums) {
    int n = nums.size();
    if (n == 0) return {};
    
    // 1. 构建DP数组
    vector<int> dp(n, 1);
    int maxLen = 1;
    
    for (int i = 1; i < n; ++i) {
        for (int j = 0; j < i; ++j) {
            if (nums[j] < nums[i]) {
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
        maxLen = max(maxLen, dp[i]);
    }
    
    // 2. 回溯构建序列
    vector<int> result;
    int currentLen = maxLen;
    // 从后往前遍历
    for (int i = n - 1; i >= 0; --i) {
        // 如果当前位置的dp值等于需要的长度
        // 且 (是最后一个元素 或者 小于结果集中刚加入的元素)
        if (dp[i] == currentLen) {
             if (result.empty() || nums[i] < result.back()) {
                result.push_back(nums[i]);
                currentLen--;
             }
        }
    }
    
    // 因为是从后往前找，所以需要反转
    reverse(result.begin(), result.end());
    return result;
}
```

---

## 核心知识点

### 1. DP思路
- **状态定义**：以第i个元素结尾的最长长度
- **转移方程**：从前面所有更小的元素转移
- **初始状态**：每个元素自己构成长度1

### 2. 贪心思路
- **关键**：维护"长度为i的子序列的最小末尾"
- **操作**：二分查找 + 替换/追加
- **正确性**：替换不影响答案，但让后续更容易接上

### 3. 子序列 vs 子数组
- **子序列**：可以不连续（本题）
- **子数组**：必须连续

---

## 相关问题

### 1. [674. 最长连续递增序列](https://leetcode.cn/problems/longest-continuous-increasing-subsequence/)
- **区别**：必须连续
- **解法**：一次遍历O(n)

```cpp
class Solution {
public:
    int findLengthOfLCIS(vector<int>& nums) {
        if (nums.empty()) return 0;
        
        int maxLen = 1;
        int currentLen = 1;
        
        for (int i = 1; i < nums.size(); ++i) {
            if (nums[i] > nums[i-1]) {
                currentLen++;
                maxLen = max(maxLen, currentLen);
            } else {
                currentLen = 1;
            }
        }
        return maxLen;
    }
};
```

### 2. [354. 俄罗斯套娃信封问题](https://leetcode.cn/problems/russian-doll-envelopes/)
- **2D版本的LIS**
- 先排序，再求LIS

### 3. [646. 最长数对链](https://leetcode.cn/problems/maximum-length-of-pair-chain/)
- 类似LIS，但有额外约束

---

## 实战应用

### 场景1：版本号排序
```cpp
// 找最长的版本升级路径
// 将版本号转为可比较的格式
// 然后应用LIS算法
```

### 场景2：活动安排
```cpp
// 选择最多不重叠的活动
// 按结束时间排序
// 应用LIS思想
```

---

## 总结

### 核心要点
1. ✅ **DP定义**：以第i个元素结尾的最长长度
2. ✅ **转移方程**：从所有更小的元素转移
3. ✅ **优化**：贪心+二分，降至O(n log n)
4. ✅ **理解tails数组**：长度为i的子序列的最小末尾

### 算法模板

**DP版本**：
```cpp
vector<int> dp(n, 1);
for (int i = 1; i < n; ++i) {
    for (int j = 0; j < i; ++j) {
        if (nums[j] < nums[i]) {
            dp[i] = max(dp[i], dp[j] + 1);
        }
    }
}
return *max_element(dp.begin(), dp.end());
```

**贪心+二分**：
```cpp
vector<int> tails;
for (int num : nums) {
    auto it = lower_bound(tails.begin(), tails.end(), num);
    if (it == tails.end()) {
        tails.push_back(num);
    } else {
        *it = num;
    }
}
return tails.size();
```

这道题是**DP经典题**和**二分优化**的代表，必须熟练掌握！
