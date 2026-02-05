# 94. 二叉树的中序遍历

## 题目描述

给定一个二叉树的根节点 `root`，返回它的**中序**遍历。

### 示例

**示例 1：**
```
输入：root = [1,null,2,3]
    1
     \
      2
     /
    3
输出：[1,3,2]
```

**示例 2：**
```
输入：root = []
输出：[]
```

**示例 3：**
```
输入：root = [1]
输出：[1]
```

**提示：**
- 树中节点数目在范围 `[0, 100]` 内
- `-100 <= Node.val <= 100`

---

## 二叉树遍历基础

### 三种遍历方式

```
       1
      / \
     2   3
    / \
   4   5

前序遍历（根-左-右）：1 2 4 5 3
中序遍历（左-根-右）：4 2 5 1 3  ← 本题
后序遍历（左-右-根）：4 5 2 3 1
```

**中序遍历特点**：
- 对于**二叉搜索树（BST）**，中序遍历结果是**有序的**
- 遍历顺序：先左子树 → 根节点 → 右子树

---

## 解法一：递归

### 核心思路

递归是二叉树遍历的最自然实现方式。

**递归三步骤**：
1. 递归遍历左子树
2. 访问根节点
3. 递归遍历右子树

### 代码实现

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        inorder(root, result);
        return result;
    }
    
    void inorder(TreeNode* node, vector<int>& result) {
        if (!node) {
            return;
        }
        
        inorder(node->left, result);      // 1. 左子树
        result.push_back(node->val);      // 2. 根节点
        inorder(node->right, result);     // 3. 右子树
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
  - 每个节点访问一次
- **空间复杂度**：O(n)
  - 递归栈深度（最坏情况）

---

## 解法二：迭代（栈）⭐

### 核心思路

用**栈**模拟递归过程。

**关键观察**：
- 中序遍历：先访问最左边的节点
- 需要记住"回溯路径"→ 用栈

**算法流程**：
1. 一直往左走，把路径上的节点入栈
2. 到达最左节点，出栈并访问
3. 转向右子树，重复步骤1

### 图解过程

```
       1
      / \
     2   3
    /
   4

步骤1：往左走
栈：[1, 2, 4]
当前：null

步骤2：出栈访问4
栈：[1, 2]
访问：4
转向4的右子树（null）

步骤3：出栈访问2
栈：[1]
访问：4 2
转向2的右子树（null）

步骤4：出栈访问1
栈：[]
访问：4 2 1
转向1的右子树（3）

步骤5：处理3
栈：[3]
当前：null

步骤6：出栈访问3
栈：[]
访问：4 2 1 3
```

### 代码实现

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        TreeNode* current = root;
        
        while (current != nullptr || !st.empty()) {
            // 1. 一直往左走，入栈
            while (current != nullptr) {
                st.push(current);
                current = current->left;
            }
            
            // 2. 到达最左，出栈访问
            current = st.top();
            st.pop();
            result.push_back(current->val);
            
            // 3. 转向右子树
            current = current->right;
        }
        
        return result;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
- **空间复杂度**：O(n)（栈空间）

---

## 解法三：Morris遍历（O(1)空间）

### 核心思路

利用**线索二叉树**的思想，在遍历过程中临时修改树结构，实现O(1)空间复杂度。

**关键技巧**：
- 找到当前节点的**前驱节点**（左子树的最右节点）
- 建立临时链接，用于回溯
- 遍历完后恢复树结构

### 图解

```
       1
      / \
     2   3
    / \
   4   5

步骤1：找2的前驱（5），建立 5 → 1 的临时链接
       1 ←┐
      / \ │
     2   3│
    / \   │
   4   5──┘

步骤2：遍历完左子树后，通过临时链接回到1
```

### 代码实现

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        TreeNode* current = root;
        
        while (current != nullptr) {
            if (current->left == nullptr) {
                // 没有左子树，直接访问
                result.push_back(current->val);
                current = current->right;
            } else {
                // 找到前驱节点（左子树的最右节点）
                TreeNode* predecessor = current->left;
                while (predecessor->right != nullptr && predecessor->right != current) {
                    predecessor = predecessor->right;
                }
                
                if (predecessor->right == nullptr) {
                    // 建立临时链接
                    predecessor->right = current;
                    current = current->left;
                } else {
                    // 已经遍历完左子树，恢复结构
                    predecessor->right = nullptr;
                    result.push_back(current->val);
                    current = current->right;
                }
            }
        }
        
        return result;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
- **空间复杂度**：O(1) ⭐

---

## 三种方法对比

| 方法 | 时间复杂度 | 空间复杂度 | 优点 | 缺点 |
|------|-----------|-----------|------|------|
| 递归 | O(n) | O(n) | 代码简洁 | 栈空间消耗 |
| 迭代 | O(n) | O(n) | 易理解 | 需要栈 |
| Morris | O(n) | O(1) | 空间最优 | 代码复杂 |

**推荐**：
- **面试首选**：迭代法（平衡简洁性和效率）
- **追求极致**：Morris遍历（O(1)空间）

---

## 相关问题

### 1. 前序遍历（根-左-右）

```cpp
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> result;
    if (!root) return result;
    
    stack<TreeNode*> st;
    st.push(root);
    
    while (!st.empty()) {
        TreeNode* node = st.top();
        st.pop();
        result.push_back(node->val);  // 先访问根
        
        // 注意：先压右子树，再压左子树
        if (node->right) st.push(node->right);
        if (node->left) st.push(node->left);
    }
    
    return result;
}
```

### 2. 后序遍历（左-右-根）

```cpp
vector<int> postorderTraversal(TreeNode* root) {
    if (!root) return {};
    
    stack<TreeNode*> st;
    st.push(root);
    vector<int> result;
    
    while (!st.empty()) {
        TreeNode* node = st.top();
        st.pop();
        result.push_back(node->val);
        
        if (node->left) st.push(node->left);
        if (node->right) st.push(node->right);
    }
    
    // 反转结果（根-右-左 → 左-右-根）
    reverse(result.begin(), result.end());
    return result;
}
```

---

## 实战应用

### 场景1：验证二叉搜索树

```cpp
// 利用中序遍历验证BST
bool isValidBST(TreeNode* root) {
    // 假设 inorderTraversal 已经实现
    // vector<int> inorder = inorderTraversal(root);
    // BST的中序遍历应该是严格递增的
    // for (size_t i = 1; i < inorder.size(); ++i) {
    //     if (inorder[i] <= inorder[i-1]) return false;
    // }
    // return true;
    
    // 实际优化：可以在遍历过程中直接检查，不需要存储所有元素
    stack<TreeNode*> st;
    TreeNode* current = root;
    long long prevVal = LONG_MIN;
    
    while (current != nullptr || !st.empty()) {
        while (current != nullptr) {
            st.push(current);
            current = current->left;
        }
        current = st.top();
        st.pop();
        
        if (current->val <= prevVal) return false;
        prevVal = current->val;
        
        current = current->right;
    }
    return true;
}
```

### 场景2：找第k小的元素

```cpp
// 找BST中第k小的元素
int kthSmallest(TreeNode* root, int k) {
    // vector<int> result = inorderTraversal(root);
    // return result[k-1];
    
    // 迭代优化
    stack<TreeNode*> st;
    TreeNode* current = root;
    
    while (current != nullptr || !st.empty()) {
        while (current != nullptr) {
            st.push(current);
            current = current->left;
        }
        current = st.top();
        st.pop();
        
        if (--k == 0) return current->val;
        
        current = current->right;
    }
    return -1;
}
```

### 场景3：BST转有序数组

```cpp
// 将BST转换为有序数组
vector<int> bstToArray(TreeNode* root) {
    // 复用中序遍历
    // return inorderTraversal(root);
    return {}; // 占位
}
```

---

## 核心知识点

### 1. 遍历顺序口诀
- **前序**：根左右（Pre-order）
- **中序**：左根右（In-order）
- **后序**：左右根（Post-order）

### 2. 递归模板
```cpp
void traverse(TreeNode* node) {
    if (!node) return;
    
    // 前序位置
    traverse(node->left);
    // 中序位置
    traverse(node->right);
    // 后序位置
}
```

### 3. 迭代模板（中序）
```cpp
stack<TreeNode*> st;
TreeNode* current = root;

while (current != nullptr || !st.empty()) {
    while (current != nullptr) {
        st.push(current);
        current = current->left;
    }
    
    current = st.top();
    st.pop();
    // visit(current);
    current = current->right;
}
```

---

## 调试技巧

### 可视化遍历过程

```cpp
vector<int> inorderTraversal_debug(TreeNode* root) {
    /* 带详细输出的版本 */
    vector<int> result;
    stack<TreeNode*> st;
    TreeNode* current = root;
    int step = 1;
    
    while (current != nullptr || !st.empty()) {
        // 往左走
        while (current != nullptr) {
            cout << "Step " << step << ": 入栈 " << current->val << endl;
            st.push(current);
            current = current->left;
            step++;
        }
        
        // 访问
        current = st.top();
        st.pop();
        result.push_back(current->val);
        cout << "Step " << step << ": 访问 " << current->val << ", 结果=[";
        for(int x : result) cout << x << ",";
        cout << "]" << endl;
        step++;
        
        // 转向右子树
        current = current->right;
    }
    
    return result;
}
```

---

## 总结

### 核心要点
1. ✅ **中序遍历 = 左-根-右**
2. ✅ **BST的中序遍历是有序的**
3. ✅ **递归最简洁，迭代更高效**
4. ✅ **Morris遍历可达到O(1)空间**

### 算法模板

**递归版本**：
```cpp
void inorder(TreeNode* node, vector<int>& result) {
    if (!node) return;
    inorder(node->left, result);
    result.push_back(node->val);
    inorder(node->right, result);
}
```

**迭代版本**：
```cpp
vector<int> inorder(TreeNode* root) {
    vector<int> result;
    stack<TreeNode*> st;
    TreeNode* current = root;
    while (current != nullptr || !st.empty()) {
        while (current != nullptr) {
            st.push(current);
            current = current->left;
        }
        current = st.top();
        st.pop();
        result.push_back(current->val);
        current = current->right;
    }
    return result;
}
```

这道题是二叉树遍历的基础，必须熟练掌握！
