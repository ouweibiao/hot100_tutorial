# 62. 不同路径 - C++ 题解

## 方法：动态规划 (空间优化版)

```cpp
#include <vector>

using namespace std;

class Solution {
public:
    int uniquePaths(int m, int n) {
        // dp[j] 表示当前行第 j 列的路径数
        // 初始时，第一行的路径数全为 1
        vector<int> dp(n, 1);
        
        // 从第 1 行开始遍历（第 0 行已经初始化为 1）
        for (int i = 1; i < m; ++i) {
            // 第 0 列的路径数永远是 1，所以从第 1 列开始更新
            for (int j = 1; j < n; ++j) {
                // dp[j] (新) = dp[j] (旧，相当于上边) + dp[j-1] (新，相当于左边)
                dp[j] += dp[j - 1];
            }
        }
        
        return dp[n - 1];
    }
};
```
