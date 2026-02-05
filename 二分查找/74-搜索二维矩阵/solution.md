# 74. 搜索二维矩阵

## 题目描述

给你一个 `m x n` 的整数矩阵 `matrix` ，该矩阵具有如下特性：
- 每行中的整数从左到右按非严格递增顺序排列。
- 每行的第一个整数大于前一行的最后一个整数。

给你一个整数 `target` ，如果 `target` 在矩阵中，返回 `true` ；否则，返回 `false` 。

**示例 1：**
```
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
输出：true
```

---

## 解法：虚拟一维数组二分

### 核心思路
由于矩阵的每一行是递增的，且下一行开头大于上一行结尾，这说明**把整个矩阵拉直成一个一维数组，它是有序的**。

我们可以直接对这个虚拟的一维数组进行二分查找：
- 设矩阵有 `m` 行 `n` 列。
- 虚拟数组的长度为 `m * n`。
- 虚拟下标 `idx` 对应矩阵中的坐标为：
  - 行：`row = idx / n`
  - 列：`col = idx % n`

### 代码实现

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size();
        int n = matrix[0].size();
        
        int left = 0;
        int right = m * n - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            
            // 映射回二维坐标
            int row = mid / n;
            int col = mid % n;
            int val = matrix[row][col];
            
            if (val == target) {
                return true;
            } else if (val < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return false;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(log(mn))，相当于在长度为 mn 的数组中进行二分。
- **空间复杂度**：O(1)。

### 易错点
- 坐标转换公式：`mid / n` 得到行，`mid % n` 得到列。注意除数是列数 `n`，而不是行数 `m`。

---

## 总结
- 二维转一维的经典应用。
- 只要有序，就可以二分。
