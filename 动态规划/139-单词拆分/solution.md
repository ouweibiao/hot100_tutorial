# 139. 单词拆分 - C++ 题解

## 方法：动态规划

```cpp
#include <vector>
#include <string>
#include <unordered_set>

using namespace std;

class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> wordSet(wordDict.begin(), wordDict.end());
        int n = s.length();
        
        // dp[i] 表示 s 的前 i 个字符是否可以被拆分
        vector<bool> dp(n + 1, false);
        dp[0] = true; // 空串算作可以被拆分
        
        for (int i = 1; i <= n; ++i) {
            // 枚举分割点 j
            for (int j = 0; j < i; ++j) {
                // 如果前 j 个字符可以拆分，且 s[j...i-1] 在字典中
                if (dp[j] && wordSet.count(s.substr(j, i - j))) {
                    dp[i] = true;
                    break; // 只要找到一种拆分方式即可
                }
            }
        }
        
        return dp[n];
    }
};
```
