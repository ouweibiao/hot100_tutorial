# 169. 多数元素

## 题目描述

给定一个大小为 `n` 的数组 `nums` ，返回其中的多数元素。多数元素是指在数组中出现次数 **大于** `⌊ n/2 ⌋` 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**示例 1：**
```
输入：nums = [3,2,3]
输出：3
```

**示例 2：**
```
输入：nums = [2,2,1,1,1,2,2]
输出：2
```

**进阶：**尝试设计时间复杂度为 O(n)、空间复杂度为 O(1) 的算法解决此问题。

---

## 解法一：哈希表（通用解法）

### 核心思路
统计频率，找到超过 n/2 的数。

### 代码实现
```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        unordered_map<int, int> counts;
        int n = nums.size();
        
        for (int num : nums) {
            counts[num]++;
            if (counts[num] > n / 2) {
                return num;
            }
        }
        
        return -1; // 理论上不会执行到这里
    }
};
```
- **时间**：O(n)
- **空间**：O(n)

---

## 解法二：排序（取巧解法）

### 核心思路
排序后，中间位置的元素一定是多数元素。

### 代码实现
```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        return nums[nums.size() / 2];
    }
};
```
- **时间**：O(n log n)
- **空间**：O(1)（取决于排序算法实现）

---

## 解法三：摩尔投票法（最优解）⭐

### 核心思路
把众数记为 +1，其他数记为 -1。
因为众数数量 > n/2，所以所有数的和肯定 > 0。
我们可以利用这个特性，通过"抵消"的方式找到众数。

### 代码实现
```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int candidate = 0;
        int count = 0;
        
        for (int num : nums) {
            if (count == 0) {
                // 之前的都抵消完了，当前数字成为新的候选人
                candidate = num;
            }
            
            if (num == candidate) {
                count++;
            } else {
                count--;
            }
        }
        
        return candidate;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，只遍历一次。
- **空间复杂度**：O(1)，只用了两个变量。

---

## 总结

- 如果不限制空间，哈希表最直观。
- 如果不限制时间，排序最代码最少。
- **摩尔投票法**是本题的精髓，是解决此类"绝对众数"问题的标准算法。

---

**标签**：`哈希表` `数组` `计数` `easy`
