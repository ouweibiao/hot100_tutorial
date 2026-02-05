# 543. 二叉树的直径 - C++ 题解

## 方法：DFS (后序遍历)

```cpp
#include <algorithm>
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
    int ans = 0;

    int diameterOfBinaryTree(TreeNode* root) {
        ans = 0;
        depth(root);
        return ans;
    }

    // 返回以 root 为根的子树的深度（节点数）
    int depth(TreeNode* root) {
        if (!root) return 0; // 空节点深度为 0

        int L = depth(root->left);  // 左子树深度
        int R = depth(root->right); // 右子树深度

        // 更新全局最大直径：左子树深度 + 右子树深度
        // 这条路径经过当前 root 节点
        ans = max(ans, L + R);

        // 返回当前节点的最大深度给父节点使用
        return max(L, R) + 1;
    }
};
```
