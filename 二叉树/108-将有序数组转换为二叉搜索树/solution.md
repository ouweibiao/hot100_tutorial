# 108. 将有序数组转换为二叉搜索树 - C++ 题解

## 方法：递归 (DFS)

```cpp
#include <vector>
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
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return build(nums, 0, nums.size() - 1);
    }
    
    TreeNode* build(vector<int>& nums, int left, int right) {
        if (left > right) return nullptr;
        
        // 总是选择中间位置左边的数字作为根节点（向下取整）
        int mid = left + (right - left) / 2;
        
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = build(nums, left, mid - 1);
        root->right = build(nums, mid + 1, right);
        
        return root;
    }
};
```
