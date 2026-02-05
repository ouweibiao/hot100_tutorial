# 236. 二叉树的最近公共祖先 - C++ 题解

## 方法：递归

```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // 1. 终止条件：如果是空，或者找到了 p 或 q，直接返回
        if (!root || root == p || root == q) return root;

        // 2. 递归查找左右子树
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);

        // 3. 处理结果
        if (left && right) {
            // p 和 q 分别在两侧，root 即为 LCA
            return root;
        }
        
        // 都在左侧（或只找到左侧），返回 left
        // 都在右侧（或只找到右侧），返回 right
        return left ? left : right;
    }
};
```
