# 394. 字符串解码 - C++ 题解

## 方法：双栈法

```cpp
#include <string>
#include <stack>
#include <algorithm>

using namespace std;

class Solution {
public:
    string decodeString(string s) {
        stack<int> numStack;
        stack<string> strStack;
        string res = "";
        int multi = 0;
        
        for (char c : s) {
            if (isdigit(c)) {
                // 处理多位数字，例如 "100[a]"
                multi = multi * 10 + (c - '0');
            } else if (c == '[') {
                // 遇到 [，将当前的倍数和已有的字符串入栈
                numStack.push(multi);
                strStack.push(res);
                // 重置状态
                multi = 0;
                res = "";
            } else if (isalpha(c)) {
                // 遇到字母，直接追加到当前字符串
                res += c;
            } else if (c == ']') {
                // 遇到 ]，开始结算
                int cur_multi = numStack.top();
                numStack.pop();
                
                string last_res = strStack.top();
                strStack.pop();
                
                // 构造新的字符串：之前的字符串 + 当前括号内字符串 * 倍数
                for (int i = 0; i < cur_multi; ++i) {
                    last_res += res;
                }
                // 更新当前字符串
                res = last_res;
            }
        }
        
        return res;
    }
};
```

### 易错点
1. **多位数字**：`multi = multi * 10 + (c - '0')`，不能只读一位。
2. **栈的内容**：`strStack` 存的是**遇到 `[` 之前**已经解码好的字符串。
3. **出栈顺序**：遇到 `]` 时，先 pop 出倍数，再 pop 出之前的字符串。新的 `res` 是 `last_res + (res * multi)`。
