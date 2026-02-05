# 226. 翻转二叉树 - 编程指导

## 🎯 学习目标
- 理解树的递归操作
- 掌握交换子树的技巧
- 学会递归和迭代两种实现

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：理解翻转
<details>
<summary>点击查看提示1</summary>

**什么是翻转？**

```
原树：          翻转后：
    4              4
   / \            / \
  2   7    →     7   2
 / \            / \
1   3          3   1
```

**观察**：
- 每个节点的左右子树互换
- 递归进行

</details>

---

### 🌟 提示 Level 2：递归思路
<details>
<summary>点击查看提示2</summary>

**递归定义**：
```
翻转一棵树 = 
  1. 交换左右子树
  2. 递归翻转左子树
  3. 递归翻转右子树
```

**伪代码**：
```
invertTree(root):
  if root == null: return null
  
  交换 root.left 和 root.right
  invertTree(root.left)
  invertTree(root.right)
  
  return root
```

</details>

---

### 🌟 提示 Level 3：实现方式
<details>
<summary>点击查看提示3</summary>

**方式1：先交换，再递归**
```cpp
TreeNode* invertTree(TreeNode* root) {
    if (root == nullptr) return nullptr;
    
    swap(root->left, root->right);
    invertTree(root->left);
    invertTree(root->right);
    
    return root;
}
```

**方式2：先递归，再交换**
```cpp
TreeNode* invertTree(TreeNode* root) {
    if (root == nullptr) return nullptr;
    
    TreeNode* left = invertTree(root->left);
    TreeNode* right = invertTree(root->right);
    root->left = right;
    root->right = left;
    
    return root;
}
```

</details>

---

### 🌟 提示 Level 4：迭代实现
<details>
<summary>点击查看提示4</summary>

**使用队列（BFS）**：
```cpp
TreeNode* invertTree(TreeNode* root) {
    if (root == nullptr) return nullptr;
    
    queue<TreeNode*> q;
    q.push(root);
    
    while (!q.empty()) {
        TreeNode* node = q.front();
        q.pop();
        
        swap(node->left, node->right);
        
        if (node->left) q.push(node->left);
        if (node->right) q.push(node->right);
    }
    
    return root;
}
```

</details>

---

## 🔨 动手实践

### 任务1：手动模拟
对以下树，手动模拟递归过程：
```
    1
   / \
  2   3
 / \
4   5
```

### 任务2：实现递归版本
```cpp
TreeNode* invertTree(TreeNode* root) {
    // TODO: 递归实现
}
```

### 任务3：实现迭代版本
```cpp
TreeNode* invertTree(TreeNode* root) {
    // TODO: BFS迭代实现
}
```

---

## ✅ 自检清单

- [ ] 我理解了什么是翻转
- [ ] 我能写出递归定义
- [ ] 我理解了两种交换时机
- [ ] 我能实现递归和迭代版本

---

**下一步**：独立实现并对照 `solution.md`！
