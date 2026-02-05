# 763. 划分字母区间

## 题目描述

给你一个字符串 `s` 。我们要把这个字符串划分为尽可能多的片段，同一字母最多出现在一个片段中。
注意，划分结果需要满足：将所有划分结果按顺序连接，得到的字符串仍然是 `s` 。
返回一个表示每个字符串片段的长度的列表。

**示例 1：**
```
输入：s = "ababcbacadefegdehijhklij"
输出：[9,7,8]
解释：
划分结果为 "ababcbaca", "defegde", "hijhklij"。
每个字母最多出现在一个片段中。
像 "ababcbacadefegde", "hijhklij" 这样的划分是错误的，因为划分的片段数较少。
```

---

## 解法：贪心 + 哈希表

### 核心思路
为了确保“同一字母最多出现在一个片段中”，这意味着：**如果一个片段包含了字母 'a'，那么这个片段必须包含 'a' 在字符串中最后出现的位置**。

**算法步骤**：
1. **第一次遍历**：记录每个字符最后出现的下标 `last[char] = index`。
2. **第二次遍历**：
   - 维护 `start` 和 `end`，表示当前片段的起始和结束位置。
   - 遍历字符串，对于当前字符 `c`，更新当前片段的结束位置 `end = max(end, last[c])`。
   - 如果当前下标 `i` 等于 `end`，说明当前片段已经包含了其中所有字符的最后出现位置，可以切分了。
   - 记录长度 `end - start + 1`，并更新 `start = i + 1`。

### 代码实现

```cpp
#include <vector>
#include <string>
#include <algorithm>
#include <unordered_map>
using namespace std;

class Solution {
public:
    vector<int> partitionLabels(string s) {
        // 1. 记录每个字符最后出现的位置
        int last[26];
        for (int i = 0; i < s.size(); ++i) {
            last[s[i] - 'a'] = i;
        }
        
        vector<int> result;
        int start = 0, end = 0;
        
        // 2. 遍历字符串，进行划分
        for (int i = 0; i < s.size(); ++i) {
            // 更新当前片段需要到达的最远位置
            end = max(end, last[s[i] - 'a']);
            
            // 如果到达了当前片段的结束位置
            if (i == end) {
                result.push_back(end - start + 1);
                start = end + 1; // 更新下一个片段的起始位置
            }
        }
        
        return result;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，其中 n 是字符串长度。我们需要遍历两次字符串（一次预处理，一次划分）。
- **空间复杂度**：O(1)，虽然用了数组，但大小固定为 26。

### 易错点
- 必须先预处理一遍得到 `last` 数组，不能边遍历边判断。
- 更新 `start` 时要在切分后更新为 `i + 1`。

---

## 总结
- 这道题利用了**贪心**思想：为了让片段尽可能多，我们只在“不得不切分”的时候（即当前片段已包含了所有涉及字符的全部出现）才切分。
- 类似于跳跃游戏，维护一个“最远边界”。
