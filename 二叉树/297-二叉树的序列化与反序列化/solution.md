# 297. 二叉树的序列化与反序列化 - C++ 题解

## 方法：DFS (前序遍历)

```cpp
#include <string>
#include <list>
#include <sstream>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        if (!root) {
            return "None,";
        }
        return to_string(root->val) + "," + serialize(root->left) + serialize(root->right);
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        list<string> dataList;
        string str;
        for (char c : data) {
            if (c == ',') {
                dataList.push_back(str);
                str.clear();
            } else {
                str.push_back(c);
            }
        }
        return rdeserialize(dataList);
    }

    TreeNode* rdeserialize(list<string>& dataList) {
        if (dataList.front() == "None") {
            dataList.pop_front();
            return nullptr;
        }

        TreeNode* root = new TreeNode(stoi(dataList.front()));
        dataList.pop_front();
        root->left = rdeserialize(dataList);
        root->right = rdeserialize(dataList);

        return root;
    }
};
```
