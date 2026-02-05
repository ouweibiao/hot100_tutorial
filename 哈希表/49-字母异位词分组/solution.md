# 49. 字母异位词分组

## 题目描述

给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。

**字母异位词** 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。

**示例 1:**
```
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

**示例 2:**
```
输入: strs = [""]
输出: [[""]]
```

**示例 3:**
```
输入: strs = ["a"]
输出: [["a"]]
```

---

## 解法一：排序 + 哈希表

### 核心思路
1. 遍历每个字符串。
2. 将字符串进行**排序**，得到标准形式（key）。
3. 将原始字符串存入哈希表中对应的 key 下。
4. 最后将哈希表中的所有 value 收集起来。

### 代码实现
```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> mp;
        
        // 1. 分组
        for (const string& s : strs) {
            string key = s;
            sort(key.begin(), key.end());
            mp[key].push_back(s);
        }
        
        // 2. 收集结果
        vector<vector<string>> result;
        for (auto& pair : mp) {
            result.push_back(pair.second);
        }
        
        return result;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(N * K log K)，其中 N 是字符串数量，K 是字符串最大长度。排序需要 K log K。
- **空间复杂度**：O(N * K)，存储哈希表。

---

## 解法二：计数 + 哈希表（自定义Key）

### 核心思路
不排序，而是统计每个字符出现的次数（26个字母）。
将统计结果转换为唯一的字符串作为 key。
例如：`eat` -> `1#0#0#0#1#...#1...` (表示 a:1, e:1, t:1)

### 代码实现
```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> mp;
        
        for (const string& s : strs) {
            // 统计频率
            vector<int> count(26, 0);
            for (char c : s) {
                count[c - 'a']++;
            }
            
            // 生成 Key
            string key = "";
            for (int i = 0; i < 26; i++) {
                // 使用特殊字符分隔，避免歧义
                key += "#" + to_string(count[i]);
            }
            
            mp[key].push_back(s);
        }
        
        vector<vector<string>> result;
        for (auto& pair : mp) {
            result.push_back(pair.second);
        }
        
        return result;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(N * K)，只需要遍历每个字符，不需要排序。
- **空间复杂度**：O(N * K)。

---

## 总结

- **排序法**：代码最简单，最容易想到，面试首选。
- **计数法**：当字符串特别长时（K很大），比排序法更快，但 Key 的生成稍微麻烦点。
- **哈希表** (`unordered_map`) 是解决此类"分组/归类"问题的核心工具。

---

**标签**：`哈希表` `字符串` `排序` `medium`
