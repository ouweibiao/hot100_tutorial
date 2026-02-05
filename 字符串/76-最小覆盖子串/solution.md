# 76. 最小覆盖子串 - C++ 题解

## 方法：滑动窗口

这是面试中非常考验代码基本功的一道题，逻辑比较多，容易写错。

```cpp
#include <string>
#include <unordered_map>
#include <climits>

using namespace std;

class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<char, int> need, window;
        // 统计 t 中字符的需求
        for (char c : t) need[c]++;
        
        int left = 0, right = 0;
        int valid = 0; // 记录满足需求的字符个数
        
        // 记录最小覆盖子串的起始索引及长度
        int start = 0, len = INT_MAX;
        
        while (right < s.size()) {
            // c 是将移入窗口的字符
            char c = s[right];
            // 右移窗口
            right++;
            
            // 进行窗口内数据的一系列更新
            if (need.count(c)) {
                window[c]++;
                if (window[c] == need[c])
                    valid++;
            }
            
            // 判断左侧窗口是否要收缩
            // 当 valid 等于 need 的大小时，说明所有字符都满足了，尝试缩小窗口
            while (valid == need.size()) {
                // 在这里更新最小覆盖子串
                if (right - left < len) {
                    start = left;
                    len = right - left;
                }
                
                // d 是将移出窗口的字符
                char d = s[left];
                // 左移窗口
                left++;
                
                // 进行窗口内数据的一系列更新
                if (need.count(d)) {
                    if (window[d] == need[d])
                        valid--;
                    window[d]--;
                }
            }
        }
        
        // 返回最小覆盖子串
        return len == INT_MAX ? "" : s.substr(start, len);
    }
};
```

### 关键点解析
1. **Valid 变量**：避免了每次都去遍历 `need` 哈希表来检查是否满足条件。只有当某个字符的数量**刚好**达到要求时，`valid` 才加 1。
2. **窗口收缩条件**：`while (valid == need.size())`。只要满足覆盖条件，就一直尝试缩小左边界，直到不再满足为止。
3. **结果更新时机**：在**收缩窗口之前**（即满足条件时）更新最小长度。
4. **左闭右开区间**：`[left, right)`，初始化都为 0，`right` 先加，长度为 `right - left`。
