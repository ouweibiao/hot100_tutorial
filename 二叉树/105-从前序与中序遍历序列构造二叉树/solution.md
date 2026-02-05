# 105. 从前序与中序遍历序列构造二叉树 - C++ 题解

## 方法：递归 + 哈希表

```cpp
#include <vector>
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
    unordered_map<int, int> indexMap;

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        // 构造哈希表，快速定位根节点在中序遍历中的位置
        for (int i = 0; i < n; ++i) {
            indexMap[inorder[i]] = i;
        }
        return myBuild(preorder, inorder, 0, n - 1, 0, n - 1);
    }

    TreeNode* myBuild(const vector<int>& preorder, const vector<int>& inorder, 
                      int preorder_left, int preorder_right, 
                      int inorder_left, int inorder_right) {
        
        if (preorder_left > preorder_right) {
            return nullptr;
        }

        // 前序遍历的第一个节点就是根节点
        int preorder_root = preorder_left;
        // 在中序遍历中定位根节点
        int inorder_root = indexMap[preorder[preorder_root]];
        
        // 建立根节点
        TreeNode* root = new TreeNode(preorder[preorder_root]);

        // 得到左子树中的节点数目
        int size_left_subtree = inorder_root - inorder_left;

        // 递归构造左子树
        // 先序遍历中「从 左边界+1 开始的 size_left_subtree」个元素就对应了中序遍历中「从 左边界 开始到 根节点定位-1」的元素
        root->left = myBuild(preorder, inorder, 
                             preorder_left + 1, preorder_left + size_left_subtree, 
                             inorder_left, inorder_root - 1);

        // 递归构造右子树
        // 先序遍历中「从 左边界+1+左子树节点数目 开始到 右边界」的元素就对应了中序遍历中「从 根节点定位+1 到 右边界」的元素
        root->right = myBuild(preorder, inorder, 
                              preorder_left + size_left_subtree + 1, preorder_right, 
                              inorder_root + 1, inorder_right);

        return root;
    }
};
```
