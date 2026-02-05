# 3. 无重复字符的最长子串 - 编程指导

## 🎯 学习目标
- 掌握滑动窗口算法
- 理解双指针的协作
- 学会哈希表优化查找

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：暴力枚举
<details>
<summary>点击查看提示1</summary>

**最简单的方法**：枚举所有子串

```cpp
int maxLength = 0;
for (int i = 0; i < n; i++) {
    for (int j = i; j < n; j++) {
        if (无重复字符(s[i..j])) {
            maxLength = max(maxLength, j - i + 1);
        }
    }
}
```

**复杂度**：O(n³)，太慢！

**优化思路**：能否用一次遍历？

</details>

---

### 🌟 提示 Level 2：滑动窗口
<details>
<summary>点击查看提示2</summary>

**核心思想**：维护一个无重复字符的窗口

```
s = "abcabcbb"
     [abc]      ← 窗口1
      [bca]     ← 窗口2（遇到重复'a'，收缩）
       [cab]    ← 窗口3
```

**两个指针**：
- `left`：窗口左边界
- `right`：窗口右边界

**操作**：
1. `right` 右移，扩展窗口
2. 遇到重复字符，`left` 右移，收缩窗口
3. 持续更新最大长度

</details>

---

### 🌟 提示 Level 3：哈希表优化
<details>
<summary>点击查看提示3</summary>

**如何快速判断重复？**

使用哈希表记录字符最后出现的位置：

```cpp
unordered_map<char, int> charIndex;
int left = 0, maxLength = 0;

for (int right = 0; right < s.length(); right++) {
    char c = s[right];
    
    // 如果字符已存在且在当前窗口内
    if (charIndex.count(c) && charIndex[c] >= left) {
        left = charIndex[c] + 1;  // 跳到重复字符的下一个位置
    }
    
    charIndex[c] = right;
    maxLength = max(maxLength, right - left + 1);
}
```

**关键**：`charIndex[c] >= left` 确保重复字符在窗口内。

</details>

---

### 🌟 提示 Level 4：完整实现
<details>
<summary>点击查看提示4</summary>

```cpp
int lengthOfLongestSubstring(string s) {
    unordered_map<char, int> charIndex;
    int maxLength = 0;
    int left = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        
        if (charIndex.count(c) && charIndex[c] >= left) {
            left = charIndex[c] + 1;
        }
        
        charIndex[c] = right;
        maxLength = max(maxLength, right - left + 1);
    }
    
    return maxLength;
}
```

</details>

---

## 🔨 动手实践

### 任务1：手动模拟
对 `s = "pwwkew"`，手动模拟滑动窗口过程。

### 任务2：实现代码
```cpp
int lengthOfLongestSubstring(string s) {
    // TODO: 实现滑动窗口 + 哈希表
}
```

### 任务3：测试用例
```cpp
s = "abcabcbb"  // 预期: 3
s = "bbbbb"     // 预期: 1
s = "pwwkew"    // 预期: 3
s = ""          // 预期: 0
s = "dvdf"      // 预期: 3
```

---

## 🎯 滑动窗口模板

```cpp
int left = 0;
哈希表/集合 window;

for (int right = 0; right < n; right++) {
    // 扩展窗口
    window.add(s[right]);
    
    // 收缩窗口
    while (窗口不满足条件) {
        window.remove(s[left]);
        left++;
    }
    
    // 更新答案
    ans = max(ans, right - left + 1);
}
```

---

## ✅ 自检清单

- [ ] 我理解了滑动窗口的原理
- [ ] 我知道何时扩展何时收缩窗口
- [ ] 我理解了哈希表的作用
- [ ] 我知道为什么要判断 `>= left`
- [ ] 我能独立实现代码

---

**下一步**：独立实现并对照 `solution.md`！
