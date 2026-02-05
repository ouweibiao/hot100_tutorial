# 64. 最小路径和 - C++ 题解

## 方法：动态规划 (原地修改)

```cpp
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        
        // 遍历整个网格
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0 && j == 0) {
                    // 起点，不用处理
                    continue;
                } else if (i == 0) {
                    // 第一行，只能从左边过来
                    grid[i][j] += grid[i][j - 1];
                } else if (j == 0) {
                    // 第一列，只能从上边过来
                    grid[i][j] += grid[i - 1][j];
                } else {
                    // 其他位置，取左边和上边的较小值
                    grid[i][j] += min(grid[i - 1][j], grid[i][j - 1]);
                }
            }
        }
        
        return grid[m - 1][n - 1];
    }
};
```
