# 104. 二叉树的最大深度 - 编程指导

## 🎯 学习目标
- 理解二叉树的递归定义
- 掌握 DFS 和 BFS 两种遍历方式
- 学会分析树的时间和空间复杂度

---

## 📖 题目回顾

求二叉树的最大深度（根到最远叶子节点的路径长度）。

**示例**：
```
    3
   / \
  9  20
    /  \
   15   7

答案：3
```

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：理解深度
<details>
<summary>点击查看提示1</summary>

**什么是深度？**

深度 = 从根到某个节点的路径上的节点数

**示例**：
```
    3      ← 深度 1
   / \
  9  20    ← 深度 2
    /  \
   15   7  ← 深度 3
```

**问题**：如何计算树的最大深度？

**思路**：
- 遍历所有节点
- 记录每个节点的深度
- 取最大值

</details>

---

### 🌟 提示 Level 2：递归思维
<details>
<summary>点击查看提示2</summary>

**递归定义**：
```
一棵树的最大深度 = 1 + max(左子树深度, 右子树深度)
```

**为什么？**
- 当前节点贡献深度 1
- 往下走，选择更深的子树

**边界条件**：
```
空节点的深度 = 0
```

**示例**：
```
    3
   / \
  9  20
    /  \
   15   7

maxDepth(3) = 1 + max(maxDepth(9), maxDepth(20))
            = 1 + max(1, 2)
            = 3

maxDepth(9) = 1 + max(0, 0) = 1

maxDepth(20) = 1 + max(maxDepth(15), maxDepth(7))
             = 1 + max(1, 1)
             = 2
```

</details>

---

### 🌟 提示 Level 3：两种实现
<details>
<summary>点击查看提示3</summary>

**方法1：递归（DFS）**
```cpp
int maxDepth(TreeNode* root) {
    if (root == nullptr) return 0;
    
    int leftDepth = maxDepth(root->left);
    int rightDepth = maxDepth(root->right);
    
    return 1 + max(leftDepth, rightDepth);
}
```

**方法2：迭代（BFS）**
```cpp
int maxDepth(TreeNode* root) {
    if (root == nullptr) return 0;
    
    queue<TreeNode*> q;
    q.push(root);
    int depth = 0;
    
    while (!q.empty()) {
        int size = q.size();
        depth++;
        
        for (int i = 0; i < size; i++) {
            TreeNode* node = q.front();
            q.pop();
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    
    return depth;
}
```

</details>

---

### 🌟 提示 Level 4：复杂度分析
<details>
<summary>点击查看提示4</summary>

**递归（DFS）**：
- **时间**：O(n)，每个节点访问一次
- **空间**：O(h)，递归栈
  - 平衡树：h = log n
  - 链式树：h = n

**迭代（BFS）**：
- **时间**：O(n)
- **空间**：O(w)，队列存储每层节点
  - 完全二叉树：w ≈ n/2

**选择**：
- 代码简洁：递归
- 避免栈溢出：迭代

</details>

---

## 🔨 动手实践

### 任务1：手动模拟递归
对以下树，手动写出递归过程：
```
    1
   / \
  2   3
 /
4
```

### 任务2：实现递归版本
```cpp
int maxDepth(TreeNode* root) {
    // TODO: 实现递归
}
```

### 任务3：实现迭代版本
```cpp
int maxDepth(TreeNode* root) {
    // TODO: 使用队列 BFS
}
```

### 任务4：测试
```cpp
// 用例1：普通树
    3
   / \
  9  20
    /  \
   15   7
// 预期: 3

// 用例2：单链
1 → 2 → 3 → 4
// 预期: 4

// 用例3：空树
null
// 预期: 0

// 用例4：单节点
1
// 预期: 1
```

---

## 🎯 核心技巧

### 1. 递归模板

```cpp
返回类型 递归函数(TreeNode* root) {
    // 1. 边界条件
    if (root == nullptr) return ...;
    
    // 2. 递归处理子树
    左子树结果 = 递归函数(root->left);
    右子树结果 = 递归函数(root->right);
    
    // 3. 合并结果
    return 合并(左子树结果, 右子树结果);
}
```

### 2. BFS 模板

```cpp
queue<TreeNode*> q;
q.push(root);

while (!q.empty()) {
    int size = q.size();  // 当前层节点数
    
    for (int i = 0; i < size; i++) {
        TreeNode* node = q.front();
        q.pop();
        
        // 处理当前节点
        
        // 加入下一层节点
        if (node->left) q.push(node->left);
        if (node->right) q.push(node->right);
    }
}
```

---

## 🚀 进阶挑战

### 挑战1：最小深度（LeetCode 111）
找到根到叶子节点的最短路径。

**提示**：叶子节点是两个子节点都为空的节点。

### 挑战2：判断平衡二叉树（LeetCode 110）
左右子树高度差不超过1。

**提示**：
```cpp
// 返回 -1 表示不平衡
int checkHeight(TreeNode* root) {
    if (root == nullptr) return 0;
    
    int left = checkHeight(root->left);
    if (left == -1) return -1;
    
    int right = checkHeight(root->right);
    if (right == -1) return -1;
    
    if (abs(left - right) > 1) return -1;
    
    return 1 + max(left, right);
}
```

### 挑战3：返回所有最深叶子节点
找出深度最大的所有叶子节点。

---

## ✅ 自检清单

- [ ] 我理解了二叉树的深度定义
- [ ] 我能写出递归定义
- [ ] 我理解了边界条件
- [ ] 我能独立实现递归版本
- [ ] 我能独立实现迭代版本
- [ ] 我理解了两种方法的复杂度差异

---

## 📌 核心要点

**递归三要素**：
```
1. 边界：root == null 返回 0
2. 递归：计算左右子树深度
3. 合并：1 + max(left, right)
```

**BFS 层序遍历**：
```
1. 用队列存储每层节点
2. 遍历当前层所有节点
3. 将下一层节点加入队列
4. 层数 +1
```

---

## 🧠 思维拓展

### 为什么递归能解决？

二叉树本身就是递归定义的：
```
树 = 根节点 + 左子树 + 右子树
```

所以很多二叉树问题都可以用递归解决：
- 遍历：前序、中序、后序
- 计算：深度、节点数、路径和
- 判断：对称、平衡、相同

---

**下一步**：独立实现并对照 `solution.md`！
