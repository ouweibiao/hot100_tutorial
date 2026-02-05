# 102. 二叉树的层序遍历

## 题目描述

给你二叉树的根节点 `root`，返回其节点值的**层序遍历**。（即逐层地，从左到右访问所有节点）。

### 示例

**示例 1：**
```
输入：root = [3,9,20,null,null,15,7]
      3
     / \
    9  20
      /  \
     15   7
输出：[[3],[9,20],[15,7]]
```

**示例 2：**
```
输入：root = [1]
输出：[[1]]
```

**示例 3：**
```
输入：root = []
输出：[]
```

**提示：**
- 树中节点数目在范围 `[0, 2000]` 内
- `-1000 <= Node.val <= 1000`

---

## C++基础知识

### 1. 二叉树定义
```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) 
        : val(x), left(left), right(right) {}
};
```

### 2. 队列（queue）
```cpp
#include <queue>
using namespace std;

queue<int> q;
q.push(1);          // 入队
int front = q.front();  // 获取队首
q.pop();            // 出队
bool empty = q.empty(); // 判空
int size = q.size();    // 大小
```

### 3. 二维vector
```cpp
vector<vector<int>> result;
vector<int> level = {1, 2, 3};
result.push_back(level);
```

---

## 解法一：BFS（队列）⭐

### 核心思路

**层序遍历 = 广度优先搜索（BFS）**

使用队列：
1. 将根节点入队
2. 每次处理队列中的所有节点（一层）
3. 将下一层节点入队
4. 重复直到队列空

### 图解过程

```
      3
     / \
    9  20
      /  \
     15   7

初始：queue = [3]

第1层：
  处理3，孩子9,20入队
  queue = [9, 20]
  level = [3]

第2层：
  处理9，无孩子
  处理20，孩子15,7入队
  queue = [15, 7]
  level = [9, 20]

第3层：
  处理15，无孩子
  处理7，无孩子
  queue = []
  level = [15, 7]

结果：[[3], [9,20], [15,7]]
```

### 代码实现

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result;
        
        if (!root) {
            return result;
        }
        
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            int levelSize = q.size();  // 当前层的节点数
            vector<int> currentLevel;
            
            // 处理当前层的所有节点
            for (int i = 0; i < levelSize; i++) {
                TreeNode* node = q.front();
                q.pop();
                
                currentLevel.push_back(node->val);
                
                // 将下一层节点入队
                if (node->left) {
                    q.push(node->left);
                }
                if (node->right) {
                    q.push(node->right);
                }
            }
            
            result.push_back(currentLevel);
        }
        
        return result;
    }
};
```

**C++知识点**：
1. **queue**：标准库队列
2. **q.size()**：当前队列大小（当前层节点数）
3. **vector::push_back()**：尾部插入
4. **局部变量**：`currentLevel` 每层创建新vector

### 关键点

**为什么要记录levelSize？**
```cpp
int levelSize = q.size();  // 必须！
```
- 队列在循环中会变化
- 提前记录当前层大小
- 保证只处理当前层

### 复杂度分析
- **时间复杂度**：O(n)
  - 每个节点访问一次
- **空间复杂度**：O(n)
  - 队列最多n个节点（最坏情况：完全二叉树最后一层）

---

## 解法二：DFS（递归）

### 核心思路

利用递归 + 深度参数：
- 递归时传递当前深度
- 根据深度将节点加入对应层

### 代码实现

```cpp
class Solution {
public:
    void dfs(TreeNode* node, int level, vector<vector<int>>& result) {
        if (!node) {
            return;
        }
        
        // 如果是新的一层，创建新vector
        if (level == result.size()) {
            result.push_back(vector<int>());
        }
        
        // 将当前节点加入对应层
        result[level].push_back(node->val);
        
        // 递归处理左右子树
        dfs(node->left, level + 1, result);
        dfs(node->right, level + 1, result);
    }
    
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result;
        dfs(root, 0, result);
        return result;
    }
};
```

**C++知识点**：
- **引用传递**：`vector<vector<int>>& result` 避免拷贝
- **递归参数**：`level` 跟踪当前深度
- **动态扩展**：`result.size()` 检查是否需要新层

### 执行过程

```
      3
     / \
    9  20
      /  \
     15   7

dfs(3, 0):
  result = [[3]]
  dfs(9, 1):
    result = [[3], [9]]
  dfs(20, 1):
    result = [[3], [9, 20]]
    dfs(15, 2):
      result = [[3], [9, 20], [15]]
    dfs(7, 2):
      result = [[3], [9, 20], [15, 7]]
```

### 复杂度分析
- **时间复杂度**：O(n)
- **空间复杂度**：O(h)
  - h是树的高度（递归栈）

---

## 方法对比

| 方法 | 实现方式 | 空间复杂度 | 优点 | 适用场景 |
|------|---------|-----------|------|---------|
| BFS | 队列 | O(n) | 直观 | 层序遍历 |
| DFS | 递归 | O(h) | 简洁 | 按层处理 |

**推荐**：BFS（更符合层序遍历的语义）

---

## 变式问题

### 1. 自底向上层序遍历

**问题**：从叶子到根的顺序

```cpp
vector<vector<int>> levelOrderBottom(TreeNode* root) {
    vector<vector<int>> result;
    // ... BFS代码相同 ...
    
    // 最后反转
    reverse(result.begin(), result.end());
    return result;
}
```

**C++知识点**：`reverse()` 反转容器

### 2. 锯齿形层序遍历

**问题**：奇数层从左到右，偶数层从右到左

```cpp
vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;
    
    queue<TreeNode*> q;
    q.push(root);
    bool leftToRight = true;
    
    while (!q.empty()) {
        int levelSize = q.size();
        vector<int> level(levelSize);  // 预分配空间
        
        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front();
            q.pop();
            
            // 根据方向决定位置
            int index = leftToRight ? i : (levelSize - 1 - i);
            level[index] = node->val;
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        result.push_back(level);
        leftToRight = !leftToRight;  // 切换方向
    }
    
    return result;
}
```

**C++知识点**：
- `vector<int> level(levelSize)`：预分配大小
- `!leftToRight`：布尔值取反

### 3. 每层最大值

```cpp
vector<int> largestValues(TreeNode* root) {
    vector<int> result;
    if (!root) return result;
    
    queue<TreeNode*> q;
    q.push(root);
    
    while (!q.empty()) {
        int levelSize = q.size();
        int maxVal = INT_MIN;  // 最小整数
        
        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front();
            q.pop();
            
            maxVal = max(maxVal, node->val);
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        result.push_back(maxVal);
    }
    
    return result;
}
```

**C++知识点**：
- `INT_MIN`：`<climits>` 中定义的最小整数
- `max()`：`<algorithm>` 的标准函数

### 4. 二叉树的右视图

```cpp
vector<int> rightSideView(TreeNode* root) {
    vector<int> result;
    if (!root) return result;
    
    queue<TreeNode*> q;
    q.push(root);
    
    while (!q.empty()) {
        int levelSize = q.size();
        
        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front();
            q.pop();
            
            // 记录每层最后一个节点
            if (i == levelSize - 1) {
                result.push_back(node->val);
            }
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    
    return result;
}
```

---

## C++特性详解

### 1. 队列（queue）

```cpp
#include <queue>

queue<int> q;

// 基本操作
q.push(1);         // 入队
q.front();         // 访问队首（不删除）
q.back();          // 访问队尾
q.pop();           // 出队（无返回值）
q.size();          // 大小
q.empty();         // 判空
```

**注意**：C++的queue没有迭代器，不能遍历

### 2. vector常用操作

```cpp
vector<int> v;

// 插入
v.push_back(1);       // 尾部插入
v.insert(v.begin(), 1);  // 指定位置

// 访问
v[0];                 // 下标访问
v.at(0);              // 安全访问（检查边界）
v.front();            // 第一个元素
v.back();             // 最后一个元素

// 大小
v.size();             // 元素个数
v.empty();            // 是否为空
v.clear();            // 清空

// 预分配
v.reserve(100);       // 预留容量
vector<int> v2(10);   // 10个默认值
vector<int> v3(10, 5); // 10个5
```

### 3. 二维vector

```cpp
// 创建
vector<vector<int>> matrix;

// 添加行
vector<int> row = {1, 2, 3};
matrix.push_back(row);

// 访问
int val = matrix[i][j];

// 遍历
for (const auto& row : matrix) {
    for (int val : row) {
        cout << val << " ";
    }
}
```

### 4. 智能指针（现代C++）

```cpp
#include <memory>

// 独占所有权
unique_ptr<TreeNode> root = make_unique<TreeNode>(1);

// 共享所有权
shared_ptr<TreeNode> root = make_shared<TreeNode>(1);
```

---

## 常见错误

### 错误1：不记录levelSize
```cpp
// ❌ 错误：levelSize在循环中变化
while (!q.empty()) {
    for (int i = 0; i < q.size(); i++) {  // q.size()动态变化
        // ...
    }
}

// ✅ 正确
while (!q.empty()) {
    int levelSize = q.size();  // 提前记录
    for (int i = 0; i < levelSize; i++) {
        // ...
    }
}
```

### 错误2：忘记检查nullptr
```cpp
// ❌ 可能空指针
q.push(node->left);

// ✅ 正确
if (node->left) {
    q.push(node->left);
}
```

### 错误3：queue的pop没有返回值
```cpp
// ❌ 错误
TreeNode* node = q.pop();  // pop()返回void！

// ✅ 正确
TreeNode* node = q.front();
q.pop();
```

---

## 实战应用

### 场景1：计算树的宽度
```cpp
int widthOfBinaryTree(TreeNode* root) {
    if (!root) return 0;
    
    queue<pair<TreeNode*, int>> q;  // (节点, 位置)
    q.push({root, 0});
    int maxWidth = 0;
    
    while (!q.empty()) {
        int levelSize = q.size();
        int left = q.front().second;
        int right = q.back().second;
        maxWidth = max(maxWidth, right - left + 1);
        
        for (int i = 0; i < levelSize; i++) {
            auto [node, pos] = q.front();
            q.pop();
            
            if (node->left) {
                q.push({node->left, 2 * pos});
            }
            if (node->right) {
                q.push({node->right, 2 * pos + 1});
            }
        }
    }
    
    return maxWidth;
}
```

**C++知识点**：
- `pair`：存储两个值
- 结构化绑定：`auto [node, pos]` (C++17)

### 场景2：填充每个节点的下一个右侧节点指针
```cpp
Node* connect(Node* root) {
    if (!root) return root;
    
    queue<Node*> q;
    q.push(root);
    
    while (!q.empty()) {
        int levelSize = q.size();
        
        for (int i = 0; i < levelSize; i++) {
            Node* node = q.front();
            q.pop();
            
            // 连接到同层的下一个节点
            if (i < levelSize - 1) {
                node->next = q.front();
            }
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    
    return root;
}
```

---

## 总结

### 核心要点
1. ✅ **BFS用队列实现**
2. ✅ **记录每层大小很重要**
3. ✅ **掌握queue的基本操作**
4. ✅ **理解二维vector的使用**

### C++算法模板

**层序遍历标准模板**：
```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;
    
    queue<TreeNode*> q;
    q.push(root);
    
    while (!q.empty()) {
        int levelSize = q.size();
        vector<int> level;
        
        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front();
            q.pop();
            
            level.push_back(node->val);
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        result.push_back(level);
    }
    
    return result;
}
```

这道题是**BFS**和**队列**的经典应用，必须熟练掌握！
