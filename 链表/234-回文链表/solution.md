# 234. 回文链表

## 题目描述

给你一个单链表的头节点 `head` ，请你判断该链表是否为回文链表。如果是，返回 `true` ；否则，返回 `false` 。

**示例 1：**
```
输入：head = [1,2,2,1]
输出：true
```

**示例 2：**
```
输入：head = [1,2]
输出：false
```

**进阶：**你能否用 `O(n)` 时间复杂度和 `O(1)` 空间复杂度解决此题？

---

## 解法一：转为数组（简单直观）

### 核心思路
将链表元素复制到数组中，然后使用双指针判断数组是否为回文。

### 代码实现
```cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        vector<int> vals;
        while (head) {
            vals.push_back(head->val);
            head = head->next;
        }
        
        int left = 0, right = vals.size() - 1;
        while (left < right) {
            if (vals[left] != vals[right]) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，遍历两次（一次复制，一次判断）。
- **空间复杂度**：O(n)，使用了数组存储所有元素。

---

## 解法二：快慢指针 + 反转链表（进阶最优）⭐

### 核心思路
为了达到 O(1) 空间，我们直接在链表上操作。
1. **找中点**：使用快慢指针找到链表的中心。
2. **反转后半部分**：将后半部分链表反转。
3. **比较**：前后两部分同步遍历比较。
4. **恢复**：将后半部分反转回来（虽然题目没强制要求，但是好习惯）。

### 图解
```
原始：1 -> 2 -> 3 -> 2 -> 1

1. 找中点：slow指向3
2. 反转后半：
   前半：1 -> 2 -> 3
   后半：1 -> 2
   (注意：中间节点归属处理视奇偶性而定，通常反转 slow->next)

3. 比较：
   p1 = head (1)
   p2 = tail (1)
   相等 -> next
   ...
```

### 代码实现
```cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        if (!head || !head->next) return true;
        
        // 1. 找中点
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast->next && fast->next->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        // 2. 反转后半部分
        ListNode* secondHalf = reverseList(slow->next);
        
        // 3. 比较
        ListNode* p1 = head;
        ListNode* p2 = secondHalf;
        bool result = true;
        
        while (result && p2 != nullptr) {
            if (p1->val != p2->val) {
                result = false;
            }
            p1 = p1->next;
            p2 = p2->next;
        }
        
        // 4. 恢复链表（可选）
        slow->next = reverseList(secondHalf);
        
        return result;
    }
    
    // 辅助函数：反转链表
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr) {
            ListNode* nextTemp = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nextTemp;
        }
        return prev;
    }
};
```

### 细节解析
- **快慢指针条件**：`fast->next && fast->next->next` 保证偶数长度时 slow 停在左中点，奇数长度时 slow 停在中点。
- **反转起点**：`slow->next` 是后半部分的起点。

### 复杂度分析
- **时间复杂度**：O(n)。找中点 O(n)，反转 O(n)，比较 O(n)。
- **空间复杂度**：O(1)。只使用了几个指针变量。

---

## 总结

- 面试中如果没限制空间，先说 O(n) 数组解法，再引出 O(1) 解法。
- 链表操作的综合题：结合了**找中点**、**反转链表**两个基础操作。
- 务必注意不能改变链表结构（或者改变后要还原），否则在并发环境下会出问题。

---

**标签**：`链表` `双指针` `easy`
