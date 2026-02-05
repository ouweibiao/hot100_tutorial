# 84. 柱状图中最大的矩形 - C++ 题解

## 方法：单调栈 + 哨兵优化

这道题是单调栈的巅峰之作，理解了这道题，接雨水(42题)等都不在话下。

```cpp
#include <vector>
#include <stack>
#include <algorithm>

using namespace std;

class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int n = heights.size();
        // 1. 首尾加哨兵 0
        // 首部加 0：防止栈空，简化逻辑
        // 尾部加 0：强制将栈中剩余元素弹出计算
        heights.insert(heights.begin(), 0);
        heights.push_back(0);
        
        stack<int> st; // 单调递增栈
        st.push(0); // 先把首部哨兵入栈
        
        int maxArea = 0;
        
        // 2. 遍历数组 (注意现在的 size 是 n + 2)
        for (int i = 1; i < heights.size(); ++i) {
            // 当当前高度 < 栈顶高度时，说明栈顶元素的右边界确定了
            while (heights[i] < heights[st.top()]) {
                int mid = st.top();
                st.pop();
                
                int h = heights[mid];
                int left = st.top(); // 左边界
                int right = i;       // 右边界
                int w = right - left - 1;
                
                maxArea = max(maxArea, h * w);
            }
            st.push(i);
        }
        
        return maxArea;
    }
};
```

### 逻辑拆解
假设 heights = `[0, 2, 1, 5, 6, 2, 3, 0]` (已加哨兵)
1. `st=[0]`.
2. `i=1 (2)`: 2 > 0, push. `st=[0, 1]`.
3. `i=2 (1)`: 1 < 2.
   - Pop 1 (height=2). Left=0, Right=2. Width=1. Area=2.
   - 1 > 0, push. `st=[0, 2]`.
4. ...
5. `i=5 (2)`: 2 < 6.
   - Pop 4 (height=6). Left=3, Right=5. Width=1. Area=6.
   - Pop 3 (height=5). Left=2, Right=5. Width=2. Area=10.
   - 2 > 1, push. `st=[0, 2, 5]`.
6. ...
7. 最后遇到尾部 0，所有元素依次出栈结算。
