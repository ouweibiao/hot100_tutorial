# 239. 滑动窗口最大值 - C++ 题解

## 方法：单调队列 (Deque)

```cpp
#include <vector>
#include <deque>

using namespace std;

class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        // 双端队列，存下标，对应的值单调递减
        deque<int> dq;
        vector<int> result;
        
        for (int i = 0; i < nums.size(); ++i) {
            // 1. 入队前，把比当前元素小的队尾元素全部弹出
            // 因为当前元素更大且更晚过期，那些小的元素永远不会成为最大值
            while (!dq.empty() && nums[i] >= nums[dq.back()]) {
                dq.pop_back();
            }
            
            // 2. 当前元素入队
            dq.push_back(i);
            
            // 3. 检查队头最大值是否过期（滑出窗口）
            // 窗口范围是 [i - k + 1, i]，如果队头下标 < i - k + 1，则过期
            if (dq.front() <= i - k) {
                dq.pop_front();
            }
            
            // 4. 记录结果（当窗口完全形成后开始记录）
            if (i >= k - 1) {
                result.push_back(nums[dq.front()]);
            }
        }
        
        return result;
    }
};
```

### 为什么存下标而不是存值？
存下标不仅可以找到值（`nums[index]`），还可以方便地判断元素是否过期（`index <= i - k`）。如果只存值，很难判断是否过期（特别是当窗口内有重复最大值时）。
