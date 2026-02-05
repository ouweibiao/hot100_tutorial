# 35. 搜索插入位置

## 题目描述

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
请必须使用时间复杂度为 `O(log n)` 的算法。

**示例 1:**
```
输入: nums = [1,3,5,6], target = 5
输出: 2
```

**示例 2:**
```
输入: nums = [1,3,5,6], target = 2
输出: 1
```

**示例 3:**
```
输入: nums = [1,3,5,6], target = 7
输出: 4
```

---

## 解法：二分查找

### 核心思路
这是一个标准的二分查找题目。我们要找的是**第一个大于等于 target 的位置**。
如果找到了 `target`，返回索引；如果没有找到，`left` 指针最终停留的位置就是插入位置。

### 代码实现

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        // 如果循环结束还没找到，left 就是插入位置
        return left;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(log n)。
- **空间复杂度**：O(1)。

### 易错点
- `mid` 的计算防止溢出：`left + (right - left) / 2`。
- 循环结束后的返回值：`left` 是第一个大于等于 target 的位置，也就是插入位置。

---

## 总结
- 二分查找最基础的变体。
- 关键在于理解 `left` 指针在循环结束时的含义。
