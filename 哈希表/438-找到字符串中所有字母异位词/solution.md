# 438. 找到字符串中所有字母异位词 - C++ 题解

## 方法：滑动窗口 + 数组哈希

使用 `vector<int>` 作为哈希表，效率极高。

```cpp
#include <vector>
#include <string>

using namespace std;

class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        int n = s.size();
        int m = p.size();
        
        if (n < m) return {};
        
        // 记录 p 的字符计数
        vector<int> pCount(26, 0);
        // 记录当前窗口 s 的字符计数
        vector<int> sCount(26, 0);
        
        // 统计 p 和 s 的第一个窗口
        for (int i = 0; i < m; ++i) {
            pCount[p[i] - 'a']++;
            sCount[s[i] - 'a']++;
        }
        
        vector<int> result;
        // 检查第一个窗口
        if (sCount == pCount) {
            result.push_back(0);
        }
        
        // 开始滑动
        // i 是窗口即将移入的右边界字符索引
        // i - m 是窗口即将移出的左边界字符索引
        for (int i = m; i < n; ++i) {
            // 加入右边的字符
            sCount[s[i] - 'a']++;
            // 移除左边的字符
            sCount[s[i - m] - 'a']--;
            
            // 比较当前窗口
            if (sCount == pCount) {
                result.push_back(i - m + 1);
            }
        }
        
        return result;
    }
};
```

### 代码解释
1. **初始化**：先统计 `p` 和 `s` 前 `m` 个字符。
2. **比较**：C++ 中 `vector` 可以直接使用 `==` 比较，它会逐个元素比较，非常方便。
3. **滑动**：每次循环，右边进一个，左边出一个，保持窗口大小不变。
