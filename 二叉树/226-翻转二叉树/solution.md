# 226. 翻转二叉树

## 📝 题目描述

给你一棵二叉树的根节点 `root`，翻转这棵二叉树，并返回其根节点。

**示例 1：**
```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]

    4              4
   / \            / \
  2   7    →     7   2
 / \ / \        / \ / \
1  3 6  9      9  6 3  1
```

**示例 2：**
```
输入：root = [2,1,3]
输出：[2,3,1]
```

**示例 3：**
```
输入：root = []
输出：[]
```

**约束条件：**
- 树中节点数目范围在 `[0, 100]` 内
- `-100 <= Node.val <= 100`

---

## ⭐ 解法一：递归（DFS）

### 思路

**递归定义**：
- 翻转一棵树 = 交换左右子树 + 递归翻转左右子树

### 代码实现
```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == nullptr) {
            return nullptr;
        }
        
        // 交换左右子树
        TreeNode* temp = root->left;
        root->left = root->right;
        root->right = temp;
        
        // 递归翻转左右子树
        invertTree(root->left);
        invertTree(root->right);
        
        return root;
    }
};
```

### 简化写法
```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == nullptr) return nullptr;
        
        // 递归翻转左右子树后交换
        TreeNode* left = invertTree(root->left);
        TreeNode* right = invertTree(root->right);
        root->left = right;
        root->right = left;
        
        return root;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，每个节点访问一次
- **空间复杂度**：O(h)，递归栈，h 是树高

---

## 💡 解法二：迭代（BFS）

### 思路

使用队列层序遍历，对每个节点交换其左右子树。

### 代码实现
```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == nullptr) return nullptr;
        
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            TreeNode* node = q.front();
            q.pop();
            
            // 交换左右子树
            TreeNode* temp = node->left;
            node->left = node->right;
            node->right = temp;
            
            // 将子节点加入队列
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        return root;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
- **空间复杂度**：O(w)，w 是树的最大宽度

---

## 🎯 核心知识点

### 1. 树的翻转操作

**本质**：镜像对称
```
原树：
    4
   / \
  2   7

翻转后：
    4
   / \
  7   2
```

**操作**：
1. 交换当前节点的左右子树
2. 递归翻转左子树
3. 递归翻转右子树

### 2. 递归 vs 迭代

| 方法 | 实现 | 空间 | 优点 |
|------|------|------|------|
| 递归 | DFS | O(h) | 代码简洁 |
| 迭代 | BFS | O(w) | 避免栈溢出 |

### 3. 交换的时机

**方式1**：先交换，再递归
```cpp
swap(root->left, root->right);
invertTree(root->left);
invertTree(root->right);
```

**方式2**：先递归，再交换
```cpp
TreeNode* left = invertTree(root->left);
TreeNode* right = invertTree(root->right);
root->left = right;
root->right = left;
```

两种方式都正确，效果相同。

---

## 🔍 图解过程（递归）

```
原树：
    4
   / \
  2   7
 / \
1   3

Step 1: 翻转根节点
    4
   / \
  7   2
     / \
    1   3

Step 2: 递归翻转左子树(7)
    4
   / \
  7   2
     / \
    3   1

Step 3: 递归翻转右子树(2)
    4
   / \
  7   2
     / \
    3   1
    
最终结果：
    4
   / \
  7   2
     / \
    3   1
```

---

## 🎓 变式题目

### 1. 对称二叉树（LeetCode 101）
判断二叉树是否是镜像对称的。

**思路**：比较左右子树是否互为镜像。

### 2. 二叉树的镜像（剑指 Offer 27）
同本题。

---

## 📌 记忆要点

- **操作**：交换左右子树
- **递归**：先交换再递归，或先递归再交换
- **迭代**：BFS 遍历，逐个交换
- **复杂度**：O(n) 时间，O(h) 或 O(w) 空间

---

## 🔨 常见错误

### 错误1：忘记递归子树
```cpp
// ❌ 错误：只交换了根节点
swap(root->left, root->right);
return root;

// ✅ 正确：还要递归子树
swap(root->left, root->right);
invertTree(root->left);
invertTree(root->right);
```

### 错误2：交换后指针错误
```cpp
// ❌ 错误：交换后 root->left 已经是原来的右子树
swap(root->left, root->right);
invertTree(root->left);  // 实际翻转的是原右子树

// ✅ 正确：方式1 - 先交换没问题
swap(root->left, root->right);
invertTree(root->left);
invertTree(root->right);

// ✅ 正确：方式2 - 保存后再交换
TreeNode* left = invertTree(root->left);
TreeNode* right = invertTree(root->right);
root->left = right;
root->right = left;
```

---

**标签**：`二叉树` `深度优先搜索` `广度优先搜索` `easy`
