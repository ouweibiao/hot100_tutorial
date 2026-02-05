# 19. 删除链表的倒数第 N 个结点

## 题目描述

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**示例 1：**
```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**示例 2：**
```
输入：head = [1], n = 1
输出：[]
```

**示例 3：**
```
输入：head = [1,2], n = 1
输出：[1]
```

**提示：**
- 链表中结点的数目为 `sz`
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

**进阶：**你能尝试使用一趟扫描实现吗？

---

## 解法一：计算链表长度（两趟扫描）

### 核心思路
1. 第一趟遍历：计算链表总长度 `L`。
2. 第二趟遍历：找到第 `L - n` 个节点（即被删节点的前驱）。
3. 执行删除操作。

### 代码实现
```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode dummy(0, head); // 哑节点，方便处理头节点被删的情况
        int length = 0;
        ListNode* curr = head;
        
        // 1. 计算长度
        while (curr) {
            length++;
            curr = curr->next;
        }
        
        // 2. 找到前驱节点
        length -= n;
        curr = &dummy;
        while (length > 0) {
            length--;
            curr = curr->next;
        }
        
        // 3. 删除
        ListNode* toDelete = curr->next;
        curr->next = curr->next->next;
        delete toDelete; // 释放内存
        
        return dummy.next;
    }
};
```

---

## 解法二：双指针法（一趟扫描）⭐

### 核心思路
利用两个指针保持固定的距离 `n`。
1. 让 `fast` 指针先走 `n + 1` 步。
2. `fast` 和 `slow` 同时走，直到 `fast` 到达末尾。
3. 此时 `slow` 刚好指向倒数第 `n + 1` 个节点（即前驱节点）。
4. 删除 `slow->next`。

为什么是 `n + 1` 步？
因为我们需要找到被删节点的前一个节点。如果只让 `fast` 先走 `n` 步，当 `fast` 到达末尾（`nullptr`）时，`slow` 指向的是倒数第 `n` 个节点（即被删节点本身），单链表无法直接删除自己（或者说不方便）。

### 图解
```
head = [1, 2, 3, 4, 5], n = 2
dummy -> 1 -> 2 -> 3 -> 4 -> 5 -> null

1. fast先走 n+1 = 3步
   dummy(slow)      fast
     |               |
     v               v
   [dummy] -> 1 -> 2 -> 3 -> 4 -> 5

2. 同时走，直到 fast == null
                      slow         fast
                       |            |
                       v            v
   [dummy] -> 1 -> 2 -> 3 -> 4 -> 5 -> null

3. 此时 slow 指向 3，倒数第2个节点是 4。
   slow->next = slow->next->next
   3 -> 5
```

### 代码实现
```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        // 哑节点：处理头节点被删除的特殊情况
        ListNode* dummy = new ListNode(0, head);
        ListNode* fast = dummy;
        ListNode* slow = dummy;
        
        // 1. fast 先走 n + 1 步
        for (int i = 0; i <= n; i++) {
            fast = fast->next;
        }
        
        // 2. 同时移动，直到 fast 到达末尾
        while (fast != nullptr) {
            fast = fast->next;
            slow = slow->next;
        }
        
        // 3. 删除 slow 的下一个节点
        ListNode* toDelete = slow->next;
        slow->next = slow->next->next;
        delete toDelete; // 良好的编程习惯：释放内存
        
        ListNode* newHead = dummy->next;
        delete dummy; // 释放哑节点
        return newHead;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(L)，其中 L 是链表长度。只遍历了一次。
- **空间复杂度**：O(1)，只使用了常数个指针。

---

## 总结

- **哑节点 (Dummy Node)**：链表题目的神器，避免了删除头节点时的特殊判断。
- **双指针**：通过让一个指针先走，维持固定的"窗口"大小，从而一次遍历定位特定位置。

---

**标签**：`链表` `双指针` `medium`
