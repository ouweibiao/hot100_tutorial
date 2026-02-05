# 98. 验证二叉搜索树 - C++ 题解

## 方法一：中序遍历 (推荐)

```cpp
#include <climits>
#include <stack>
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
    long long pre = LONG_MIN; // 记录前一个节点的值，初始化为最小值

    bool isValidBST(TreeNode* root) {
        if (!root) return true;

        // 1. 访问左子树
        if (!isValidBST(root->left)) return false;

        // 2. 访问当前节点
        if (root->val <= pre) return false; // 必须严格大于前一个值
        pre = root->val;

        // 3. 访问右子树
        return isValidBST(root->right);
    }
};
```

## 方法二：递归 (区间判定)

```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        return check(root, LONG_MIN, LONG_MAX);
    }

    bool check(TreeNode* node, long long minVal, long long maxVal) {
        if (!node) return true;

        if (node->val <= minVal || node->val >= maxVal) return false;

        return check(node->left, minVal, node->val) && 
               check(node->right, node->val, maxVal);
    }
};
```
