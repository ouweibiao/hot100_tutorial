# 34. 在排序数组中查找元素的第一个和最后一个位置

## 题目描述

给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。
如果数组中不存在目标值 `target`，返回 `[-1, -1]`。
你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。

**示例 1：**
```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**示例 2：**
```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

---

## 解法：两次二分查找

### 核心思路
我们需要找到 target 的**左边界**和**右边界**。
由于直接在一个二分里同时找两个边界比较麻烦，我们可以写两个二分函数（或者复用一个逻辑）：
1. **寻找左边界**：找到第一个 `>= target` 的位置。
2. **寻找右边界**：找到第一个 `> target` 的位置，然后减 1；或者直接找最后一个 `<= target` 的位置。

这里我们采用寻找“第一个 >= target”的通用函数 `lower_bound`。
- 左边界 `start` = `lower_bound(nums, target)`
- 右边界 `end` = `lower_bound(nums, target + 1) - 1`

### 代码实现

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int start = lowerBound(nums, target);
        
        // 如果 start 越界或者 nums[start] != target，说明 target 不存在
        if (start == nums.size() || nums[start] != target) {
            return {-1, -1};
        }
        
        // 寻找 target + 1 的位置，减 1 就是 target 的结束位置
        int end = lowerBound(nums, target + 1) - 1;
        
        return {start, end};
    }
    
    // 寻找第一个 >= target 的位置
    int lowerBound(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return left;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(log n)，执行了两次二分查找。
- **空间复杂度**：O(1)。

### 易错点
- `lowerBound` 返回的是 `left`，循环结束时 `left` 指向第一个 `>= target` 的元素。
- 判断 target 是否存在：需要检查 `start` 是否越界，以及 `nums[start]` 是否等于 `target`。

---

## 总结
- `std::lower_bound` 和 `std::upper_bound` 的手动实现版本。
- 找范围问题通常可以转化为找两个边界的问题。
