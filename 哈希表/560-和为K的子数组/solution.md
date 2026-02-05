# 560. 和为 K 的子数组

## 题目描述

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 *该数组中和为 `k` 的子数组的个数* 。

子数组是数组中元素的连续非空序列。

**示例 1：**
```
输入：nums = [1,1,1], k = 2
输出：2
```

**示例 2：**
```
输入：nums = [1,2,3], k = 3
输出：2
```

---

## 解法一：枚举（O(n²) - 可能超时）

### 核心思路
枚举所有子数组的起点和终点，计算和。

```cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int count = 0;
        for (int start = 0; start < nums.size(); ++start) {
            int sum = 0;
            for (int end = start; end < nums.size(); ++end) {
                sum += nums[end];
                if (sum == k) {
                    count++;
                }
            }
        }
        return count;
    }
};
```
虽然简单，但在大数据量下效率不够。

---

## 解法二：前缀和 + 哈希表（O(n) - 最优解）⭐

### 核心思路
利用公式：`子数组和[j..i] = preSum[i] - preSum[j-1]`。
如果 `preSum[i] - preSum[j-1] == k`，则说明找到了一个和为 k 的子数组。
变形为：`preSum[j-1] == preSum[i] - k`。

我们在遍历数组计算当前前缀和 `currentSum` 时，只需要查找哈希表中是否已经存在 `currentSum - k` 这个前缀和，以及它出现的次数。

### 代码实现
```cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        // key: 前缀和, value: 出现次数
        unordered_map<int, int> mp;
        
        // 初始化：前缀和为0出现1次（相当于什么都不选的情况）
        // 这对于处理从下标0开始的子数组非常重要！
        mp[0] = 1;
        
        int count = 0;
        int preSum = 0;
        
        for (int x : nums) {
            preSum += x;
            
            // 查找是否存在 preSum - k
            if (mp.count(preSum - k)) {
                count += mp[preSum - k];
            }
            
            // 更新当前前缀和的次数
            mp[preSum]++;
        }
        
        return count;
    }
};
```

### 为什么 `mp[0] = 1` 很重要？
举个例子：`nums = [3], k = 3`。
- `preSum` 变成 3。
- 我们想找 `preSum - k = 0`。
- 如果没有 `mp[0] = 1`，我们会错过 `[3]` 这个子数组。
- `mp[0] = 1` 实际上代表了下标 `-1` 处的前缀和为 0。

### 复杂度分析
- **时间复杂度**：O(N)，只遍历一次数组。
- **空间复杂度**：O(N)，哈希表存储前缀和。

---

## 总结

- 这是一个非常经典的 **前缀和 + 哈希表** 问题。
- 很多涉及"子数组和"的问题都可以用这个套路解决。
- **注意**：如果数组中只有正数，可以用滑动窗口；但本题 `nums` 可能包含负数，滑动窗口失效，必须用前缀和。

---

**标签**：`哈希表` `前缀和` `medium`
