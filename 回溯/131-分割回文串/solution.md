# 131. 分割回文串 - C++ 题解

## 方法：回溯 + 记忆化/预处理

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    vector<vector<string>> ans;
    vector<string> path;
    vector<vector<bool>> dp;

    vector<vector<string>> partition(string s) {
        int n = s.size();
        // 预处理回文串信息
        dp.assign(n, vector<bool>(n, true));
        for (int i = n - 1; i >= 0; --i) {
            for (int j = i + 1; j < n; ++j) {
                dp[i][j] = (s[i] == s[j]) && dp[i + 1][j - 1];
            }
        }

        backtrack(s, 0);
        return ans;
    }

    void backtrack(const string& s, int index) {
        if (index == s.size()) {
            ans.push_back(path);
            return;
        }

        for (int i = index; i < s.size(); ++i) {
            // 如果 s[index...i] 是回文串
            if (dp[index][i]) {
                path.push_back(s.substr(index, i - index + 1));
                backtrack(s, i + 1);
                path.pop_back(); // 回溯
            }
        }
    }
};
```
