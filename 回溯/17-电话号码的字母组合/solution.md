# 17. 电话号码的字母组合 - C++ 题解

## 方法：回溯

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    vector<string> ans;
    string path;
    string phoneMap[10] = {
        "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"
    };

    vector<string> letterCombinations(string digits) {
        if (digits.empty()) return {};
        backtrack(digits, 0);
        return ans;
    }

    void backtrack(const string& digits, int index) {
        if (index == digits.size()) {
            ans.push_back(path);
            return;
        }

        int digit = digits[index] - '0';
        string letters = phoneMap[digit];

        for (char ch : letters) {
            path.push_back(ch);
            backtrack(digits, index + 1);
            path.pop_back(); // 回溯
        }
    }
};
```
