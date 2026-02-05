# 437. 路径总和 III - C++ 题解

## 方法：前缀和 + 回溯

```cpp
#include <unordered_map>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};

class Solution {
public:
    unordered_map<long long, int> prefix;
    int ans = 0;

    int pathSum(TreeNode* root, int targetSum) {
        prefix[0] = 1; // 前缀和为0的路径有一条（空路径），用于处理从根节点开始的情况
        dfs(root, 0, targetSum);
        return ans;
    }

    void dfs(TreeNode* root, long long curr, int targetSum) {
        if (!root) return;

        curr += root->val;

        // 查找是否存在前缀和 = curr - targetSum
        if (prefix.count(curr - targetSum)) {
            ans += prefix[curr - targetSum];
        }

        // 记录当前前缀和
        prefix[curr]++;

        dfs(root->left, curr, targetSum);
        dfs(root->right, curr, targetSum);

        // 回溯：离开当前节点时，移除当前前缀和，以免影响其他分支
        prefix[curr]--;
    }
};
```
