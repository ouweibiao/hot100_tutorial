# 739. 每日温度 - C++ 题解

## 方法：单调栈

这是单调栈最入门也最经典的题目。

```cpp
#include <vector>
#include <stack>

using namespace std;

class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        int n = temperatures.size();
        vector<int> result(n, 0); // 初始化为 0
        stack<int> st; // 单调栈，存下标
        
        for (int i = 0; i < n; ++i) {
            // 当栈不为空，且当前温度大于栈顶下标对应的温度
            while (!st.empty() && temperatures[i] > temperatures[st.top()]) {
                int prev_index = st.top();
                st.pop();
                // 计算距离
                result[prev_index] = i - prev_index;
            }
            // 当前下标入栈
            st.push(i);
        }
        
        return result;
    }
};
```

### 代码模板总结
寻找**右边第一个更大**元素：
```cpp
stack<int> st;
for (int i = 0; i < nums.size(); ++i) {
    while (!st.empty() && nums[i] > nums[st.top()]) {
        // 找到了比 st.top() 更大的元素 nums[i]
        int idx = st.top(); st.pop();
        result[idx] = ...;
    }
    st.push(i);
}
```
寻找**右边第一个更小**元素：
将 `>` 改为 `<` 即可。
