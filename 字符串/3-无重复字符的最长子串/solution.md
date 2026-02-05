# 3. 无重复字符的最长子串

## 📝 题目描述

给定一个字符串 `s`，请你找出其中不含有重复字符的**最长子串**的长度。

**示例 1：**
```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例 2：**
```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例 3：**
```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是子串的长度，"pwke" 是一个子序列，不是子串。
```

**约束条件：**
- `0 <= s.length <= 5 * 10^4`
- `s` 由英文字母、数字、符号和空格组成

---

## ⭐ 解法：滑动窗口 + 哈希表

### 思路

使用**滑动窗口**维护一个无重复字符的子串：
- **右指针**：扩展窗口，加入新字符
- **左指针**：收缩窗口，移除重复字符
- **哈希表**：记录字符最后出现的位置

**关键**：
- 当遇到重复字符时，左指针跳到重复字符的下一个位置
- 持续更新最大长度

### 代码实现
```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> charIndex;  // 字符 -> 最后出现的索引
        int maxLength = 0;
        int left = 0;
        
        for (int right = 0; right < s.length(); right++) {
            char c = s[right];
            
            // 如果字符已存在且在当前窗口内
            if (charIndex.count(c) && charIndex[c] >= left) {
                left = charIndex[c] + 1;  // 左指针跳到重复字符的下一个位置
            }
            
            charIndex[c] = right;  // 更新字符位置
            maxLength = max(maxLength, right - left + 1);
        }
        
        return maxLength;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，每个字符最多被访问两次
- **空间复杂度**：O(min(m, n))，m 是字符集大小

---

## 🎯 核心知识点

### 1. 滑动窗口模板

```cpp
int left = 0;
for (int right = 0; right < n; right++) {
    // 扩展窗口：加入 s[right]
    窗口.add(s[right]);
    
    // 收缩窗口：移除重复或不满足条件的元素
    while (窗口不满足条件) {
        窗口.remove(s[left]);
        left++;
    }
    
    // 更新答案
    更新最优解(right - left + 1);
}
```

### 2. 为什么用哈希表？

**作用**：
- 记录字符最后出现的位置
- O(1) 时间判断字符是否在窗口内

**示例**：
```
s = "abcabcbb"
      ↑   ↑
    left right

遇到第二个 'a'：
- charIndex['a'] = 0（在窗口内）
- left 跳到 charIndex['a'] + 1 = 1
```

### 3. 左指针的跳跃

**关键判断**：
```cpp
if (charIndex.count(c) && charIndex[c] >= left)
```

**为什么要判断 `>= left`？**
```
s = "abba"
       ↑
     left=2, right=3

charIndex['a'] = 0（不在窗口内）
不需要移动 left
```

---

## 🔍 图解过程

以 `s = "abcabcbb"` 为例：

```
Step 1: right=0, s[0]='a'
  窗口: [a]
  left=0, maxLength=1

Step 2: right=1, s[1]='b'
  窗口: [ab]
  left=0, maxLength=2

Step 3: right=2, s[2]='c'
  窗口: [abc]
  left=0, maxLength=3

Step 4: right=3, s[3]='a' (重复！)
  charIndex['a']=0, >= left
  left = 0 + 1 = 1
  窗口: [bca]
  maxLength=3

Step 5: right=4, s[4]='b' (重复！)
  charIndex['b']=1, >= left
  left = 1 + 1 = 2
  窗口: [cab]
  maxLength=3

...

最长子串长度: 3
```

---

## 🎓 变式题目

### 1. 至多包含 K 个不同字符的最长子串（LeetCode 340）
找到至多包含 k 个不同字符的最长子串。

### 2. 最小覆盖子串（LeetCode 76）
找到包含另一个字符串所有字符的最小子串。

### 3. 字符串的排列（LeetCode 567）
判断 s2 是否包含 s1 的排列。

---

## 📌 记忆要点

- **模式**：滑动窗口 + 哈希表
- **窗口维护**：左指针跳跃，右指针扩展
- **哈希表作用**：记录字符位置，O(1) 查找
- **复杂度**：O(n) 时间，O(min(m,n)) 空间

---

## 🔨 常见错误

### 错误1：忘记判断 left 边界
```cpp
// ❌ 错误：可能跳到窗口外的位置
if (charIndex.count(c)) {
    left = charIndex[c] + 1;
}

// ✅ 正确：只在窗口内才跳转
if (charIndex.count(c) && charIndex[c] >= left) {
    left = charIndex[c] + 1;
}
```

### 错误2：更新顺序错误
```cpp
// ❌ 错误：先更新长度，再更新索引
maxLength = max(maxLength, right - left + 1);
charIndex[c] = right;

// ✅ 正确：先更新索引，再更新长度
charIndex[c] = right;
maxLength = max(maxLength, right - left + 1);
// 实际上两种顺序都可以，关键是逻辑清晰
```

---

**标签**：`哈希表` `字符串` `滑动窗口` `medium`
