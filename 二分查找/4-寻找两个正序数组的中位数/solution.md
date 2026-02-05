# 4. 寻找两个正序数组的中位数

## 题目描述

给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数** 。

算法的时间复杂度应该为 `O(log (m+n))` 。

**示例 1：**
```
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
```

**示例 2：**
```
输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
```

---

## 解法：二分查找划分数组

### 核心思路
这道题是二分查找的 Hard 级别经典题。
要在 `O(log(m+n))` 时间内解决，就不能合并数组（那是 `O(m+n)`）。我们需要直接找到中位数的位置。

**中位数的定义**：将数组划分为等长的两部分，左边部分的最大值 `<= `右边部分的最小值。

假设我们在 `nums1` 中切一刀，位置为 `i`；在 `nums2` 中切一刀，位置为 `j`。
- 左半部分包含：`nums1[0...i-1]` 和 `nums2[0...j-1]`
- 右半部分包含：`nums1[i...m-1]` 和 `nums2[j...n-1]`

我们要满足：
1. **长度平衡**：左半部分元素个数等于右半部分（或者多 1 个）。即 `i + j = (m + n + 1) / 2`。
2. **数值正确**：`nums1[i-1] <= nums2[j]` 且 `nums2[j-1] <= nums1[i]`。

既然 `i + j` 是定值，我们可以只对 `nums1` 的切割位置 `i` 进行**二分查找**，算出来对应的 `j`，然后检查是否满足数值正确条件。

### 代码实现

```cpp
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        // 保证 nums1 是较短的数组，这样可以减少二分查找的范围
        if (nums1.size() > nums2.size()) {
            return findMedianSortedArrays(nums2, nums1);
        }
        
        int m = nums1.size();
        int n = nums2.size();
        
        // 分割线左边的元素总个数（m + n + 1）/ 2
        // 这样如果总数是奇数，左边多一个，中位数就是左边的最大值
        int totalLeft = (m + n + 1) / 2;
        
        int left = 0, right = m;
        
        while (left <= right) {
            // i 是 nums1 的分割点
            int i = left + (right - left) / 2;
            // j 是 nums2 的分割点
            int j = totalLeft - i;
            
            // 边界处理：如果分割点在 0，说明左边没元素，设为最小值
            // 如果分割点在末尾，说明右边没元素，设为最大值
            int nums1LeftMax = (i == 0) ? INT_MIN : nums1[i - 1];
            int nums1RightMin = (i == m) ? INT_MAX : nums1[i];
            
            int nums2LeftMax = (j == 0) ? INT_MIN : nums2[j - 1];
            int nums2RightMin = (j == n) ? INT_MAX : nums2[j];
            
            // 交叉比较
            if (nums1LeftMax <= nums2RightMin && nums2LeftMax <= nums1RightMin) {
                // 找到了合适的分割点
                if ((m + n) % 2 == 1) {
                    // 奇数个，中位数是左边最大的那个
                    return max(nums1LeftMax, nums2LeftMax);
                } else {
                    // 偶数个，中位数是左边最大和右边最小的平均值
                    return (max(nums1LeftMax, nums2LeftMax) + min(nums1RightMin, nums2RightMin)) / 2.0;
                }
            } else if (nums1LeftMax > nums2RightMin) {
                // nums1 左边太大了，说明分割点 i 需要左移
                right = i - 1;
            } else {
                // nums1 右边太小了（或者 nums2 左边太大了），说明分割点 i 需要右移
                left = i + 1;
            }
        }
        
        return 0.0;
    }
};
```
*(注意：代码中 `INT_min` 应为 `INT_MIN`，代码块中已修正)*

### 复杂度分析
- **时间复杂度**：O(log(min(m, n)))，我们只在较短的数组上进行二分。
- **空间复杂度**：O(1)。

### 易错点
- **边界条件**：处理 `i=0`, `i=m`, `j=0`, `j=n` 的情况，需要用到 `INT_MIN` 和 `INT_MAX`。
- **循环终止条件**：`left <= right`。
- **奇偶判断**：总个数为奇数时，中位数在左半边；偶数时，是左半边最大值和右半边最小值的平均。

---

## 总结
- 极具技巧性的题目，考察了对中位数性质的理解和二分查找的灵活运用。
- 关键在于将问题转化为“在两个有序数组中寻找第 K 小的数”（或者直接寻找划分点）。
