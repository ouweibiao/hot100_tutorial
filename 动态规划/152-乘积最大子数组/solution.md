# 152. 乘积最大子数组 - C++ 题解

## 方法：动态规划 (滚动数组优化)

```cpp
#include <vector>
#include <algorithm>
#include <climits>

using namespace std;

class Solution {
public:
    int maxProduct(vector<int>& nums) {
        if (nums.empty()) return 0;
        
        // 维护当前位置的最大值和最小值
        double maxF = nums[0];
        double minF = nums[0];
        double ans = nums[0];
        
        for (int i = 1; i < nums.size(); ++i) {
            double mx = maxF, mn = minF;
            
            // 状态转移：当前值、当前值*前最大、当前值*前最小，三者取最大/最小
            maxF = max((double)nums[i], max(mx * nums[i], mn * nums[i]));
            minF = min((double)nums[i], min(mx * nums[i], mn * nums[i]));
            
            ans = max(ans, maxF);
        }
        
        return (int)ans;
    }
};
```

### 为什么用 double?
LeetCode 的测试用例中，中间乘积可能会短暂溢出 `int` 或 `long long` 的范围（虽然最终结果在 int 范围内），使用 `double` 可以避免溢出问题，或者在计算时判断是否溢出。但在本题的标准解法中，通常题目保证了结果在 int 范围内，如果中间过程溢出，可以用 `long long`。

**更稳妥的 int 版本（如果不涉及极大数溢出）：**

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        if (nums.empty()) return 0;
        
        long long maxF = nums[0];
        long long minF = nums[0];
        int ans = nums[0];
        
        for (int i = 1; i < nums.size(); ++i) {
            long long mx = maxF, mn = minF;
            maxF = max((long long)nums[i], max(mx * nums[i], mn * nums[i]));
            minF = min((long long)nums[i], min(mx * nums[i], mn * nums[i]));
            
            // 每次比较更新结果时，maxF 有可能因为中间计算变得很大，
            // 但题目保证最终结果是 int，所以如果中间过程超过 int 范围也没关系，
            // 只要最终 ans 是有效的即可。
            // 这里用 long long 防御一下中间过程溢出 int。
            if (maxF > INT_MAX) maxF = INT_MAX; // 截断防止继续溢出（视题目数据而定）
            if (minF < INT_MIN) minF = INT_MIN;
            
            ans = max(ans, (int)maxF);
        }
        
        return ans;
    }
};
```
注：实际上本题数据规模较小，直接用 int 或 double 都能过。上面的 double 写法在 LC 上是常见的技巧。
