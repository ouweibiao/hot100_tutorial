# 33. 搜索旋转排序数组

## 题目描述

整数数组 `nums` 按升序排列，数组中的值 **互不相同** 。
在传递给函数之前，`nums` 在预先未知的某个下标 `k`（`0 <= k < nums.length`）上进行了 **旋转**，使数组变为 `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]`（下标 从 0 开始 计数）。
例如， `[0,1,2,4,5,6,7]` 在下标 `3` 处经旋转后可能变为 `[4,5,6,7,0,1,2]` 。

给你 **旋转后** 的数组 `nums` 和一个整数 `target` ，如果 `nums` 中存在这个目标值 `target` ，则返回它的下标，否则返回 `-1` 。
你必须设计一个时间复杂度为 `O(log n)` 的算法解决此问题。

**示例 1：**
```
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```

---

## 解法：二分查找（判断有序区间）

### 核心思路
虽然数组被旋转了，但它仍然是**局部有序**的。
当我们从中间切开时，**一定有一半是有序的**，另一半可能是有序也可能是部分有序。

算法步骤：
1. 计算 `mid`。
2. 判断哪一部分是有序的：
   - 如果 `nums[left] <= nums[mid]`，说明 **[left, mid]** 是有序的。
   - 否则，说明 **[mid, right]** 是有序的。
3. 判断 `target` 是否在有序的那一部分中：
   - 如果在有序部分，则调整 `right` 或 `left` 在该范围内查找。
   - 如果不在，则去另一半查找。

### 代码实现

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            
            if (nums[mid] == target) {
                return mid;
            }
            
            // 判断哪边是有序的
            if (nums[left] <= nums[mid]) {
                // 左半边 [left, mid] 有序
                // 检查 target 是否在左半边范围内
                if (nums[left] <= target && target < nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } else {
                // 右半边 [mid, right] 有序
                // 检查 target 是否在右半边范围内
                if (nums[mid] < target && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        
        return -1;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(log n)。
- **空间复杂度**：O(1)。

### 易错点
- 判断有序区间时用 `nums[left] <= nums[mid]`，这里的 `<=` 是为了处理只有两个元素的情况（如 `[3, 1]`）。
- 判断 target 范围时，注意边界条件（是否包含 `nums[left]` 或 `nums[right]`）。

---

## 总结
- 处理旋转数组的关键在于利用“局部有序性”。
- 每次二分都能排除掉一半不可能的区域。
