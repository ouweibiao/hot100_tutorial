# 22. 括号生成

## 题目描述

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且**有效的**括号组合。

**示例 1：**
```
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2：**
```
输入：n = 1
输出：["()"]
```

**提示：**
- `1 <= n <= 8`

---

## C++基础知识

### 1. 字符串操作
```cpp
// 定义字符串
string s = "hello";

// 拼接字符
s += '(';        // s = "hello("
s += ')';        // s = "hello()"

// 删除最后一个字符（回溯用）
s.pop_back();    // s = "hello("

// 获取长度
int len = s.size();  // 或 s.length()

// 字符串拷贝
string copy = s;     // 深拷贝
```

### 2. vector的使用
```cpp
// 定义
vector<string> result;

// 添加元素
result.push_back("()");

// 访问
string first = result[0];

// 遍历
for (const auto& str : result) {
    cout << str << endl;
}

// 大小
int size = result.size();
```

### 3. 引用传递（提高效率）
```cpp
// 值传递（拷贝，慢）
void backtrack(string current, vector<string> result) { }

// 引用传递（不拷贝，快）
void backtrack(string& current, vector<string>& result) { }
```

### 4. const关键字
```cpp
// const引用（只读，不能修改）
void print(const string& s) {
    // s += '(';  // 错误！不能修改
    cout << s;   // 正确
}

// 普通引用（可以修改）
void modify(string& s) {
    s += '(';    // 正确
}
```

---

## 解法一：回溯（推荐）

### 思路
使用回溯算法，逐个添加 `'('` 和 `')'`，并确保在任何位置：
- 左括号数量 `<= n`
- 右括号数量 `<= 左括号数量`（保证有效性）

### 核心思想
- **决策树**：每个节点有两个选择：添加 `'('` 或 `')'`
- **剪枝条件**：
  1. 左括号数量超过 `n`：不继续
  2. 右括号数量超过左括号：不继续（会导致无效）

### 图解决策树（n=3）

```
                        ""
                /                \
            "("(left=1)           [剪枝：右括号>左括号]
           /          \
    "(("(l=2)       "()"(l=1,r=1)
    /      \         /        \
"((("     "(()     "()("     "())"
l=3       l=2,r=1   l=2,r=1   l=1,r=2
  |         |  \      |  \       [剪枝]
"((()   "(()" "(())  "()(" "()()
...      ...   ...    ...   ...

最终有效的：
"((()))", "(()())", "(())()", "()(())", "()()()"
```

### 代码实现

```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> result;
        string current = "";
        backtrack(result, current, 0, 0, n);
        return result;
    }
    
private:
    // left: 已使用的左括号数量
    // right: 已使用的右括号数量
    // n: 括号对数
    void backtrack(vector<string>& result, string& current, 
                   int left, int right, int n) {
        // 终止条件：生成了完整的括号组合
        if (current.size() == 2 * n) {
            result.push_back(current);
            return;
        }
        
        // 选择1：添加左括号
        if (left < n) {
            current += '(';
            backtrack(result, current, left + 1, right, n);
            current.pop_back();  // 回溯
        }
        
        // 选择2：添加右括号
        if (right < left) {
            current += ')';
            backtrack(result, current, left, right + 1, n);
            current.pop_back();  // 回溯
        }
    }
};
```

### 更简洁的版本（值传递）
```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> result;
        backtrack(result, "", 0, 0, n);
        return result;
    }
    
private:
    void backtrack(vector<string>& result, string current, 
                   int left, int right, int n) {
        if (current.size() == 2 * n) {
            result.push_back(current);
            return;
        }
        
        if (left < n) {
            backtrack(result, current + "(", left + 1, right, n);
        }
        
        if (right < left) {
            backtrack(result, current + ")", left, right + 1, n);
        }
    }
};
```

### 复杂度分析
- **时间复杂度**：O(4^n / √n)，即第n个卡塔兰数
  - 有效括号序列数量为卡塔兰数 C_n = (2n)! / ((n+1)! * n!)
  - 约等于 4^n / (n√n)
- **空间复杂度**：O(n)，递归栈深度最多为 2n

---

## 解法二：动态规划

### 思路
`dp[i]` 表示 `i` 对括号的所有有效组合。

递推公式：
```
dp[i] = "(" + dp[j] + ")" + dp[i-j-1]
```
其中 `j` 从 `0` 到 `i-1`。

### 图解（n=3）

```
dp[0] = [""]
dp[1] = ["()"]
dp[2] = ["(())", "()()"]
dp[3] = ?

构造dp[3]：
j=0: "(" + dp[0] + ")" + dp[2] = "()" + "(())" = "()(())"
     "(" + dp[0] + ")" + dp[2] = "()" + "()()" = "()()()"
j=1: "(" + dp[1] + ")" + dp[1] = "(())" + "()" = "(())()"
j=2: "(" + dp[2] + ")" + dp[0] = "((()))" 或 "(()())"

结果：["((()))", "(()())", "(())()", "()(())", "()()()"]
```

### 代码实现

```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<vector<string>> dp(n + 1);
        dp[0] = {""};  // 0对括号
        
        // 计算每个dp[i]
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                // dp[i] = "(" + dp[j] + ")" + dp[i-j-1]
                for (const string& left : dp[j]) {
                    for (const string& right : dp[i - j - 1]) {
                        dp[i].push_back("(" + left + ")" + right);
                    }
                }
            }
        }
        
        return dp[n];
    }
};
```

### 复杂度分析
- **时间复杂度**：O(4^n / √n)，与卡塔兰数相关
- **空间复杂度**：O(4^n / √n)，存储所有结果

---

## 解法三：BFS（广度优先搜索）

### 思路
用队列维护所有可能的中间状态，逐步扩展。

### 代码实现

```cpp
#include <queue>
using namespace std;

class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> result;
        
        // 队列存储：{当前字符串, 左括号数, 右括号数}
        struct State {
            string str;
            int left;
            int right;
        };
        
        queue<State> q;
        q.push({"", 0, 0});
        
        while (!q.empty()) {
            State current = q.front();
            q.pop();
            
            // 完成
            if (current.str.size() == 2 * n) {
                result.push_back(current.str);
                continue;
            }
            
            // 添加左括号
            if (current.left < n) {
                q.push({current.str + "(", current.left + 1, current.right});
            }
            
            // 添加右括号
            if (current.right < current.left) {
                q.push({current.str + ")", current.left, current.right + 1});
            }
        }
        
        return result;
    }
};
```

### C++知识点：结构体
```cpp
// 定义结构体
struct State {
    string str;
    int left;
    int right;
};

// 初始化
State s = {"()", 1, 1};

// 访问成员
s.str += '(';
s.left++;
```

### 复杂度分析
- **时间复杂度**：O(4^n / √n)
- **空间复杂度**：O(4^n / √n)，队列大小

---

## 方法对比

| 方法 | 时间复杂度 | 空间复杂度 | 代码难度 | 推荐度 |
|------|-----------|-----------|---------|--------|
| 回溯 | O(4^n/√n) | O(n) | 中等 | ⭐⭐⭐⭐⭐ |
| 动态规划 | O(4^n/√n) | O(4^n/√n) | 较难 | ⭐⭐⭐ |
| BFS | O(4^n/√n) | O(4^n/√n) | 中等 | ⭐⭐ |

**推荐**：**回溯法**，代码简洁，空间最优。

---

## 核心知识点

### 1. 回溯模板
```cpp
void backtrack(参数) {
    if (终止条件) {
        收集结果;
        return;
    }
    
    for (选择 in 选择列表) {
        做选择;
        backtrack(参数);
        撤销选择;  // 回溯
    }
}
```

### 2. 剪枝技巧
- **左括号剪枝**：`left < n`
- **右括号剪枝**：`right < left`（关键！保证有效性）

### 3. 有效括号的判断
在任意位置：
- 右括号数量 **不能超过** 左括号数量
- 最终左括号数量 = 右括号数量 = n

---

## 实战应用

1. **表达式生成**：生成合法的数学表达式
2. **编译器**：括号匹配检查
3. **代码格式化**：自动添加括号
4. **组合问题**：生成满足约束的组合

---

## 常见变式

1. **判断括号是否有效**（LeetCode 20）
   - 用栈检查

2. **最长有效括号**（LeetCode 32）
   - 动态规划或栈

3. **删除无效的括号**（LeetCode 301）
   - BFS + 剪枝

4. **括号的最大嵌套深度**（LeetCode 1614）
   - 遍历统计

---

## 常见错误

### 错误1：忘记回溯
```cpp
// ❌ 错误（没有撤销选择）
void backtrack(vector<string>& result, string& current, 
               int left, int right, int n) {
    if (left < n) {
        current += '(';
        backtrack(result, current, left + 1, right, n);
        // 忘记 current.pop_back();
    }
}

// ✅ 正确
void backtrack(vector<string>& result, string& current, 
               int left, int right, int n) {
    if (left < n) {
        current += '(';
        backtrack(result, current, left + 1, right, n);
        current.pop_back();  // 回溯
    }
}
```

### 错误2：右括号条件错误
```cpp
// ❌ 错误（会生成无效括号）
if (right < n) {
    // 应该是 right < left
}

// ✅ 正确
if (right < left) {
    current += ')';
}
```

### 错误3：终止条件错误
```cpp
// ❌ 错误
if (left == n && right == n) {
    // 可能提前终止
}

// ✅ 正确
if (current.size() == 2 * n) {
    result.push_back(current);
}
```

---

## 调试技巧

### 添加打印语句
```cpp
void backtrack(vector<string>& result, string& current, 
               int left, int right, int n) {
    // 打印当前状态
    cout << "current: " << current 
         << ", left: " << left 
         << ", right: " << right << endl;
    
    if (current.size() == 2 * n) {
        result.push_back(current);
        return;
    }
    
    if (left < n) {
        cout << "  -> add '('" << endl;
        current += '(';
        backtrack(result, current, left + 1, right, n);
        current.pop_back();
    }
    
    if (right < left) {
        cout << "  -> add ')'" << endl;
        current += ')';
        backtrack(result, current, left, right + 1, n);
        current.pop_back();
    }
}
```

---

## C++实用模板

### 回溯模板（引用传递）
```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> result;
        string current = "";
        backtrack(result, current, 0, 0, n);
        return result;
    }
    
private:
    void backtrack(vector<string>& result, string& current, 
                   int left, int right, int n) {
        if (current.size() == 2 * n) {
            result.push_back(current);
            return;
        }
        
        if (left < n) {
            current += '(';
            backtrack(result, current, left + 1, right, n);
            current.pop_back();
        }
        
        if (right < left) {
            current += ')';
            backtrack(result, current, left, right + 1, n);
            current.pop_back();
        }
    }
};
```

### 回溯模板（值传递）
```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> result;
        backtrack(result, "", 0, 0, n);
        return result;
    }
    
private:
    void backtrack(vector<string>& result, string current, 
                   int left, int right, int n) {
        if (current.size() == 2 * n) {
            result.push_back(current);
            return;
        }
        
        if (left < n) {
            backtrack(result, current + "(", left + 1, right, n);
        }
        
        if (right < left) {
            backtrack(result, current + ")", left, right + 1, n);
        }
    }
};
```

---

## 总结

**括号生成**是回溯算法的经典应用，重点掌握：

1. **回溯模板**：做选择 → 递归 → 撤销选择
2. **剪枝条件**：
   - 左括号：`left < n`
   - 右括号：`right < left`（核心！）
3. **终止条件**：`current.size() == 2 * n`
4. **字符串操作**：`+=` 添加，`pop_back()` 删除

**面试建议**：
- 优先使用回溯法
- 清晰地画出决策树
- 解释剪枝条件的原因
- 能够分析时间复杂度（卡塔兰数）

这道题是回溯的标准范例，务必完全掌握！🎯
