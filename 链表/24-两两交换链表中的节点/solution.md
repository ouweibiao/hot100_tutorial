# 24. 两两交换链表中的节点

## 题目描述

给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

**示例 1：**
```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**示例 2：**
```
输入：head = []
输出：[]
```

**示例 3：**
```
输入：head = [1]
输出：[1]
```

---

## 解法一：递归（代码最简洁）

### 核心思路
将链表分为三部分：
1. `head`：第一个节点
2. `next`：第二个节点
3. `rest`：剩余链表

交换过程：
- `next` 变成新的头。
- `head` 变成第二个节点。
- `head->next` 指向 `递归处理(rest)` 的结果。

### 代码实现
```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        // 终止条件：没有节点或只有一个节点，无法交换
        if (head == nullptr || head->next == nullptr) {
            return head;
        }
        
        ListNode* nextNode = head->next;
        
        // 递归处理剩余部分
        head->next = swapPairs(nextNode->next);
        
        // 交换当前两个节点
        nextNode->next = head;
        
        return nextNode; // 返回新的头节点
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，每个节点处理一次。
- **空间复杂度**：O(n)，递归栈的深度。

---

## 解法二：迭代（空间最优）⭐

### 核心思路
使用 `dummy` 节点指向头节点，每次处理两个节点。
我们需要操作三个指针：
- `temp`：当前要交换的一对节点的前驱（初始为 dummy）
- `node1`：第一个节点
- `node2`：第二个节点

操作步骤：
1. `temp->next = node2`
2. `node1->next = node2->next`
3. `node2->next = node1`
4. `temp = node1` (准备下一轮)

### 图解
```
初始：dummy(temp) -> 1(node1) -> 2(node2) -> 3 -> 4

步骤1：temp 指向 node2
dummy -> 2

步骤2：node1 指向 node2的后面
1 -> 3

步骤3：node2 指向 node1
2 -> 1

结果：dummy -> 2 -> 1 -> 3 -> 4
状态更新：temp 移动到 1，准备处理 3, 4
```

### 代码实现
```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode dummy(0, head);
        ListNode* temp = &dummy;
        
        // 只要后面还有两个节点，就继续交换
        while (temp->next != nullptr && temp->next->next != nullptr) {
            ListNode* node1 = temp->next;
            ListNode* node2 = temp->next->next;
            
            // 交换步骤
            temp->next = node2;
            node1->next = node2->next;
            node2->next = node1;
            
            // 移动 temp 到下一组的前驱位置
            temp = node1;
        }
        
        return dummy.next;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)。
- **空间复杂度**：O(1)。

---

## 总结

- **递归法**：逻辑清晰，符合函数式思维，但空间占用 O(n)。
- **迭代法**：指针操作繁琐，容易出错，但空间 O(1)，是工程上的更优解。
- 所有的链表指针修改题，**画图**都是第一步！

---

**标签**：`链表` `递归` `medium`
