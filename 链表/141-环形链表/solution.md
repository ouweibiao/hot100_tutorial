# 141. 环形链表

## 📝 题目描述

给你一个链表的头节点 `head`，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。如果链表中存在环，则返回 `true`。否则，返回 `false`。

**示例 1：**
```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**
```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**
```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

**约束条件：**
- 链表中节点的数目范围是 `[0, 10^4]`
- `-10^5 <= Node.val <= 10^5`
- `pos` 为 `-1` 或者链表中的一个有效索引

**进阶**：你能用 `O(1)` 空间解决此题吗？

---

## ⭐ 解法一：快慢指针（Floyd 判圈算法）

### 思路

使用两个指针：
- **慢指针 `slow`**：每次移动 1 步
- **快指针 `fast`**：每次移动 2 步

**关键观察**：
- 如果没有环，快指针会先到达链表末尾（`null`）
- 如果有环，快指针一定会在环内追上慢指针

**为什么快指针一定能追上慢指针？**
- 快指针每次比慢指针多走 1 步
- 在环内，快指针会逐渐接近慢指针，最终相遇

### 代码实现
```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if (head == nullptr || head->next == nullptr) {
            return false;
        }
        
        ListNode* slow = head;
        ListNode* fast = head;
        
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;           // 慢指针走1步
            fast = fast->next->next;     // 快指针走2步
            
            if (slow == fast) {          // 相遇，有环
                return true;
            }
        }
        
        return false;  // 快指针到达末尾，无环
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
  - 无环：快指针遍历一次到末尾
  - 有环：快指针最多绕环一圈追上慢指针
- **空间复杂度**：O(1)

---

## 💡 解法二：哈希表

### 思路

使用哈希表记录访问过的节点，如果再次访问到已记录的节点，说明有环。

### 代码实现
```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        unordered_set<ListNode*> visited;
        
        ListNode* curr = head;
        while (curr != nullptr) {
            if (visited.count(curr)) {
                return true;  // 节点已访问过，有环
            }
            visited.insert(curr);
            curr = curr->next;
        }
        
        return false;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
- **空间复杂度**：O(n)，哈希表存储节点

---

## 🎯 核心知识点

### 1. Floyd 判圈算法（龟兔赛跑）

**原理**：
- 在环形跑道上，跑得快的一定会追上跑得慢的
- 快指针每次比慢指针多走 1 步，在环内必然相遇

**为什么不会错过？**
```
假设慢指针和快指针在环内相差 k 步
第1次：相差 k-1 步
第2次：相差 k-2 步
...
第k次：相差 0 步 → 相遇！
```

### 2. 为什么快指针走2步？

- **走2步**：保证能追上，且步数最少
- **走3步或更多**：可能会跳过慢指针，需要绕多圈

### 3. 边界条件

```cpp
// 空链表或单节点
if (head == nullptr || head->next == nullptr) {
    return false;
}

// 循环条件：fast 和 fast->next 都不为空
while (fast != nullptr && fast->next != nullptr)
```

---

## 🔍 图解过程

```
链表：1 → 2 → 3 → 4 → 5
                    ↑     ↓
                    └─────┘

初始：
slow → 1, fast → 1

第1步：
slow → 2, fast → 3

第2步：
slow → 3, fast → 5

第3步：
slow → 4, fast → 4  ← 相遇！

返回 true
```

---

## 🎓 变式题目

### 1. 环形链表 II（LeetCode 142）
找到环的入口节点。

**提示**：
1. 先用快慢指针找到相遇点
2. 一个指针从头开始，一个从相遇点开始，同时走，相遇点即为入口

### 2. 快乐数（LeetCode 202）
判断一个数是否是快乐数（也是判环问题）。

---

## 📌 记忆要点

- **方法**：快慢指针（Floyd 判圈）
- **慢指针**：每次走 1 步
- **快指针**：每次走 2 步
- **判断**：相遇则有环，快指针到末尾则无环
- **复杂度**：O(n) 时间，O(1) 空间

---

## 🔨 常见错误

### 错误1：初始位置错误
```cpp
// ❌ 错误：都从 head 开始，立即相遇
ListNode* slow = head;
ListNode* fast = head;
if (slow == fast) return true;  // 错误！

// ✅ 正确：先移动，再判断
while (fast != nullptr && fast->next != nullptr) {
    slow = slow->next;
    fast = fast->next->next;
    if (slow == fast) return true;
}
```

### 错误2：边界条件不完整
```cpp
// ❌ 错误：可能 fast->next 为 null，访问 next->next 崩溃
while (fast != nullptr) {
    fast = fast->next->next;
}

// ✅ 正确：同时判断 fast 和 fast->next
while (fast != nullptr && fast->next != nullptr) {
    fast = fast->next->next;
}
```

---

**标签**：`链表` `双指针` `easy`
