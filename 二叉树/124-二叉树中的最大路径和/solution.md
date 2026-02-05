# 124. 二叉树中的最大路径和 - C++ 题解

## 方法：DFS

```cpp
#include <algorithm>
#include <climits>
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
    int maxSum = INT_MIN;

    int maxPathSum(TreeNode* root) {
        maxGain(root);
        return maxSum;
    }

    int maxGain(TreeNode* node) {
        if (!node) return 0;

        // 递归计算左右子节点的最大贡献值
        // 只有在最大贡献值大于 0 时，才会选取对应子节点
        int leftGain = max(maxGain(node->left), 0);
        int rightGain = max(maxGain(node->right), 0);

        // 节点的最大路径和取决于该节点的值与该节点的左右子节点的最大贡献值
        // 这条路径穿过当前节点（倒V型）
        int priceNewPath = node->val + leftGain + rightGain;

        // 更新答案
        maxSum = max(maxSum, priceNewPath);

        // 返回节点的最大贡献值
        // 只能选择一边（或者只选自己），因为要组成路径给父节点用
        return node->val + max(leftGain, rightGain);
    }
};
```
