# 101. 对称二叉树 - C++ 题解

## 方法：递归

```cpp
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
    bool isSymmetric(TreeNode* root) {
        if (!root) return true;
        return check(root->left, root->right);
    }
    
    bool check(TreeNode* p, TreeNode* q) {
        // 1. 都为空，对称
        if (!p && !q) return true;
        
        // 2. 一个空一个不空，不对称
        if (!p || !q) return false;
        
        // 3. 值不相等，不对称
        if (p->val != q->val) return false;
        
        // 4. 递归检查：外侧对左外侧，内侧对内侧
        return check(p->left, q->right) && check(p->right, q->left);
    }
};
```
