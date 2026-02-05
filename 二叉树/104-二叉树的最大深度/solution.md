# 104. 二叉树的最大深度

## 📝 题目描述

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

**说明**：叶子节点是指没有子节点的节点。

**示例：**
```
给定二叉树 [3,9,20,null,null,15,7]

    3
   / \
  9  20
    /  \
   15   7

返回它的最大深度 3
```

**约束条件：**
- 树中节点的数量在范围 `[0, 10^4]` 内
- `-100 <= Node.val <= 100`

---

## ⭐ 解法一：递归（DFS）

### 思路

**递归定义**：一棵树的最大深度 = 1 + max(左子树深度, 右子树深度)

**边界条件**：空节点的深度为 0

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
    int maxDepth(TreeNode* root) {
        // 边界：空节点深度为 0
        if (root == nullptr) {
            return 0;
        }
        
        // 递归计算左右子树深度
        int leftDepth = maxDepth(root->left);
        int rightDepth = maxDepth(root->right);
        
        // 当前树的深度 = 1 + 左右子树深度的最大值
        return 1 + max(leftDepth, rightDepth);
    }
};
```

### 简化写法
```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        return root == nullptr ? 0 : 1 + max(maxDepth(root->left), maxDepth(root->right));
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，每个节点访问一次
- **空间复杂度**：O(h)，递归栈空间，h 是树的高度
  - 最好情况（平衡树）：O(log n)
  - 最坏情况（链式树）：O(n)

---

## 💡 解法二：迭代（BFS 层序遍历）

### 思路

使用队列进行层序遍历（BFS），统计层数即为最大深度。

### 代码实现
```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) return 0;
        
        queue<TreeNode*> q;
        q.push(root);
        int depth = 0;
        
        while (!q.empty()) {
            int levelSize = q.size();  // 当前层的节点数
            depth++;  // 层数加1
            
            // 遍历当前层的所有节点
            for (int i = 0; i < levelSize; i++) {
                TreeNode* node = q.front();
                q.pop();
                
                // 将下一层节点加入队列
                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
        }
        
        return depth;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
- **空间复杂度**：O(w)，w 是树的最大宽度
  - 最坏情况（完全二叉树）：O(n/2) = O(n)

---

## 🎯 核心知识点

### 1. 二叉树的遍历

**深度优先遍历（DFS）**：
- 前序遍历：根 → 左 → 右
- 中序遍历：左 → 根 → 右
- 后序遍历：左 → 右 → 根

**广度优先遍历（BFS）**：
- 层序遍历：一层一层遍历

### 2. 递归三要素

**① 递归定义**：
```
maxDepth(root) = 1 + max(maxDepth(left), maxDepth(right))
```

**② 边界条件**：
```
root == nullptr 时返回 0
```

**③ 递归调用**：
```
递归处理左右子树
```

### 3. BFS vs DFS

| 方法 | 实现 | 空间 | 适用场景 |
|------|------|------|----------|
| DFS | 递归/栈 | O(h) | 深度优先问题 |
| BFS | 队列 | O(w) | 层次问题 |

**本题**：
- DFS 更简洁，代码更短
- BFS 更直观，逐层统计

---

## 🔍 图解过程（递归）

```
树结构：
    3
   / \
  9  20
    /  \
   15   7

递归过程：
maxDepth(3)
  ├─ maxDepth(9)
  │   ├─ maxDepth(null) → 0
  │   └─ maxDepth(null) → 0
  │   return 1 + max(0, 0) = 1
  │
  └─ maxDepth(20)
      ├─ maxDepth(15)
      │   ├─ maxDepth(null) → 0
      │   └─ maxDepth(null) → 0
      │   return 1 + max(0, 0) = 1
      │
      └─ maxDepth(7)
          ├─ maxDepth(null) → 0
          └─ maxDepth(null) → 0
          return 1 + max(0, 0) = 1
      
      return 1 + max(1, 1) = 2
  
  return 1 + max(1, 2) = 3

最终答案：3
```

---

## 🎓 变式题目

### 1. 二叉树的最小深度（LeetCode 111）
找到最短路径（根到叶子）。

**注意**：叶子节点是两个子节点都为空的节点。

### 2. 平衡二叉树（LeetCode 110）
判断是否是平衡二叉树（左右子树高度差不超过1）。

### 3. N叉树的最大深度（LeetCode 559）
扩展到多叉树。

---

## 📌 记忆要点

- **递归定义**：`1 + max(左子树深度, 右子树深度)`
- **边界条件**：空节点返回 0
- **两种方法**：
  - DFS（递归）：代码简洁
  - BFS（迭代）：逻辑清晰
- **复杂度**：时间 O(n)，空间 O(h) 或 O(w)

---

## 🔨 常见错误

### 错误1：忘记加1
```cpp
// ❌ 错误：忘记当前层
return max(maxDepth(root->left), maxDepth(root->right));

// ✅ 正确：加上当前层
return 1 + max(maxDepth(root->left), maxDepth(root->right));
```

### 错误2：空节点处理
```cpp
// ❌ 错误：未处理空节点
int leftDepth = maxDepth(root->left);  // 如果 root 为 null，会崩溃

// ✅ 正确：先判断
if (root == nullptr) return 0;
```

---

**标签**：`二叉树` `深度优先搜索` `广度优先搜索` `easy`
