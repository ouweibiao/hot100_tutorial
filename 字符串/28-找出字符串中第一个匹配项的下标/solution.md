# 28. 找出字符串中第一个匹配项的下标 - C++ 题解

## 方法：KMP 算法

这是标准的 KMP 模板，建议熟练背诵。

```cpp
#include <string>
#include <vector>

using namespace std;

class Solution {
public:
    // 1. 获取 Next 数组
    void getNext(int* next, const string& s) {
        int j = 0; // j 代表前缀末尾位置，也代表当前的最长相等前后缀长度
        next[0] = 0;
        
        // i 从 1 开始，代表后缀末尾位置
        for(int i = 1; i < s.size(); i++) {
            // 前后缀不匹配，j 回退
            while(j > 0 && s[i] != s[j]) {
                j = next[j - 1];
            }
            // 匹配，j 前进
            if(s[i] == s[j]) {
                j++;
            }
            // 更新 next[i]
            next[i] = j;
        }
    }

    int strStr(string haystack, string needle) {
        if (needle.size() == 0) {
            return 0;
        }
        
        int next[needle.size()];
        getNext(next, needle);
        
        int j = 0; // j 指向 needle
        for (int i = 0; i < haystack.size(); i++) { // i 指向 haystack
            // 不匹配，j 回退
            while(j > 0 && haystack[i] != needle[j]) {
                j = next[j - 1];
            }
            // 匹配，j 前进
            if(haystack[i] == needle[j]) {
                j++;
            }
            // j 走到了 needle 末尾，说明匹配成功
            if(j == needle.size()) {
                return (i - needle.size() + 1);
            }
        }
        
        return -1;
    }
};
```

### 关键逻辑解释
1. **Next 数组构建**：
   - `j` 既是前缀的末尾下标，也是当前长度为 `i` 的子串的最长相等前后缀长度。
   - `while(j > 0 && s[i] != s[j]) j = next[j-1];` 是回退的核心，利用之前计算好的前缀信息。
2. **匹配过程**：
   - 逻辑与构建 Next 数组几乎一样，只是把 `s[i]` 换成了 `haystack[i]`，`s[j]` 换成了 `needle[j]`。

### 替代方案：find 函数
如果面试允许使用 STL，一行代码即可：
```cpp
return haystack.find(needle);
```
但面试官通常就是为了考 KMP。
