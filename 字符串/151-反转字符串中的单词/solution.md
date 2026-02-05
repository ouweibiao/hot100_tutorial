# 151. 反转字符串中的单词 - C++ 题解

## 方法一：利用 stringstream (工程常用)

```cpp
#include <string>
#include <vector>
#include <sstream>
#include <algorithm>

using namespace std;

class Solution {
public:
    string reverseWords(string s) {
        stringstream ss(s);
        string word;
        vector<string> words;
        
        // 自动分割空格
        while (ss >> word) {
            words.push_back(word);
        }
        
        string res = "";
        for (int i = words.size() - 1; i >= 0; --i) {
            res += words[i];
            if (i > 0) res += " ";
        }
        
        return res;
    }
};
```

## 方法二：原地反转 (面试高分)

```cpp
#include <string>
#include <algorithm>

using namespace std;

class Solution {
public:
    string reverseWords(string s) {
        // 1. 移除多余空格
        removeExtraSpaces(s);
        
        // 2. 整体反转
        reverse(s.begin(), s.end());
        
        // 3. 局部反转（每个单词）
        int start = 0;
        for (int i = 0; i <= s.size(); ++i) {
            // 到达字符串末尾或遇到空格，说明一个单词结束
            if (i == s.size() || s[i] == ' ') {
                reverse(s.begin() + start, s.begin() + i);
                start = i + 1;
            }
        }
        
        return s;
    }
    
    // 辅助函数：移除多余空格 (快慢指针)
    void removeExtraSpaces(string& s) {
        int slow = 0;
        for (int fast = 0; fast < s.size(); ++fast) {
            // 跳过连续空格
            if (s[fast] != ' ') {
                // 如果不是第一个单词，需要先手动加一个空格
                if (slow != 0) {
                    s[slow++] = ' ';
                }
                
                // 复制当前单词
                while (fast < s.size() && s[fast] != ' ') {
                    s[slow++] = s[fast++];
                }
            }
        }
        // 调整大小
        s.resize(slow);
    }
};
```

### 核心技巧
- **快慢指针去空格**：非常经典的数组操作技巧。`slow` 指向下一个要写入的位置，`fast` 寻找下一个非空字符。
- **三次反转**：这个思想在《剑指 Offer》和《编程珠玑》中都有提及，非常经典。
