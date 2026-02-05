# 94. 二叉树的中序遍历 - 渐进式学习指南

> 💡 **学习目标**：掌握二叉树的三种遍历方式，理解递归和迭代实现

---

## 🎯 问题理解

### 核心问题
按照"左-根-右"的顺序遍历二叉树。

### 示例
```
       1
      / \
     2   3
    / \
   4   5

中序遍历：4 → 2 → 5 → 1 → 3
顺序：左子树(4,2,5) → 根(1) → 右子树(3)
```

### 关键概念
**中序遍历（In-order）**：
1. 先遍历**左子树**
2. 再访问**根节点**
3. 最后遍历**右子树**

---

## 💭 思考引导

### 问题1：如何手动遍历？
给定一棵树，你会按什么顺序访问节点？

<details>
<summary>💡 提示</summary>

**口诀**：**左-根-右**

```
       1
      / \
     2   3

步骤：
1. 先看左子树 → 找到最左边的节点2
2. 访问2
3. 看2的右子树（无）
4. 回到1，访问1
5. 看右子树 → 访问3

结果：2 → 1 → 3
```

</details>

### 问题2：递归怎么写？
如何用递归实现这个过程？

<details>
<summary>💡 提示</summary>

**递归三步骤**：
```cpp
void inorder(TreeNode* node) {
    if (!node) return;
    
    inorder(node->left);      // 1. 左子树
    cout << node->val << endl;// 2. 根节点
    inorder(node->right);     // 3. 右子树
}
```

**关键**：访问根节点的位置决定了遍历类型！
- 前序：在开头访问根
- 中序：在中间访问根
- 后序：在结尾访问根

</details>

### 问题3：不用递归怎么做？
如何用循环实现？

<details>
<summary>💡 提示</summary>

**关键**：用**栈**模拟递归

**思路**：
1. 一直往左走，把路径入栈
2. 到达最左节点，出栈访问
3. 转向右子树，重复

**为什么用栈？**
- 需要记住"回溯路径"
- 先进后出，符合递归逻辑

</details>

---

## 📚 渐进式提示

### Level 1：理解遍历顺序 ⭐

<details>
<summary>点击查看</summary>

#### 三种遍历方式

```
       1
      / \
     2   3
    / \
   4   5

前序（根-左-右）：1 2 4 5 3
中序（左-根-右）：4 2 5 1 3  ← 本题
后序（左-右-根）：4 5 2 3 1
```

#### 记忆技巧

**看根节点的位置**：
- **前**序：根在**前面**
- **中**序：根在**中间**
- **后**序：根在**后面**

#### 手动模拟

```
       1
      / \
     2   3
    /
   4

中序遍历过程：
1. 从1开始，有左子树 → 去2
2. 从2开始，有左子树 → 去4
3. 从4开始，无左子树 → 访问4 ✓
4. 4无右子树，回到2
5. 访问2 ✓
6. 2无右子树，回到1
7. 访问1 ✓
8. 1有右子树 → 去3
9. 3无左子树 → 访问3 ✓

结果：4 2 1 3
```

#### 重要性质

**对于二叉搜索树（BST）**：
- 中序遍历结果是**有序的**！
- 这是验证BST的常用方法

</details>

---

### Level 2：递归实现 ⭐⭐

<details>
<summary>点击查看</summary>

#### 递归思路

**核心**：把问题分解为子问题
- 遍历整棵树 = 遍历左子树 + 访问根 + 遍历右子树

#### 伪代码

```
function inorder(node):
    if node is null:
        return
    
    inorder(node.left)      # 递归处理左子树
    visit(node)             # 访问当前节点
    inorder(node.right)     # 递归处理右子树
```

#### 完整代码

```cpp
vector<int> inorderTraversal(TreeNode* root) {
    vector<int> result;
    inorder(root, result);
    return result;
}

void inorder(TreeNode* node, vector<int>& result) {
    if (!node) return;
    
    inorder(node->left, result);  // 左
    result.push_back(node->val);  // 根
    inorder(node->right, result); // 右
}
```

#### 递归树

```
       inorder(1)
          |
    +-----+-----+
    |     |     |
  左子树  访问1  右子树
    |           |
inorder(2)   inorder(3)
    |
+---+---+
|   |   |
左  访问2 右
|
inorder(4)
```

#### 执行轨迹

```
inorder(1)
  inorder(2)
    inorder(4)
      inorder(null) → 返回
      访问 4 ✓
      inorder(null) → 返回
    访问 2 ✓
    inorder(null) → 返回
  访问 1 ✓
  inorder(3)
    inorder(null) → 返回
    访问 3 ✓
    inorder(null) → 返回

结果：[4, 2, 1, 3]
```

</details>

---

### Level 3：迭代实现 ⭐⭐⭐

<details>
<summary>点击查看</summary>

#### 为什么需要迭代？

**递归的缺点**：
- 消耗栈空间（可能栈溢出）
- 不够直观

**迭代的优点**：
- 显式控制栈
- 更高效

#### 核心思想

**用栈模拟递归**：
1. **往左走**：一直往左，入栈
2. **到底**：无左子树时，出栈访问
3. **转右**：转向右子树，重复

#### 图解过程

```
       1
      / \
     2   3
    /
   4

初始：current=1, stack=[]

步骤1：往左走
current=1 → stack=[1], current=2
current=2 → stack=[1,2], current=4
current=4 → stack=[1,2,4], current=null

步骤2：到底，出栈
current=stack.pop() → 4
访问 4 ✓
current=4.right → null

步骤3：继续出栈
current=stack.pop() → 2
访问 2 ✓
current=2.right → null

步骤4：继续出栈
current=stack.pop() → 1
访问 1 ✓
current=1.right → 3

步骤5：处理3
current=3 → stack=[3], current=null
current=stack.pop() → 3
访问 3 ✓

结果：4 2 1 3
```

#### 完整代码

```cpp
vector<int> inorderTraversal_iterative(TreeNode* root) {
    vector<int> result;
    stack<TreeNode*> st;
    TreeNode* current = root;
    
    while (current != nullptr || !st.empty()) {
        // 1. 一直往左走
        while (current != nullptr) {
            st.push(current);
            current = current->left;
        }
        
        // 2. 到底，出栈访问
        current = st.top();
        st.pop();
        result.push_back(current->val);
        
        // 3. 转向右子树
        current = current->right;
    }
    
    return result;
}
```

#### 关键点

**循环条件**：`while current or stack`
- `current`：当前有节点要处理
- `stack`：栈中还有待访问的节点

**内层循环**：`while current`
- 不断往左走，直到最左边

</details>

---

### Level 4：Morris遍历 ⭐⭐⭐⭐

<details>
<summary>点击查看</summary>

#### 为什么需要Morris？

**目标**：O(1)空间复杂度

**思路**：
- 不用栈，利用树本身的空闲指针
- 临时修改树结构，遍历后恢复

#### 核心概念

**前驱节点**：
- 在中序遍历中，当前节点的前一个节点
- 即：左子树的最右节点

```
       1
      /
     2
      \
       3

1的前驱是3（左子树2的最右节点）
```

#### 算法流程

```
1. 如果无左子树：
   - 直接访问当前节点
   - 转向右子树

2. 如果有左子树：
   - 找到前驱节点
   - 如果前驱的right为空：
     * 建立 前驱.right → 当前 的链接
     * 转向左子树
   - 如果前驱的right指向当前：
     * 说明左子树已遍历完
     * 恢复树结构（删除链接）
     * 访问当前节点
     * 转向右子树
```

#### 代码实现

```cpp
vector<int> inorderTraversal_morris(TreeNode* root) {
    vector<int> result;
    TreeNode* current = root;
    
    while (current != nullptr) {
        if (current->left == nullptr) {
            // 无左子树，直接访问
            result.push_back(current->val);
            current = current->right;
        } else {
            // 找前驱节点
            TreeNode* predecessor = current->left;
            while (predecessor->right != nullptr && predecessor->right != current) {
                predecessor = predecessor->right;
            }
            
            if (predecessor->right == nullptr) {
                // 建立临时链接
                predecessor->right = current;
                current = current->left;
            } else {
                // 恢复树结构
                predecessor->right = nullptr;
                result.push_back(current->val);
                current = current->right;
            }
        }
    }
    
    return result;
}
```

#### 优缺点

**优点**：
- ✅ O(1)空间复杂度

**缺点**：
- ❌ 代码复杂
- ❌ 临时修改树结构（虽然会恢复）

</details>

---

## 🔨 动手实践

### 任务1：手动遍历
画出以下树的中序遍历过程：
```
       5
      / \
     3   7
    / \
   2   4
```

### 任务2：实现递归版本
```cpp
vector<int> inorderTraversal(TreeNode* root) {
    // TODO: 实现递归中序遍历
    return {};
}
```

### 任务3：实现迭代版本
```cpp
vector<int> inorderTraversal_iterative(TreeNode* root) {
    // TODO: 实现迭代中序遍历
    return {};
}
```

### 任务4：对比三种遍历
实现前序、中序、后序遍历，观察差异：
```cpp
void preorder(TreeNode* root) {   // 根-左-右
    // ...
}

void inorder(TreeNode* root) {    // 左-根-右
    // ...
}

void postorder(TreeNode* root) {  // 左-右-根
    // ...
}
```

---

## ✅ 自我检查清单

- [ ] 理解三种遍历顺序的区别
- [ ] 能手动模拟中序遍历
- [ ] 理解递归的执行过程
- [ ] 能画出递归树
- [ ] 理解为什么用栈实现迭代
- [ ] 能独立实现递归和迭代版本
- [ ] 知道Morris遍历的原理
- [ ] 理解BST中序遍历的性质

---

## 🎓 知识拓展

### 1. 遍历应用场景

| 遍历方式 | 应用场景 |
|---------|----------|
| 前序 | 复制树、序列化 |
| 中序 | BST排序、验证BST |
| 后序 | 删除树、计算大小 |

### 2. BST性质

**二叉搜索树的中序遍历是有序的**

```cpp
bool isValidBST(TreeNode* root) {
    /* 验证是否为BST */
    vector<int> result = inorderTraversal(root);
    for (int i = 1; i < result.size(); ++i) {
        if (result[i] <= result[i-1]) {
            return false;
        }
    }
    return true;
}
```

---

## 🚀 进阶挑战

### 挑战1：验证BST
利用中序遍历验证二叉搜索树

### 挑战2：BST第k小元素
找出BST中第k小的元素

### 挑战3：恢复BST
两个节点被错误交换，恢复BST

---

## 💡 常见错误

### 错误1：递归顺序错误
```cpp
// ❌ 错误（这是前序）
void inorder(TreeNode* node) {
    result.push_back(node->val);  // 先访问根
    inorder(node->left);
    inorder(node->right);
}

// ✅ 正确（中序）
void inorder(TreeNode* node) {
    inorder(node->left);       // 先左
    result.push_back(node->val);  // 再根
    inorder(node->right);      // 后右
}
```

### 错误2：迭代循环条件
```cpp
// ❌ 错误
while (!st.empty()) {  // 只检查栈
    // ...
}

// ✅ 正确
while (current != nullptr || !st.empty()) {  // 还要检查current
    // ...
}
```

### 错误3：忘记判空
```cpp
// ❌ 错误
void inorder(TreeNode* node) {
    inorder(node->left);  // node可能为null
    // ...
}

// ✅ 正确
void inorder(TreeNode* node) {
    if (!node) {
        return;
    }
    inorder(node->left);
    // ...
}
```

---

## 📖 学习建议

1. **先掌握递归**：最直观
2. **理解遍历顺序**：左-根-右
3. **手动模拟过程**：加深理解
4. **练习迭代版本**：理解栈的作用
5. **对比三种遍历**：找出差异

完成后查看 `solution.md` 获取完整解答！🎉
