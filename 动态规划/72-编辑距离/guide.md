# 72. 编辑距离 - 学习指南

> 🎯 **学习目标**：掌握二维动态规划，理解编辑距离的状态转移

---

## 📚 前置知识

在开始之前，确保你了解：
- [x] 动态规划的基本概念
- [x] 二维数组的使用
- [x] 递归与备忘录
- [x] 字符串操作

---

## 🎓 渐进式提示系统

### 💡 Level 1：问题理解

<details>
<summary>点击查看第一层提示</summary>

#### 核心问题
什么是编辑距离？

**编辑距离（Levenshtein距离）**：
- 将一个字符串转换成另一个字符串所需的**最少**单字符编辑操作数
- 允许的操作：插入、删除、替换

#### 简化问题

**示例1**：
```
word1 = "cat"
word2 = "cut"

操作：替换 'a' → 'u'
编辑距离 = 1
```

**示例2**：
```
word1 = "horse"
word2 = "ros"

操作序列：
1. horse → rorse (替换 h → r)
2. rorse → rose  (删除 r)
3. rose → ros    (删除 e)

编辑距离 = 3
```

#### 关键观察

1. **最优子结构**：
   - 如果知道 `word1[0..i-1]` 转换为 `word2[0..j-1]` 的最少操作数
   - 就可以计算 `word1[0..i]` 转换为 `word2[0..j]` 的最少操作数

2. **重叠子问题**：
   - 相同的子问题会被多次计算
   - 适合用DP

</details>

---

### 💡 Level 2：思路启发

<details>
<summary>点击查看第二层提示</summary>

#### 动态规划思路

**状态定义**：
```
dp[i][j] = word1的前i个字符转换为word2的前j个字符的最少操作数
```

**状态转移**：

考虑 `word1[i-1]` 和 `word2[j-1]`：

**情况1：字符相同**
```
word1: a b c [d]
word2: a b c [d]
       ↑     ↑
    已匹配  相同！

dp[i][j] = dp[i-1][j-1]  // 不需要操作
```

**情况2：字符不同**，有三种选择：

**选择1：替换**
```
word1: a b c [d]  →  a b c [e]
word2: a b c [e]      a b c [e]

替换 d → e，然后匹配前面的
dp[i][j] = dp[i-1][j-1] + 1
```

**选择2：删除**
```
word1: a b c [d]  →  a b c
word2: a b c          a b c

删除 d，然后匹配剩余部分
dp[i][j] = dp[i-1][j] + 1
```

**选择3：插入**
```
word1: a b c       →  a b c [e]
word2: a b c [e]      a b c [e]

插入 e，然后匹配剩余部分
dp[i][j] = dp[i][j-1] + 1
```

**最终**：
```cpp
if (word1[i-1] == word2[j-1]) {
    dp[i][j] = dp[i-1][j-1];
} else {
    dp[i][j] = min(替换, 删除, 插入);
}
```

#### 初始化

```
dp[0][j] = j  // word1为空，需要插入j个字符
dp[i][0] = i  // word2为空，需要删除i个字符
```

#### 手动模拟

**示例**：`word1 = "abc"`, `word2 = "adc"`

```
步骤1：初始化
        ""  a   d   c
    ""  0   1   2   3
    a   1   
    b   2   
    c   3   

步骤2：填表
dp[1][1]: 'a' vs 'a'
  相同！dp[1][1] = dp[0][0] = 0

dp[1][2]: 'a' vs 'd'
  不同，选择：
  替换: dp[0][1] + 1 = 2
  删除: dp[0][2] + 1 = 3
  插入: dp[1][1] + 1 = 1
  dp[1][2] = 1

dp[2][2]: 'b' vs 'd'
  不同，选择：
  替换: dp[1][1] + 1 = 1
  删除: dp[1][2] + 1 = 2
  插入: dp[2][1] + 1 = 2
  dp[2][2] = 1

... 继续填表

最终DP表：
        ""  a   d   c
    ""  0   1   2   3
    a   1   0   1   2
    b   2   1   1   2
    c   3   2   2   1

答案：dp[3][3] = 1（将b替换为d）
```

</details>

---

### 💡 Level 3：代码框架

<details>
<summary>点击查看第三层提示</summary>

#### DP框架

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size();
        int n = word2.size();
        
        // 特殊情况
        if (m == 0) return n;
        if (n == 0) return m;
        
        // TODO: 创建DP表
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
        
        // TODO: 初始化第一行和第一列
        for (int i = 0; i <= m; i++) {
            dp[i][0] = i;
        }
        for (int j = 0; j <= n; j++) {
            dp[0][j] = j;
        }
        
        // TODO: 填表
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1[i-1] == word2[j-1]) {
                    // 字符相同
                    dp[i][j] = dp[i-1][j-1];
                } else {
                    // 字符不同，三种选择
                    dp[i][j] = min({
                        dp[i-1][j-1] + 1,  // 替换
                        dp[i-1][j] + 1,    // 删除
                        dp[i][j-1] + 1     // 插入
                    });
                }
            }
        }
        
        return dp[m][n];
    }
};
```

#### 关键点

1. **DP表大小**：`(m+1) × (n+1)`
   - 多一行一列用于空字符串

2. **索引映射**：
   - `dp[i][j]` 对应 `word1[i-1]` 和 `word2[j-1]`

3. **状态转移**：
   - 相同：不操作
   - 不同：三种操作取最小

</details>

---

### 💡 Level 4：完整实现

<details>
<summary>点击查看完整代码</summary>

#### 标准DP实现

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size();
        int n = word2.size();
        
        if (m == 0) return n;
        if (n == 0) return m;
        
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
        
        // 初始化
        for (int i = 0; i <= m; i++) {
            dp[i][0] = i;
        }
        for (int j = 0; j <= n; j++) {
            dp[0][j] = j;
        }
        
        // 填表
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1[i-1] == word2[j-1]) {
                    dp[i][j] = dp[i-1][j-1];
                } else {
                    dp[i][j] = min({
                        dp[i-1][j-1] + 1,  // 替换
                        dp[i-1][j] + 1,    // 删除
                        dp[i][j-1] + 1     // 插入
                    });
                }
            }
        }
        
        return dp[m][n];
    }
};
```

#### 空间优化（滚动数组）

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size();
        int n = word2.size();
        
        if (m == 0) return n;
        if (n == 0) return m;
        
        vector<int> dp(n + 1);
        
        // 初始化第一行
        for (int j = 0; j <= n; j++) {
            dp[j] = j;
        }
        
        // 逐行计算
        for (int i = 1; i <= m; i++) {
            int prev = dp[0];
            dp[0] = i;
            
            for (int j = 1; j <= n; j++) {
                int temp = dp[j];
                
                if (word1[i-1] == word2[j-1]) {
                    dp[j] = prev;
                } else {
                    dp[j] = min({prev + 1, dp[j] + 1, dp[j-1] + 1});
                }
                
                prev = temp;
            }
        }
        
        return dp[n];
    }
};
```

</details>

---

## 🎯 动手实践

### 任务1：基础实现
实现标准DP版本，测试示例。

**测试用例**：
```cpp
Solution sol;

// 测试1
string word1 = "horse";
string word2 = "ros";
cout << sol.minDistance(word1, word2) << endl;  // 3

// 测试2
word1 = "intention";
word2 = "execution";
cout << sol.minDistance(word1, word2) << endl;  // 5
```

### 任务2：打印DP表
添加函数打印DP表，观察状态转移。

```cpp
void printDP(const vector<vector<int>>& dp, 
             const string& word1, const string& word2) {
    // TODO: 实现打印函数
}
```

### 任务3：追踪路径
修改代码，记录具体的操作步骤。

**提示**：用额外的数组记录每步的选择。

### 任务4：空间优化
实现滚动数组版本，理解空间优化技巧。

---

## 🔍 深入理解

### 三种操作的本质

#### 1. 替换
```
word1: a b c [d]
word2: a b c [e]

操作：替换 d → e
等价于：忽略这两个字符，继续匹配前面的
状态：dp[i-1][j-1] + 1
```

#### 2. 删除
```
word1: a b c [d]
word2: a b c

操作：删除 d
等价于：忽略word1的最后一个字符
状态：dp[i-1][j] + 1
```

#### 3. 插入
```
word1: a b c
word2: a b c [e]

操作：插入 e
等价于：忽略word2的最后一个字符
状态：dp[i][j-1] + 1
```

### 为什么插入和删除不是同一个？

**视角不同**：
- **删除**：从 word1 的角度，删除一个字符
- **插入**：从 word2 的角度，插入一个字符

**实际上**：
- 在 word1 删除 = 在 word2 插入
- 在 word1 插入 = 在 word2 删除

### DP表的对称性

```
dp[i][j] (word1 → word2) = dp[j][i] (word2 → word1)
```

### 空间优化的关键

**观察依赖关系**：
```
dp[i][j] 依赖于：
  - dp[i-1][j-1]  (左上)
  - dp[i-1][j]    (上)
  - dp[i][j-1]    (左)
```

**只需保存一行**：
- 用 `prev` 保存左上角的值
- 当前值就是上方
- `dp[j-1]` 就是左边

---

## 📊 复杂度分析

### 时间复杂度
- **O(m × n)**
- 两层循环，每个格子计算一次

### 空间复杂度
- **标准DP**：O(m × n)
- **优化版**：O(min(m, n))

---

## 🚀 进阶挑战

### 挑战1：输出具体操作
不仅返回最少操作数，还要输出具体的操作序列。

**提示**：记录每个状态的选择。

### 挑战2：不同操作成本
如果插入成本2，删除成本1，替换成本3，如何修改？

**提示**：修改状态转移方程。

### 挑战3：最长公共子序列
用编辑距离的思想解决LCS问题。

**提示**：只能删除，求最少删除次数。

### 挑战4：通配符匹配
扩展到支持通配符 `*` 和 `?`。

---

## 🐛 常见错误

### 错误1：索引混淆
```cpp
// ❌ 错误（dp数组索引从1开始）
if (word1[i] == word2[j]) {
    dp[i][j] = dp[i-1][j-1];
}

// ✅ 正确
if (word1[i-1] == word2[j-1]) {
    dp[i][j] = dp[i-1][j-1];
}
```

### 错误2：忘记初始化
```cpp
// ❌ 错误（第一行和第一列未初始化）
vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

// ✅ 正确
for (int i = 0; i <= m; i++) dp[i][0] = i;
for (int j = 0; j <= n; j++) dp[0][j] = j;
```

### 错误3：边界条件
```cpp
// ❌ 错误（未处理空字符串）
int m = word1.size();
int n = word2.size();
// 直接开始填表

// ✅ 正确
if (m == 0) return n;
if (n == 0) return m;
```

---

## 📝 自检清单

完成本题后，确保你能够：
- [ ] 理解编辑距离的定义
- [ ] 清晰解释三种操作的含义
- [ ] 写出状态定义和状态转移方程
- [ ] 正确初始化DP表
- [ ] 理解索引映射关系
- [ ] 能够进行空间优化
- [ ] 知道实际应用场景

---

## 🎓 知识拓展

### 相关题目

1. **最长公共子序列**（LeetCode 1143）
   - 类似的二维DP

2. **两个字符串的删除操作**（LeetCode 583）
   - 只能删除，编辑距离的简化版

3. **通配符匹配**（LeetCode 44）
   - 支持通配符的编辑距离

4. **正则表达式匹配**（LeetCode 10）
   - 更复杂的字符串匹配

### 实际应用

1. **拼写检查**
   - 找出编辑距离最小的单词

2. **DNA序列比对**
   - 生物信息学中的重要工具

3. **版本控制（Git diff）**
   - 计算文件差异

4. **机器翻译评估**
   - 评估翻译质量（BLEU score）

5. **语音识别**
   - 计算识别结果与真实文本的差异

### C++技巧总结

```cpp
// 1. min函数（三个数）
int minVal = min({a, b, c});  // C++11

// 2. 二维vector初始化
vector<vector<int>> dp(m, vector<int>(n, 0));

// 3. 引用传递（避免拷贝）
int helper(const string& word1, const string& word2);

// 4. 空间优化（滚动数组）
vector<int> dp(n + 1);
int prev = dp[0];  // 保存左上角
```

---

## 💬 总结

**编辑距离**是经典的二维DP问题，重点掌握：

1. **状态定义**：
   - `dp[i][j]` = word1前i个字符转换为word2前j个字符的最少操作数

2. **状态转移**：
   - 相同：`dp[i][j] = dp[i-1][j-1]`
   - 不同：`dp[i][j] = min(替换, 删除, 插入) + 1`

3. **初始化**：
   - `dp[0][j] = j`（插入）
   - `dp[i][0] = i`（删除）

4. **空间优化**：
   - 滚动数组优化到 O(n)

**面试建议**：
- 清晰解释三种操作
- 画出DP表演示
- 能够进行空间优化
- 知道实际应用场景

这是DP的经典题目，理解了它就理解了二维DP的精髓！🎯
