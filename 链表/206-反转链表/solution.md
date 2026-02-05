# 206. 反转链表

## 📝 题目描述

给你单链表的头节点 `head`，请你反转链表，并返回反转后的链表。

**示例 1：**
```
输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]
```

**示例 2：**
```
输入：head = [1,2]
输出：[2,1]
```

**示例 3：**
```
输入：head = []
输出：[]
```

**约束条件：**
- 链表中节点的数目范围是 `[0, 5000]`
- `-5000 <= Node.val <= 5000`

---

## ⭐ 解法一：迭代（推荐）

### 思路

使用三个指针：
- `prev`：指向前一个节点（初始为 null）
- `curr`：指向当前节点（初始为 head）
- `next`：临时保存下一个节点

**核心操作**：
1. 保存 `next = curr->next`（防止链表断裂）
2. 反转指针 `curr->next = prev`
3. 移动指针 `prev = curr`, `curr = next`

### 代码实现
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        
        while (curr != nullptr) {
            ListNode* next = curr->next;  // 保存下一个节点
            curr->next = prev;            // 反转指针
            prev = curr;                  // 移动 prev
            curr = next;                  // 移动 curr
        }
        
        return prev;  // prev 是新的头节点
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，遍历一次链表
- **空间复杂度**：O(1)，只使用常量额外空间

---

## 💡 解法二：递归

### 思路

**递归定义**：`reverseList(head)` 返回反转后的头节点

**递归过程**：
1. 递归到最后一个节点，它将成为新的头节点
2. 回溯时，让下一个节点指向当前节点
3. 当前节点指向 null

### 代码实现
```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        // 边界条件：空链表或只有一个节点
        if (head == nullptr || head->next == nullptr) {
            return head;
        }
        
        // 递归反转后面的链表
        ListNode* newHead = reverseList(head->next);
        
        // 让下一个节点指向当前节点
        head->next->next = head;
        // 当前节点指向 null
        head->next = nullptr;
        
        return newHead;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)
- **空间复杂度**：O(n)，递归栈空间

---

## 🎯 核心知识点

### 1. 链表反转的本质

**原链表**：
```
1 → 2 → 3 → 4 → 5 → null
```

**反转后**：
```
null ← 1 ← 2 ← 3 ← 4 ← 5
```

每个节点的 `next` 指针都要指向前一个节点。

### 2. 迭代法的关键

**三个指针的作用**：
```
null  ←  1  →  2  →  3  →  4  →  5
↑        ↑     ↑
prev    curr  next
```

**每一步操作**：
1. `next = curr->next`：保存下一个节点（防止丢失）
2. `curr->next = prev`：反转当前节点的指针
3. `prev = curr`：prev 向前移动
4. `curr = next`：curr 向前移动

### 3. 递归法的理解

**递归过程**（以 `1→2→3→null` 为例）：

```
reverseList(1)
  ↓ 递归
reverseList(2)
  ↓ 递归
reverseList(3)
  ↓ 递归
reverseList(null)  // 返回 null
  ↑ 回溯：3->next=null, return 3
回到 reverseList(2)
  执行：2->next->next = 2  即 3->next = 2
       2->next = null
  return 3
  ↑ 回溯
回到 reverseList(1)
  执行：1->next->next = 1  即 2->next = 1
       1->next = null
  return 3

最终：3 → 2 → 1 → null
```

---

## 🔍 图解过程（迭代法）

以 `1 → 2 → 3 → null` 为例：

```
初始状态：
prev = null, curr = 1
null    1 → 2 → 3 → null
↑       ↑
prev   curr

Step 1：
next = 2
curr->next = prev (即 1->next = null)
移动指针：prev = 1, curr = 2

null ← 1    2 → 3 → null
       ↑    ↑
      prev curr

Step 2：
next = 3
curr->next = prev (即 2->next = 1)
移动指针：prev = 2, curr = 3

null ← 1 ← 2    3 → null
              ↑    ↑
            prev  curr

Step 3：
next = null
curr->next = prev (即 3->next = 2)
移动指针：prev = 3, curr = null

null ← 1 ← 2 ← 3    null
                 ↑    ↑
               prev  curr

结束：curr == null，返回 prev (即 3)
```

---

## 🎓 变式题目

### 1. 反转链表 II（LeetCode 92）
反转链表的一部分，从位置 `left` 到 `right`。

### 2. K 个一组翻转链表（LeetCode 25）
每 K 个节点一组进行翻转。

### 3. 回文链表（LeetCode 234）
判断链表是否是回文结构。

**提示**：找到中点，反转后半部分，然后比较。

---

## 📌 记忆要点

### 迭代法
- **三个指针**：prev, curr, next
- **四个步骤**：
  1. 保存 next
  2. 反转 curr->next
  3. 移动 prev
  4. 移动 curr
- **返回**：prev（新头节点）

### 递归法
- **递归到底**：找到最后一个节点（新头）
- **回溯反转**：`head->next->next = head`
- **断开原指针**：`head->next = null`

---

## 🔨 常见错误

### 错误1：忘记保存 next
```cpp
// ❌ 错误：直接修改 curr->next，丢失了后续链表
curr->next = prev;
curr = curr->next;  // curr 变成了 prev，死循环！

// ✅ 正确：先保存 next
ListNode* next = curr->next;
curr->next = prev;
curr = next;
```

### 错误2：递归边界错误
```cpp
// ❌ 错误：只判断了 head == null
if (head == nullptr) return head;

// ✅ 正确：还要判断 head->next == null
if (head == nullptr || head->next == nullptr) return head;
```

### 错误3：递归中修改错误
```cpp
// ❌ 错误：顺序错误，先断开了链表
head->next = nullptr;
head->next->next = head;  // head->next 已经是 null！

// ✅ 正确：先反转，再断开
head->next->next = head;
head->next = nullptr;
```

---

## 🎓 面试技巧

### 1. 先问清楚要求
- 是否可以修改原链表？（通常可以）
- 空间复杂度要求？（迭代 O(1)，递归 O(n)）

### 2. 画图演示
在白板或纸上画出链表，演示指针变化。

### 3. 两种方法都要会
- 迭代法：空间 O(1)，更优
- 递归法：代码简洁，展示递归思维

---

**标签**：`链表` `递归` `easy`
