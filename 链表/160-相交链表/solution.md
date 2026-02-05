# 160. 相交链表

## 题目描述

给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 `null` 。

图示如下：
```
A:       a1 -> a2
                 \
                  c1 -> c2 -> c3
                 /
B: b1 -> b2 -> b3
```
题目数据 **保证** 整个链式结构中不存在环。

**注意**，函数返回结果后，链表必须 **保持其原始结构** 。

### 示例

**示例 1：**
```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
输出：Intersected at '8'
解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,6,1,8,4,5]。
在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

**示例 2：**
```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。
由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
这两个链表不相交，因此返回 null 。
```

---

## C++基础知识

### 1. 指针比较
在C++中，判断两个节点是否相同，是比较它们的**内存地址**，即直接比较指针变量。
```cpp
if (pA == pB) { ... } // 比较地址
```
注意不要比较 `val`，因为不同节点可能有相同的值。

---

## 解法一：哈希表

### 核心思路
1. 遍历链表A，将所有节点（的地址）存入哈希集合。
2. 遍历链表B，对于每个节点，检查它是否在集合中。
3. 第一个在集合中发现的节点就是相交点。

### 代码实现
```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        unordered_set<ListNode*> visited;
        
        // 记录A中所有节点
        ListNode* temp = headA;
        while (temp != nullptr) {
            visited.insert(temp);
            temp = temp->next;
        }
        
        // 检查B中节点
        temp = headB;
        while (temp != nullptr) {
            if (visited.count(temp)) {
                return temp;
            }
            temp = temp->next;
        }
        
        return nullptr;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(m + n)，其中 m 和 n 是两个链表的长度。
- **空间复杂度**：O(m)，需要存储链表A的所有节点。

---

## 解法二：双指针（浪漫相遇）⭐

### 核心思路
这是一个非常巧妙的数学解法。
设链表A的非公共部分长度为 `a`，链表B的非公共部分长度为 `b`，公共部分长度为 `c`。
- 链表A全长：`a + c`
- 链表B全长：`b + c`

如果我们让两个指针 `pA` 和 `pB` 分别从 `headA` 和 `headB` 出发：
1. `pA` 走完链表A后，接着走链表B。
2. `pB` 走完链表B后，接着走链表A。

当它们相遇时，走过的路程：
- `pA`：`a + c + b`
- `pB`：`b + c + a`
显然 `a + c + b = b + c + a`，它们会在公共起点相遇！

如果两个链表不相交，它们会同时到达 `nullptr`（此时路程为 `a + b`），也算一种"相遇"。

### 图解
```
A: [1, 2, 3] -> [6, 7] (公共)
B: [4, 5]    -> [6, 7] (公共)

pA路线: 1->2->3->6->7 ->(转B)-> 4->5->6(相遇)
pB路线: 4->5->6->7    ->(转A)-> 1->2->3->6(相遇)
```

### 代码实现
```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (headA == nullptr || headB == nullptr) {
            return nullptr;
        }
        
        ListNode* pA = headA;
        ListNode* pB = headB;
        
        while (pA != pB) {
            // pA走完了走B，否则走下一步
            pA = (pA == nullptr) ? headB : pA->next;
            
            // pB走完了走A，否则走下一步
            pB = (pB == nullptr) ? headA : pB->next;
        }
        
        return pA;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(m + n)，最坏情况两个指针各走一遍两个链表。
- **空间复杂度**：O(1)，只用了两个指针。

---

## 总结

- **哈希表法**：直观，容易想到，但空间占用大。
- **双指针法**：技巧性强，代码极其简洁，是**最优解**。
- 这道题的核心在于消除**长度差**。

---

**标签**：`链表` `双指针` `easy`
