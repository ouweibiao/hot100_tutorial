# 240. 搜索二维矩阵 II

## 题目描述

编写一个高效的算法来搜索 `m x n` 矩阵 `matrix` 中的一个目标值 `target` 。该矩阵具有以下特性：
- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

**示例 1：**
```
输入：matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 5
输出：true
```

---

## 解法：从右上角（或左下角）开始搜索

### 核心思路
这道题利用了矩阵行列有序的特性。
如果我们从**右上角** `(0, n-1)` 开始看：
- 把它看作一棵**二叉搜索树**的根节点。
- 如果当前值 `> target`：
  - 因为列是递增的，所以当前值下方的所有值肯定也大于 `target`。
  - 因此，我们可以**排除当前列**，向左移动一列。
- 如果当前值 `< target`：
  - 因为行是递增的，所以当前值左方的所有值肯定也小于 `target`。
  - 因此，我们可以**排除当前行**，向下移动一行。
- 如果当前值 `== target`：找到目标，返回 `true`。

从左下角开始也是同理。但不能从左上角或右下角开始，因为那样会有两个方向都变大或都变小，无法剪枝。

### 代码实现

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.empty()) return false;
        
        int m = matrix.size();
        int n = matrix[0].size();
        
        // 从右上角开始
        int row = 0;
        int col = n - 1;
        
        while (row < m && col >= 0) {
            int curr = matrix[row][col];
            
            if (curr == target) {
                return true;
            } else if (curr > target) {
                // 当前值太大，排除这一列，向左
                col--;
            } else {
                // 当前值太小，排除这一行，向下
                row++;
            }
        }
        
        return false;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(M + N)。每次迭代要么行增加，要么列减少，最多走 M+N 步。
- **空间复杂度**：O(1)。

### 易错点
- 注意边界条件 `row < m && col >= 0`。
- 别选错起始点（左上/右下不行）。

---

## 总结
- 这种方法也被称为“抽象 BST”查找。
- 这是一个非常优雅的 O(M+N) 解法。
