# 114. 二叉树展开为链表 - C++ 题解

## 方法：原地修改 (寻找前驱节点)

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
    void flatten(TreeNode* root) {
        TreeNode* curr = root;
        
        while (curr) {
            if (curr->left) {
                // 找到左子树的最右节点
                TreeNode* predecessor = curr->left;
                while (predecessor->right) {
                    predecessor = predecessor->right;
                }
                
                // 将当前节点的右子树接到 predecessor 的右边
                predecessor->right = curr->right;
                
                // 将左子树移到右边
                curr->right = curr->left;
                curr->left = nullptr;
            }
            
            // 继续处理下一个节点
            curr = curr->right;
        }
    }
};
```
