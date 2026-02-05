# 230. 二叉搜索树中第K小的元素 - C++ 题解

## 方法：中序遍历 (递归)

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
    int count = 0;
    int ans = 0;

    int kthSmallest(TreeNode* root, int k) {
        count = 0;
        inorder(root, k);
        return ans;
    }

    void inorder(TreeNode* root, int k) {
        if (!root) return;

        inorder(root->left, k);

        // 剪枝：如果已经找到了，就不需要继续了
        if (count >= k) return;

        count++;
        if (count == k) {
            ans = root->val;
            return;
        }

        inorder(root->right, k);
    }
};
```
