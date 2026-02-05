# 39. 组合总和 - C++ 题解

## 方法：回溯 + 剪枝

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<vector<int>> ans;
    vector<int> path;

    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        // 排序是为了方便剪枝
        sort(candidates.begin(), candidates.end());
        backtrack(candidates, target, 0, 0);
        return ans;
    }

    void backtrack(vector<int>& candidates, int target, int sum, int start) {
        if (sum == target) {
            ans.push_back(path);
            return;
        }

        for (int i = start; i < candidates.size(); ++i) {
            // 剪枝：如果加上当前数字已经超过目标，后面的数字更大，肯定也超过
            if (sum + candidates[i] > target) {
                break;
            }

            path.push_back(candidates[i]);
            // 关键：下一层递归的 start 依然是 i，表示可以重复选择当前数字
            backtrack(candidates, target, sum + candidates[i], i);
            path.pop_back(); // 回溯
        }
    }
};
```
