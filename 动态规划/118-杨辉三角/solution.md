# 118. 杨辉三角 - C++ 题解

## 方法：模拟

```cpp
#include <vector>

using namespace std;

class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ans(numRows);
        
        for (int i = 0; i < numRows; ++i) {
            // 第 i 行有 i + 1 个元素
            ans[i].resize(i + 1);
            
            // 首尾元素为 1
            ans[i][0] = 1;
            ans[i][i] = 1;
            
            // 中间元素 = 上一行对应两个元素之和
            for (int j = 1; j < i; ++j) {
                ans[i][j] = ans[i - 1][j - 1] + ans[i - 1][j];
            }
        }
        
        return ans;
    }
};
```
