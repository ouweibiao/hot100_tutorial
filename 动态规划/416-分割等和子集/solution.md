# 416. 分割等和子集 - C++ 题解

## 方法：0-1 背包 (一维空间优化)

```cpp
#include <vector>
#include <numeric>
#include <algorithm>

using namespace std;

class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = accumulate(nums.begin(), nums.end(), 0);
        
        // 如果总和是奇数，不可能平分
        if (sum % 2 != 0) return false;
        
        int target = sum / 2;
        
        // dp[j] 表示是否能凑出和为 j
        vector<bool> dp(target + 1, false);
        dp[0] = true; // 和为 0 总是可以凑出（不选任何元素）
        
        for (int num : nums) {
            // 从大到小遍历，避免重复使用同一个 num
            for (int j = target; j >= num; --j) {
                dp[j] = dp[j] || dp[j - num];
            }
            
            // 剪枝：如果已经能凑出 target，提前结束
            if (dp[target]) return true;
        }
        
        return dp[target];
    }
};
```
