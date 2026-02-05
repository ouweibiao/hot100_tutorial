# 54. 螺旋矩阵

## 题目描述

给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。

**示例 1：**
```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

---

## 解法：模拟（设定边界）

### 核心思路
我们可以模拟螺旋的过程。
定义四个边界：
- `top`：上边界，初始为 0。
- `bottom`：下边界，初始为 m-1。
- `left`：左边界，初始为 0。
- `right`：右边界，初始为 n-1。

**循环过程**：
1. **向右**：遍历 `top` 行，从 `left` 到 `right`。完成后 `top++`。
2. **向下**：遍历 `right` 列，从 `top` 到 `bottom`。完成后 `right--`。
3. **向左**：遍历 `bottom` 行，从 `right` 到 `left`。完成后 `bottom--`。
4. **向上**：遍历 `left` 列，从 `bottom` 到 `top`。完成后 `left++`。

**终止条件**：
- 在每次遍历后，都要检查是否越界（例如 `top > bottom` 或 `left > right`），如果越界则立即结束。
- 或者统计元素总数，当收集了 `m * n` 个元素时结束。

### 代码实现

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> res;
        if (matrix.empty()) return res;
        
        int top = 0;
        int bottom = matrix.size() - 1;
        int left = 0;
        int right = matrix[0].size() - 1;
        
        while (true) {
            // 1. 向右
            for (int i = left; i <= right; ++i) res.push_back(matrix[top][i]);
            if (++top > bottom) break;
            
            // 2. 向下
            for (int i = top; i <= bottom; ++i) res.push_back(matrix[i][right]);
            if (--right < left) break;
            
            // 3. 向左
            for (int i = right; i >= left; --i) res.push_back(matrix[bottom][i]);
            if (--bottom < top) break;
            
            // 4. 向上
            for (int i = bottom; i >= top; --i) res.push_back(matrix[i][left]);
            if (++left > right) break;
        }
        
        return res;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(MN)。每个元素被访问一次。
- **空间复杂度**：O(1)。除了返回结果数组外，只用了常数空间。

### 易错点
- 每次遍历完一行/一列后，必须立即更新边界，并**检查是否越界**。
- 比如只有一行时，执行完“向右”后 `top` 增加，如果此时不检查，可能会继续执行“向左”，导致重复访问。

---

## 总结
- 纯模拟题，关键是边界控制。
- `break` 的位置很关键。
