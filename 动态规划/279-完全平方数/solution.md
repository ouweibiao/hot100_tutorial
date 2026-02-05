# 279. 完全平方数 - C++ 题解

## 方法：动态规划

```cpp
#include <vector>
#include <algorithm>
#include <climits>

using namespace std;

class Solution {
public:
    int numSquares(int n) {
        vector<int> dp(n + 1, INT_MAX);
        dp[0] = 0;
        
        // 遍历每个数字 i，计算 dp[i]
        for (int i = 1; i <= n; ++i) {
            // 尝试减去每个小于等于 i 的完全平方数 j*j
            for (int j = 1; j * j <= i; ++j) {
                dp[i] = min(dp[i], dp[i - j * j] + 1);
            }
        }
        
        return dp[n];
    }
};
```

## 方法：数学 (四平方和定理 - 了解即可)

```cpp
#include <cmath>

class Solution {
public:
    int numSquares(int n) {
        // 判断是否是 4
        // 形式：n = 4^k * (8m + 7)
        int temp = n;
        while (temp % 4 == 0) temp /= 4;
        if (temp % 8 == 7) return 4;
        
        // 判断是否是 1
        int sqrtN = (int)sqrt(n);
        if (sqrtN * sqrtN == n) return 1;
        
        // 判断是否是 2
        for (int i = 1; i * i <= n; ++i) {
            int j = (int)sqrt(n - i * i);
            if (i * i + j * j == n) return 2;
        }
        
        // 否则就是 3
        return 3;
    }
};
```
