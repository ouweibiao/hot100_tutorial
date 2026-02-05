# 207. 课程表

## 题目描述

你这个学期必须选修 `numCourses` 门课程，记为 `0` 到 `numCourses - 1` 。

在选修某些课程之前需要一些先修课程。 先修课程按数组 `prerequisites` 给出，其中 `prerequisites[i] = [ai, bi]` ，表示如果要学习课程 `ai` 则 **必须** 先学习课程  `bi` 。
- 例如，先修课程对 `[0, 1]` 表示：想要学习课程 `0` ，你需要先完成课程 `1` 。

请你判断是否可能完成所有课程的学习？如果可以，返回 `true` ；否则，返回 `false` 。

**示例 1：**
```
输入：numCourses = 2, prerequisites = [[1,0]]
输出：true
解释：总共有 2 门课程。学习课程 1 之前，你需要完成课程 0 。这是可能的。
```

**示例 2：**
```
输入：numCourses = 2, prerequisites = [[1,0],[0,1]]
输出：false
解释：总共有 2 门课程。学习课程 1 之前，你需要先完成​课程 0 ；并且学习课程 0 之前，你还应先完成课程 1 。这是不可能的。
```

---

## 解法：拓扑排序（BFS）

### 核心思路
这个问题本质上是判断一个**有向图**是否存在**环**。如果存在环，说明课程之间存在循环依赖，无法完成。
我们可以使用**拓扑排序**来解决。

**Kahn 算法（BFS）流程**：
1. **统计入度**：遍历 `prerequisites`，统计每门课的入度（有多少先修课），并构建邻接表（这门课是哪些课的先修课）。
2. **入队**：将所有入度为 0 的课程（没有先修课的课程）加入队列。
3. **BFS**：
   - 从队列中取出一门课 `u`，说明这门课可以修了。
   - 计数 `count++`。
   - 遍历 `u` 的后续课程 `v`：
     - 将 `v` 的入度减 1（相当于修完了 `u`，`v` 的一个先修条件满足了）。
     - 如果 `v` 的入度变为 0，说明 `v` 的所有先修课都修完了，可以将 `v` 加入队列。
4. **结果**：如果 `count == numCourses`，说明所有课程都能修完（没有环）；否则说明有环。

### 代码实现

```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        // 1. 构建邻接表和入度数组
        vector<vector<int>> adj(numCourses);
        vector<int> indegree(numCourses, 0);
        
        for (const auto& pre : prerequisites) {
            int course = pre[0];
            int dependency = pre[1];
            adj[dependency].push_back(course);
            indegree[course]++;
        }
        
        // 2. 将所有入度为 0 的课程加入队列
        queue<int> q;
        for (int i = 0; i < numCourses; ++i) {
            if (indegree[i] == 0) {
                q.push(i);
            }
        }
        
        // 3. BFS 拓扑排序
        int count = 0;
        while (!q.empty()) {
            int curr = q.front();
            q.pop();
            count++;
            
            for (int next : adj[curr]) {
                indegree[next]--;
                if (indegree[next] == 0) {
                    q.push(next);
                }
            }
        }
        
        // 4. 判断是否所有课程都完成了
        return count == numCourses;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(V + E)，其中 V 是课程数，E 是依赖关系数。我们需要遍历所有的点和边。
- **空间复杂度**：O(V + E)，邻接表和入度数组的空间。

### 易错点
- 邻接表的构建方向：`[ai, bi]` 表示 `bi -> ai`，即 `bi` 是 `ai` 的先修课。所以 `adj[bi].push_back(ai)`。
- 环的检测：如果最后 `count < numCourses`，说明有些课程入度永远不为 0（在环中），无法入队。

---

## 总结
- 拓扑排序的标准模板题。
- 除了 BFS（Kahn 算法），也可以用 DFS（三色标记法）来检测环。BFS 通常更容易理解和实现。
