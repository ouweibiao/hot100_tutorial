# 647. 回文子串 - C++ 题解

## 方法：中心扩展法

代码结构与第 5 题高度相似。

```cpp
#include <string>
#include <vector>

using namespace std;

class Solution {
public:
    int countSubstrings(string s) {
        int count = 0;
        for (int i = 0; i < s.size(); ++i) {
            // 1. 以 s[i] 为中心的奇数回文串
            count += expand(s, i, i);
            // 2. 以 s[i] 和 s[i+1] 为中心的偶数回文串
            count += expand(s, i, i + 1);
        }
        return count;
    }
    
    // 辅助函数：返回以 left, right 为中心的回文串个数
    int expand(const string& s, int left, int right) {
        int res = 0;
        while (left >= 0 && right < s.size() && s[left] == s[right]) {
            res++; // 发现一个回文串，计数+1
            left--;
            right++;
        }
        return res;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n²)。
- **空间复杂度**：O(1)。

---

## 补充：动态规划写法

虽然空间不如中心扩展法，但作为 DP 的练习也很有价值。

```cpp
#include <string>
#include <vector>

using namespace std;

class Solution {
public:
    int countSubstrings(string s) {
        int n = s.size();
        vector<vector<bool>> dp(n, vector<bool>(n, false));
        int count = 0;
        
        // 注意遍历顺序：i 从下往上，j 从左往右
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (s[i] == s[j]) {
                    // 两种情况：
                    // 1. j - i <= 1 (如 "a", "aa")
                    // 2. dp[i+1][j-1] 为真 (如 "aba")
                    if (j - i <= 1 || dp[i + 1][j - 1]) {
                        dp[i][j] = true;
                        count++;
                    }
                }
            }
        }
        
        return count;
    }
};
```
