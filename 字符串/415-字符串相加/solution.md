# 415. 字符串相加 - C++ 题解

## 方法：模拟竖式加法

```cpp
#include <string>
#include <algorithm>

using namespace std;

class Solution {
public:
    string addStrings(string num1, string num2) {
        int i = num1.size() - 1;
        int j = num2.size() - 1;
        int carry = 0;
        string res = "";
        
        // 循环条件包含 carry != 0，处理最高位进位的情况 (如 9 + 1 = 10)
        while (i >= 0 || j >= 0 || carry != 0) {
            // 如果指针越界，视为 0
            int x = i >= 0 ? num1[i] - '0' : 0;
            int y = j >= 0 ? num2[j] - '0' : 0;
            
            int sum = x + y + carry;
            
            // 当前位
            res += to_string(sum % 10);
            // 进位
            carry = sum / 10;
            
            i--;
            j--;
        }
        
        // 计算时是 低位->高位，追加到 res 是从左到右，所以结果是反的
        reverse(res.begin(), res.end());
        return res;
    }
};
```

### 优化点
- `res += to_string(sum % 10)` 会产生临时字符串对象，更高效的写法是 `res.push_back((sum % 10) + '0')`。
- 也可以先 `reserve` 足够大的空间，避免 `string` 频繁扩容。
