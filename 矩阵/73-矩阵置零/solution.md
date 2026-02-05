# 73. 矩阵置零

## 题目描述

给定一个 `m x n` 的矩阵，如果一个元素为 `0` ，则将其所在行和列的所有元素都设为 `0` 。请使用 **原地** 算法。

**示例 1：**
```
输入：matrix = [[1,1,1],[1,0,1],[1,1,1]]
输出：[[1,0,1],[0,0,0],[1,0,1]]
```

---

## 解法：利用首行首列做标记

### 核心思路
为了实现 O(1) 空间复杂度，我们可以利用矩阵本身的**第一行**和**第一列**来作为标志位。

**算法步骤**：
1. **记录首行首列状态**：首先检查第一行和第一列是否本来就包含 0，用两个变量 `row0_has_zero` 和 `col0_has_zero` 记录。
2. **使用首行首列标记**：遍历矩阵其余部分（从 `(1,1)` 开始）。如果 `matrix[i][j] == 0`，则将 `matrix[i][0]` 和 `matrix[0][j]` 设为 0。
3. **根据标记置零**：再次遍历矩阵其余部分。如果 `matrix[i][0] == 0` 或 `matrix[0][j] == 0`，则将 `matrix[i][j]` 设为 0。
4. **处理首行首列**：
   - 如果 `row0_has_zero` 为真，将第一行全设为 0。
   - 如果 `col0_has_zero` 为真，将第一列全设为 0。

### 代码实现

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        
        bool row0_has_zero = false;
        bool col0_has_zero = false;
        
        // 1. 检查第一行是否有 0
        for (int j = 0; j < n; ++j) {
            if (matrix[0][j] == 0) {
                row0_has_zero = true;
                break;
            }
        }
        
        // 2. 检查第一列是否有 0
        for (int i = 0; i < m; ++i) {
            if (matrix[i][0] == 0) {
                col0_has_zero = true;
                break;
            }
        }
        
        // 3. 使用第一行和第一列作为标记
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }
        
        // 4. 根据标记置零（从 1,1 开始）
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }
        
        // 5. 处理第一行和第一列
        if (row0_has_zero) {
            for (int j = 0; j < n; ++j) matrix[0][j] = 0;
        }
        if (col0_has_zero) {
            for (int i = 0; i < m; ++i) matrix[i][0] = 0;
        }
    }
};
```

### 复杂度分析
- **时间复杂度**：O(MN)。
- **空间复杂度**：O(1)。我们只用了两个布尔变量，其余利用了原数组空间。

### 易错点
- 必须先单独记录第一行和第一列的状态，因为后续操作会修改它们。
- 处理顺序很重要：先标记内部，再根据标记修改内部，最后处理首行首列。

---

## 总结
- 这是一个空间优化的经典案例。
- 如果允许 O(M+N) 空间，可以用两个数组分别记录行和列的状态，逻辑会简单很多。
