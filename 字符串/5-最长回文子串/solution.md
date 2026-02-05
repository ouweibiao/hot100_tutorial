# 5. 最长回文子串 - C++ 题解

## 方法：中心扩展法

```cpp
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    string longestPalindrome(string s) {
        if (s.empty()) return "";
        
        int start = 0;
        int maxLen = 0;
        
        for (int i = 0; i < s.size(); ++i) {
            // 1. 以 s[i] 为中心的奇数回文串 (如 "aba")
            int len1 = expandAroundCenter(s, i, i);
            // 2. 以 s[i] 和 s[i+1] 为中心的偶数回文串 (如 "abba")
            int len2 = expandAroundCenter(s, i, i + 1);
            
            int len = max(len1, len2);
            
            // 更新最大长度和起始位置
            if (len > maxLen) {
                maxLen = len;
                // 计算起始位置：
                // 如果是奇数长度，i 是中心，start = i - len/2
                // 如果是偶数长度，i 是左中心，start = i - (len-1)/2
                // 统一公式：start = i - (len - 1) / 2
                start = i - (len - 1) / 2;
            }
        }
        
        return s.substr(start, maxLen);
    }
    
    // 辅助函数：从 left 和 right 向两边扩展，返回回文串长度
    int expandAroundCenter(const string& s, int left, int right) {
        while (left >= 0 && right < s.size() && s[left] == s[right]) {
            left--;
            right++;
        }
        // 跳出循环时，s[left] != s[right]，回文串是 s[left+1...right-1]
        // 长度 = (right - 1) - (left + 1) + 1 = right - left - 1
        return right - left - 1;
    }
};
```

### 易错点解析
1. **返回值**：`expandAroundCenter` 返回的是**长度**。
2. **起始位置计算**：
   - 当 `len` 为奇数（如 3），中心在 `i`，`start = i - 1`。代入公式 `i - (3-1)/2 = i - 1`，正确。
   - 当 `len` 为偶数（如 4），中心在 `i` 和 `i+1` 之间，`start = i - 1`。代入公式 `i - (4-1)/2 = i - 1`（整数除法），正确。
3. **边界条件**：`while` 循环中必须检查 `left >= 0` 和 `right < s.size()`。
