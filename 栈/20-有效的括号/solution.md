# 20. 有效的括号

## 题目描述

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s`，判断字符串是否有效。

有效字符串需满足：
1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。

### 示例

**示例 1：**
```
输入：s = "()"
输出：true
```

**示例 2：**
```
输入：s = "()[]{}"
输出：true
```

**示例 3：**
```
输入：s = "(]"
输出：false
```

**示例 4：**
```
输入：s = "([)]"
输出：false
```

**示例 5：**
```
输入：s = "{[]}"
输出：true
```

**提示：**
- `1 <= s.length <= 10^4`
- `s` 仅由括号 `'()[]{}'` 组成

---

## 解法：栈

### 核心思路

括号匹配是**栈**的经典应用场景。

**关键观察**：
1. **左括号**：需要等待匹配 → **入栈**
2. **右括号**：应该匹配最近的左括号 → **出栈并检查**
3. **最后栈应该为空**（所有左括号都被匹配）

### 匹配规则

```
配对关系：
( 匹配 )
[ 匹配 ]
{ 匹配 }
```

### 图解过程

**示例1**：`s = "{[()]}"`

```
遍历过程：
字符  | 操作    | 栈状态
-----|---------|--------
{    | 入栈    | [{]
[    | 入栈    | [{, []
(    | 入栈    | [{, [, (]
)    | 匹配(   | [{, []
]    | 匹配[   | [{]
}    | 匹配{   | []

最终栈空 → 有效
```

**示例2**：`s = "([)]"`

```
遍历过程：
字符  | 操作    | 栈状态
-----|---------|--------
(    | 入栈    | [(]
[    | 入栈    | [(, []
)    | 匹配[?  | 失败！应该匹配(

不匹配 → 无效
```

### 代码实现

```cpp
#include <stack>
#include <unordered_map>
#include <string>
using namespace std;

class Solution {
public:
    bool isValid(string s) {
        // 括号映射表
        unordered_map<char, char> pairs = {
            {')', '('},
            {']', '['},
            {'}', '{'}
        };
        
        stack<char> st;
        
        for (char ch : s) {
            if (pairs.count(ch)) {  // 右括号
                // 检查栈顶是否匹配
                if (st.empty() || st.top() != pairs[ch]) {
                    return false;
                }
                st.pop();
            } else {  // 左括号
                st.push(ch);
            }
        }
        
        // 最后栈必须为空
        return st.empty();
    }
};

// 测试
// Solution sol;
// sol.isValid("()");        // true
// sol.isValid("()[]{}");    // true
// sol.isValid("(]");        // false
// sol.isValid("([)]");      // false
// sol.isValid("{[]}");      // true
```

### 复杂度分析
- **时间复杂度**：O(n)
  - 遍历字符串一次
  - 每个字符只入栈/出栈一次
- **空间复杂度**：O(n)
  - 最坏情况下全是左括号，栈大小为n

---

## 优化：更简洁的写法

### 方法1：用字典存储映射

```cpp
bool isValid(string s) {
    stack<char> st;
    unordered_map<char, char> mapping = {{')', '('}, {']', '['}, {'}', '{'}};
    
    for (char ch : s) {
        if (mapping.count(ch)) {
            char top_element = st.empty() ? '#' : st.top();
            if (top_element != mapping[ch]) {
                return false;
            }
            if (!st.empty()) st.pop();
        } else {
            st.push(ch);
        }
    }
    
    return st.empty();
}
```

### 方法2：用替换法（不推荐）

```cpp
bool isValid(string s) {
    /*
    不断删除成对的括号，看最后是否为空
    注意：这种方法效率较低，仅供理解
    */
    int prev_len = -1;
    while (s.length() != prev_len) {
        prev_len = s.length();
        size_t pos;
        while ((pos = s.find("()")) != string::npos) s.replace(pos, 2, "");
        while ((pos = s.find("[]")) != string::npos) s.replace(pos, 2, "");
        while ((pos = s.find("{}")) != string::npos) s.replace(pos, 2, "");
    }
    return s.empty();
}
```

**复杂度分析**：
- 时间复杂度：O(n^2)（每次替换需要O(n)）
- 不推荐用于面试

---

## 边界情况处理

### 情况1：空字符串
```cpp
isValid("");  // 应该返回 true（没有不匹配的括号）
```

### 情况2：奇数长度
```cpp
isValid("(");  // false（必然无法完全匹配）
```

**优化**：可以提前判断
```cpp
bool isValid(string s) {
    if (s.length() % 2 != 0) {
        return false;
    }
    // ... 其他逻辑
}
```

### 情况3：只有右括号
```cpp
isValid("))");  // false（栈空时遇到右括号）
```

### 情况4：只有左括号
```cpp
isValid("((");  // false（最后栈不为空）
```

---

## 变式问题

### 1. [921. 使括号有效的最少添加](https://leetcode.cn/problems/minimum-add-to-make-parentheses-valid/)

**问题**：需要添加多少个括号才能使字符串有效？

```cpp
int minAddToMakeValid(string s) {
    /*
    需要添加的括号数 = 未匹配的左括号 + 未匹配的右括号
    */
    int left_need = 0;   // 未匹配的右括号
    int right_need = 0;  // 未匹配的左括号
    
    for (char ch : s) {
        if (ch == '(') {
            right_need += 1;
        } else {
            if (right_need > 0) {
                right_need -= 1;
            } else {
                left_need += 1;
            }
        }
    }
    
    return left_need + right_need;
}
```

### 2. [1541. 平衡括号字符串的最少插入次数](https://leetcode.cn/problems/minimum-insertions-to-balance-a-parentheses-string/)

**规则变化**：每个 `(` 需要匹配两个 `)`

### 3. [678. 有效的括号字符串](https://leetcode.cn/problems/valid-parenthesis-string/)

**新增**：字符串中包含 `*`，可以当作 `(`、`)` 或空

---

## 实战应用

### 场景1：表达式验证
```cpp
bool validate_expression(string expr) {
    // 验证数学表达式的括号是否正确
    string brackets = "";
    string target = "([{)]}";
    for (char ch : expr) {
        if (target.find(ch) != string::npos) {
            brackets += ch;
        }
    }
    // 调用之前的 isValid 函数
    // return isValid(brackets);
    return false; // 占位
}

// 测试
// validate_expression("3 * (2 + [5 - 1])");  // true
// validate_expression("3 * (2 + [5 - 1)");   // false
```

### 场景2：HTML标签匹配
```cpp
bool validate_html(string html) {
    // 简化版HTML标签匹配
    stack<string> st;
    int i = 0;
    while (i < html.length()) {
        if (html[i] == '<') {
            size_t j = html.find('>', i);
            if (j == string::npos) break;
            
            string tag = html.substr(i + 1, j - i - 1);
            if (tag.length() > 0 && tag[0] == '/') {
                // 闭合标签
                string tag_name = tag.substr(1);
                if (st.empty() || st.top() != tag_name) {
                    return false;
                }
                st.pop();
            } else {
                // 开始标签
                st.push(tag);
            }
            i = j + 1;
        } else {
            i += 1;
        }
    }
    return st.empty();
}
```

### 场景3：代码编辑器的括号高亮
```cpp
int find_matching_bracket(string code, int pos) {
    // 找到位置pos处括号的匹配括号
    string brackets = "()[]{}";
    if (brackets.find(code[pos]) == string::npos) {
        return -1;
    }
    
    string open = "([{";
    string close = ")]}";
    unordered_map<char, char> pairs = {{')', '('}, {']', '['}, {'}', '{'}};
    
    // 如果是左括号，向右找
    if (open.find(code[pos]) != string::npos) {
        stack<char> st;
        st.push(code[pos]);
        for (int i = pos + 1; i < code.length(); i++) {
            if (open.find(code[i]) != string::npos) {
                st.push(code[i]);
            } else if (close.find(code[i]) != string::npos) {
                if (!st.empty() && st.top() == pairs[code[i]]) {
                    st.pop();
                    if (st.empty()) {
                        return i;
                    }
                }
            }
        }
    }
    // 如果是右括号，向左找（类似逻辑）
    return -1;
}
```

---

## 核心知识点

### 1. 栈的特性
- **LIFO**（后进先出）
- 完美契合括号匹配的"就近匹配"需求

### 2. 匹配模式
```
左括号 → 入栈（等待匹配）
右括号 → 出栈并检查（匹配最近的左括号）
```

### 3. 三种失败情况
1. ❌ 遇到右括号时栈为空
2. ❌ 右括号与栈顶不匹配
3. ❌ 遍历结束后栈不为空

---

## 调试技巧

### 可视化栈状态
```cpp
bool isValid_debug(string s) {
    stack<char> st;
    unordered_map<char, char> mapping = {{')', '('}, {']', '['}, {'}', '{'}};
    
    for (int i = 0; i < s.length(); i++) {
        char ch = s[i];
        cout << "Step " << i+1 << ": char='" << ch << "', stack size=" << st.size() << endl;
        
        if (mapping.count(ch)) {
            if (st.empty() || st.top() != mapping[ch]) {
                cout << "  ❌ 匹配失败！" << endl;
                return false;
            }
            st.pop();
            cout << "  ✓ 匹配成功，出栈" << endl;
        } else {
            st.push(ch);
            cout << "  入栈" << endl;
        }
    }
    
    bool result = st.empty();
    cout << "\n最终栈是否为空: " << (result ? "true" : "false") << ", 结果: " << result << endl;
    return result;
}

// 测试
// isValid_debug("([)]");
```

---

## 总结

### 核心要点
1. ✅ **栈是括号匹配的标准解法**
2. ✅ **左括号入栈，右括号匹配**
3. ✅ **最后栈必须为空**
4. ✅ **时间复杂度O(n)，空间复杂度O(n)**

### 算法模板
```cpp
bool isValid(string s) {
    stack<char> st;
    unordered_map<char, char> pairs = {{')', '('}, {']', '['}, {'}', '{'}};
    
    for (char ch : s) {
        if (pairs.count(ch)) {  // 右括号
            if (st.empty() || st.top() != pairs[ch]) {
                return false;
            }
            st.pop();
        } else {  // 左括号
            st.push(ch);
        }
    }
    
    return st.empty();
}
```

### 记忆技巧
**括号匹配 = 栈的入栈出栈**
- 看到左括号 → 放进栈
- 看到右括号 → 从栈里拿
- 最后栈空了 → 全部匹配

这道题是**栈**数据结构的最佳入门题，必须熟练掌握！
