# 42. 接雨水 - C++ 题解

## 方法一：双指针 (推荐，最优解)

这是面试中最希望看到的解法，代码简练，时空复杂度最优。

```cpp
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        if (n == 0) return 0;
        
        int left = 0, right = n - 1;
        int left_max = 0, right_max = 0;
        int ans = 0;
        
        while (left < right) {
            // 谁小移动谁，因为短板决定盛水量
            if (height[left] < height[right]) {
                // 更新左边最大值
                if (height[left] >= left_max) {
                    left_max = height[left];
                } else {
                    // 当前列能接的水 = 左边最大值 - 当前高度
                    ans += (left_max - height[left]);
                }
                left++;
            } else {
                // 更新右边最大值
                if (height[right] >= right_max) {
                    right_max = height[right];
                } else {
                    ans += (right_max - height[right]);
                }
                right--;
            }
        }
        
        return ans;
    }
};
```

## 方法二：单调栈 (通用思想)

虽然代码略长，但这是"接雨水"这类题目最通用的模型。

```cpp
#include <vector>
#include <stack>
#include <algorithm>

using namespace std;

class Solution {
public:
    int trap(vector<int>& height) {
        int ans = 0;
        stack<int> st; // 单调递减栈，存下标
        
        for (int i = 0; i < height.size(); ++i) {
            // 当出现比栈顶高的元素时，说明形成了一个凹槽的右壁
            while (!st.empty() && height[i] > height[st.top()]) {
                int bottom_idx = st.top();
                st.pop();
                
                // 如果栈空了，说明没有左壁，接不住水
                if (st.empty()) break;
                
                int left_idx = st.top();
                
                // 计算宽度：右壁下标 - 左壁下标 - 1
                int w = i - left_idx - 1;
                // 计算高度：左右壁较矮者 - 坑底高度
                int h = min(height[left_idx], height[i]) - height[bottom_idx];
                
                ans += w * h;
            }
            st.push(i);
        }
        
        return ans;
    }
};
```
