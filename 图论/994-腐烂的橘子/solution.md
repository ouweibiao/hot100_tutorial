# 994. 腐烂的橘子

## 题目描述

在给定的 `m x n` 网格 `grid` 中，每个单元格可以有以下三个值之一：
- 值 `0` 代表空单元格；
- 值 `1` 代表新鲜橘子；
- 值 `2` 代表腐烂的橘子。
每分钟，腐烂的橘子周围 4 个方向上相邻的新鲜橘子都会腐烂。
返回 **直到单元格中没有新鲜橘子为止所必须经过的最小分钟数**。如果不可能，返回 `-1`。

**示例 1：**
```
输入：grid = [[2,1,1],[1,1,0],[0,1,1]]
输出：4
```

---

## 解法：多源 BFS

### 核心思路
这道题求的是“最小分钟数”，其实就是求**最短路径**，也就是 BFS 的层数。
与普通 BFS 不同的是，这里可能有多个腐烂的橘子同时开始扩散，所以是**多源 BFS**。

**算法流程**：
1. **初始化**：
   - 遍历网格，将所有腐烂橘子（`val=2`）加入队列。
   - 统计新鲜橘子（`val=1`）的数量 `freshCount`。
2. **BFS**：
   - 当队列不为空且 `freshCount > 0` 时，进行循环。
   - 每一轮循环代表 1 分钟，处理当前队列中的所有橘子。
   - 对每个取出的橘子，向四周扩散。如果遇到新鲜橘子：
     - 将其腐烂（`val=2`）。
     - `freshCount--`。
     - 加入队列。
   - 每完成一轮（一层），`minutes++`。
3. **结果**：
   - 如果循环结束后 `freshCount == 0`，返回 `minutes`。
   - 否则，说明还有橘子无法被感染，返回 `-1`。

### 代码实现

```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        queue<pair<int, int>> q;
        int freshCount = 0;
        
        // 1. 初始化：将所有腐烂橘子入队，并统计新鲜橘子数量
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (grid[i][j] == 2) {
                    q.push({i, j});
                } else if (grid[i][j] == 1) {
                    freshCount++;
                }
            }
        }
        
        // 如果没有新鲜橘子，直接返回 0
        if (freshCount == 0) return 0;
        
        int minutes = 0;
        int dirs[4][2] = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        
        // 2. BFS
        while (!q.empty()) {
            int size = q.size();
            bool rotted = false; // 标记这一轮是否有橘子腐烂
            
            for (int i = 0; i < size; ++i) {
                auto [r, c] = q.front();
                q.pop();
                
                for (auto& dir : dirs) {
                    int nr = r + dir[0];
                    int nc = c + dir[1];
                    
                    if (nr >= 0 && nr < m && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                        grid[nr][nc] = 2; // 腐烂
                        q.push({nr, nc});
                        freshCount--;
                        rotted = true;
                    }
                }
            }
            
            if (rotted) minutes++;
        }
        
        return freshCount == 0 ? minutes : -1;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(MN)。每个节点最多进队一次。
- **空间复杂度**：O(MN)。队列的大小。

### 易错点
- 只有在这一轮**确实感染了新橘子**的情况下，时间才 +1。
- 或者可以在 while 循环里无脑 +1，最后返回 `minutes - 1`（前提是 `minutes > 0`）。上面的代码采用了 `rotted` 标记法，逻辑更清晰。

---

## 总结
- BFS 处理层级/最短路径问题的典范。
- 多源 BFS 只需要在一开始把所有源头都放进队列即可。
