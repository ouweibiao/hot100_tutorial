# 21. 合并两个有序链表

## 📝 题目描述

将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

**示例 1：**
```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

**示例 2：**
```
输入：l1 = [], l2 = []
输出：[]
```

**示例 3：**
```
输入：l1 = [], l2 = [0]
输出：[0]
```

**约束条件：**
- 两个链表的节点数目范围是 `[0, 50]`
- `-100 <= Node.val <= 100`
- `l1` 和 `l2` 均按非递减顺序排列

---

## ⭐ 解法一：迭代

### 思路

使用虚拟头节点（哑节点）和尾指针：
1. 创建虚拟头节点 `dummy`
2. 比较两个链表的当前节点，选择较小的节点接到结果链表
3. 移动对应的指针
4. 最后处理剩余的节点

### 代码实现
```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        // 虚拟头节点
        ListNode* dummy = new ListNode(0);
        ListNode* tail = dummy;
        
        // 两个链表都未遍历完
        while (l1 != nullptr && l2 != nullptr) {
            if (l1->val <= l2->val) {
                tail->next = l1;
                l1 = l1->next;
            } else {
                tail->next = l2;
                l2 = l2->next;
            }
            tail = tail->next;
        }
        
        // 处理剩余节点
        if (l1 != nullptr) {
            tail->next = l1;
        }
        if (l2 != nullptr) {
            tail->next = l2;
        }
        
        ListNode* result = dummy->next;
        delete dummy;  // 释放虚拟头节点
        return result;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(m + n)，m 和 n 是两个链表的长度
- **空间复杂度**：O(1)

---

## 💡 解法二：递归

### 思路

**递归定义**：
- 比较两个链表的头节点
- 选择较小的作为当前节点
- 递归合并剩余部分

### 代码实现
```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        // 边界条件
        if (l1 == nullptr) return l2;
        if (l2 == nullptr) return l1;
        
        // 选择较小的节点
        if (l1->val <= l2->val) {
            l1->next = mergeTwoLists(l1->next, l2);
            return l1;
        } else {
            l2->next = mergeTwoLists(l1, l2->next);
            return l2;
        }
    }
};
```

### 复杂度分析
- **时间复杂度**：O(m + n)
- **空间复杂度**：O(m + n)，递归栈空间

---

## 🎯 核心知识点

### 1. 虚拟头节点（哑节点）

**作用**：简化边界处理

```cpp
// 不使用虚拟头节点（复杂）
if (result == nullptr) {
    result = node;
    tail = result;
} else {
    tail->next = node;
    tail = tail->next;
}

// 使用虚拟头节点（简洁）
tail->next = node;
tail = tail->next;
```

### 2. 归并思想

类似于归并排序的合并过程：
- 比较两个有序序列的首元素
- 选择较小的加入结果
- 重复直到一个序列为空
- 接上剩余序列

### 3. 迭代 vs 递归

| 方法 | 时间 | 空间 | 优点 | 缺点 |
|------|------|------|------|------|
| 迭代 | O(m+n) | O(1) | 空间最优 | 代码稍长 |
| 递归 | O(m+n) | O(m+n) | 代码简洁 | 可能栈溢出 |

---

## 🔍 图解过程（迭代）

```
l1: 1 → 2 → 4
l2: 1 → 3 → 4

dummy → null
tail ↑

Step 1: 比较 1 和 1，选 l1
dummy → 1
tail    ↑
l1: 2 → 4
l2: 1 → 3 → 4

Step 2: 比较 2 和 1，选 l2
dummy → 1 → 1
tail        ↑
l1: 2 → 4
l2: 3 → 4

Step 3: 比较 2 和 3，选 l1
dummy → 1 → 1 → 2
tail            ↑
l1: 4
l2: 3 → 4

Step 4: 比较 4 和 3，选 l2
dummy → 1 → 1 → 2 → 3
tail                ↑
l1: 4
l2: 4

Step 5: 比较 4 和 4，选 l1
dummy → 1 → 1 → 2 → 3 → 4
tail                    ↑
l1: null
l2: 4

Step 6: l1 为空，接上 l2
dummy → 1 → 1 → 2 → 3 → 4 → 4

返回 dummy->next
```

---

## 🎓 变式题目

### 1. 合并 K 个升序链表（LeetCode 23）
给定 K 个升序链表，合并成一个。

**提示**：使用优先队列（最小堆）。

### 2. 合并两个有序数组（LeetCode 88）
数组版本的合并问题。

### 3. 排序链表（LeetCode 148）
使用归并排序对链表排序。

---

## 📌 记忆要点

- **虚拟头节点**：简化边界处理
- **比较合并**：类似归并排序
- **剩余处理**：直接接上剩余链表
- **两种方法**：迭代（推荐）、递归

---

## 🔨 常见错误

### 错误1：忘记移动 tail
```cpp
// ❌ 错误
tail->next = l1;
l1 = l1->next;
// 忘记移动 tail

// ✅ 正确
tail->next = l1;
l1 = l1->next;
tail = tail->next;
```

### 错误2：处理剩余节点复杂化
```cpp
// ❌ 复杂：逐个接上
while (l1 != nullptr) {
    tail->next = l1;
    tail = tail->next;
    l1 = l1->next;
}

// ✅ 简洁：直接接上整条链表
if (l1 != nullptr) {
    tail->next = l1;
}
```

---

**标签**：`链表` `递归` `easy`
