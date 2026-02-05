# 32. 最长有效括号 - C++ 题解

## 方法：栈 (Stack)

```cpp
#include <string>
#include <stack>
#include <algorithm>

using namespace std;

class Solution {
public:
    int longestValidParentheses(string s) {
        stack<int> st;
        // 初始化放入 -1，作为"参照物"
        // 它的作用是：当第一个括号匹配成功时，长度 = i - (-1)
        st.push(-1);
        
        int maxLen = 0;
        
        for (int i = 0; i < s.length(); ++i) {
            if (s[i] == '(') {
                // 左括号：存下标
                st.push(i);
            } else {
                // 右括号：先弹出栈顶
                st.pop();
                
                if (st.empty()) {
                    // 栈空了，说明刚才弹出的就是栈底参照物
                    // 说明当前的 ')' 是多余的，无法匹配
                    // 将当前下标作为新的参照物入栈
                    st.push(i);
                } else {
                    // 栈不空，说明匹配成功
                    // 长度 = 当前下标 - 栈顶下标 (栈顶下标是上一个未匹配的位置)
                    maxLen = max(maxLen, i - st.top());
                }
            }
        }
        
        return maxLen;
    }
};
```

### 复杂度
- 时间复杂度：O(N)，遍历一次。
- 空间复杂度：O(N)，栈的大小。

## 方法：双向遍历 (O(1) 空间)

```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        int left = 0, right = 0, maxLen = 0;
        
        // 从左向右
        for (char c : s) {
            if (c == '(') left++;
            else right++;
            
            if (left == right) {
                maxLen = max(maxLen, 2 * right);
            } else if (right > left) {
                left = right = 0;
            }
        }
        
        left = right = 0;
        // 从右向左
        for (int i = s.length() - 1; i >= 0; i--) {
            if (s[i] == '(') left++;
            else right++;
            
            if (left == right) {
                maxLen = max(maxLen, 2 * left);
            } else if (left > right) {
                left = right = 0;
            }
        }
        
        return maxLen;
    }
};
```
