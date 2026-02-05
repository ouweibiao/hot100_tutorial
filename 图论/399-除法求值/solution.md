# 399. 除法求值

## 题目描述

给你一个变量对数组 `equations` 和一个实数值数组 `values` 作为已知条件，其中 `equations[i] = [Ai, Bi]` 和 `values[i]` 共同表示等式 `Ai / Bi = values[i]` 。每个 `Ai` 或 `Bi` 是一个表示单个变量的字符串。

另有一些以数组 `queries` 表示的问题，其中 `queries[j] = [Cj, Dj]` 表示第 `j` 个问题，请你根据已知条件找出 `Cj / Dj = ?` 的结果作为答案。

返回 **所有问题的答案** 。如果存在某个无法确定的答案，则用 `-1.0` 替代这个答案。

**示例 1：**
```
输入：equations = [["a","b"],["b","c"]], values = [2.0,3.0], queries = [["a","c"],["b","a"],["a","e"],["a","a"],["x","x"]]
输出：[6.00000,0.50000,-1.00000,1.00000,-1.00000]
解释：
给定：a / b = 2.0, b / c = 3.0
问题：a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ?
结果：
a / c = (a / b) * (b / c) = 2.0 * 3.0 = 6.0
b / a = 1 / (a / b) = 1 / 2.0 = 0.5
a / e = -1.0 (e 未定义)
a / a = 1.0
x / x = -1.0 (x 未定义)
```

---

## 解法：并查集（带权并查集）

### 核心思路
我们可以将变量看作图中的节点，除法关系看作带权重的边。
`a / b = 2.0` 可以看作 `a -> b` 的权重是 2.0，`b -> a` 的权重是 1/2.0。
求解 `a / c`，本质上是寻找从 `a` 到 `c` 的路径，并将路径上的权重相乘。

**带权并查集**是一个非常优雅的解法：
- 每个节点指向其父节点，边权表示 `node / parent` 的值。
- 通过路径压缩，我们可以让每个节点直接指向根节点，并维护 `node / root` 的值。
- 如果两个节点在同一个集合（有相同的根），那么 `a / c = (a / root) / (c / root)`。

### 代码实现

```cpp
#include <vector>
#include <string>
#include <unordered_map>
using namespace std;

class UnionFind {
private:
    unordered_map<string, string> parent;
    unordered_map<string, double> weight; // weight[x] = x / parent[x]

public:
    // 查找根节点，并进行路径压缩
    pair<string, double> find(string x) {
        if (parent.find(x) == parent.end()) {
            // 如果节点不存在，初始化指向自己，权重 1.0
            parent[x] = x;
            weight[x] = 1.0;
        }
        
        if (parent[x] != x) {
            auto [root, rootWeight] = find(parent[x]);
            weight[x] *= rootWeight; // 更新权重：x/newParent = (x/oldParent) * (oldParent/root)
            parent[x] = root; // 路径压缩
        }
        return {parent[x], weight[x]};
    }
    
    // 合并两个节点：a / b = val
    void unite(string a, string b, double val) {
        auto [rootA, weightA] = find(a);
        auto [rootB, weightB] = find(b);
        
        if (rootA != rootB) {
            // 将 rootA 挂到 rootB 下面
            // 我们需要求 weight[rootA] = rootA / rootB
            // 已知：a / b = val
            //       a / rootA = weightA
            //       b / rootB = weightB
            // 推导：rootA / rootB = (rootA / a) * (a / b) * (b / rootB)
            //                    = (1 / weightA) * val * weightB
            parent[rootA] = rootB;
            weight[rootA] = val * weightB / weightA;
        }
    }
    
    // 查询结果：a / b
    double query(string a, string b) {
        if (parent.find(a) == parent.end() || parent.find(b) == parent.end()) {
            return -1.0;
        }
        
        auto [rootA, weightA] = find(a);
        auto [rootB, weightB] = find(b);
        
        if (rootA != rootB) {
            return -1.0; // 不连通
        }
        
        // a / b = (a / root) / (b / root) = weightA / weightB
        return weightA / weightB;
    }
};

class Solution {
public:
    vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, vector<vector<string>>& queries) {
        UnionFind uf;
        
        // 1. 构建并查集
        for (int i = 0; i < equations.size(); ++i) {
            uf.unite(equations[i][0], equations[i][1], values[i]);
        }
        
        // 2. 处理查询
        vector<double> results;
        for (const auto& q : queries) {
            results.push_back(uf.query(q[0], q[1]));
        }
        
        return results;
    }
};
```

### 复杂度分析
- **时间复杂度**：O((N + Q) * α(N))，其中 N 是方程数，Q 是查询数，α 是阿克曼函数的反函数（近乎常数）。
- **空间复杂度**：O(N)，存储并查集。

### 易错点
- 路径压缩时权重的更新逻辑：`weight[x] *= rootWeight`。
- 合并时权重的计算逻辑：`weight[rootA] = val * weightB / weightA`。推导过程要仔细。
- 查询时要先判断变量是否存在。

---

## 总结
- 带权并查集的经典应用。
- 也可以使用 BFS/DFS 在图中搜索路径，但并查集对于多次查询更高效。
