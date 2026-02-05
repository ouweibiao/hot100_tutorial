# 199. 二叉树的右视图 - C++ 题解

## 方法一：BFS (层序遍历)

```cpp
#include <vector>
#include <queue>
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
    vector<int> rightSideView(TreeNode* root) {
        vector<int> ans;
        if (!root) return ans;

        queue<TreeNode*> q;
        q.push(root);

        while (!q.empty()) {
            int n = q.size();
            for (int i = 0; i < n; ++i) {
                TreeNode* node = q.front();
                q.pop();

                // 如果是当前层的最后一个节点，加入结果
                if (i == n - 1) {
                    ans.push_back(node->val);
                }

                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
        }

        return ans;
    }
};
```

## 方法二：DFS (根右左)

```cpp
class Solution {
public:
    vector<int> ans;

    vector<int> rightSideView(TreeNode* root) {
        dfs(root, 0);
        return ans;
    }

    void dfs(TreeNode* root, int depth) {
        if (!root) return;

        // 如果当前深度等于结果集大小，说明这一层还没被记录过
        // 因为我们先访问右子树，所以这一定是该层最右边的节点
        if (depth == ans.size()) {
            ans.push_back(root->val);
        }

        dfs(root->right, depth + 1);
        dfs(root->left, depth + 1);
    }
};
```
