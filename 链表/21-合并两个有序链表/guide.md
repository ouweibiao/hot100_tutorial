# 21. 合并两个有序链表 - 编程指导

## 🎯 学习目标
- 掌握虚拟头节点的使用
- 理解归并思想
- 学会链表的迭代和递归操作

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：归并思想
<details>
<summary>点击查看提示1</summary>

**类似问题**：合并两个有序数组

```
数组1: [1, 3, 5]
数组2: [2, 4, 6]

比较 1 和 2，选 1
比较 3 和 2，选 2
比较 3 和 4，选 3
...

结果: [1, 2, 3, 4, 5, 6]
```

**链表同理**：逐个比较，选择较小的。

</details>

---

### 🌟 提示 Level 2：虚拟头节点
<details>
<summary>点击查看提示2</summary>

**问题**：如何处理第一个节点？

```cpp
// 不使用虚拟头节点（复杂）
ListNode* result = nullptr;
ListNode* tail = nullptr;

// 第一个节点需要特殊处理
if (l1->val <= l2->val) {
    result = l1;
    tail = l1;
    l1 = l1->next;
} else {
    result = l2;
    tail = l2;
    l2 = l2->next;
}

// 后续节点
while (...) {
    tail->next = ...;
    tail = tail->next;
}
```

**使用虚拟头节点（简洁）**：
```cpp
ListNode* dummy = new ListNode(0);
ListNode* tail = dummy;

// 统一处理
while (...) {
    tail->next = ...;
    tail = tail->next;
}

return dummy->next;
```

</details>

---

### 🌟 提示 Level 3：迭代实现
<details>
<summary>点击查看提示3</summary>

```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode* dummy = new ListNode(0);
    ListNode* tail = dummy;
    
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
    
    // 接上剩余部分
    tail->next = (l1 != nullptr) ? l1 : l2;
    
    ListNode* result = dummy->next;
    delete dummy;
    return result;
}
```

</details>

---

### 🌟 提示 Level 4：递归实现
<details>
<summary>点击查看提示4</summary>

**递归定义**：
```
merge(l1, l2) = 
  if l1 == null: return l2
  if l2 == null: return l1
  if l1.val <= l2.val:
    l1.next = merge(l1.next, l2)
    return l1
  else:
    l2.next = merge(l1, l2.next)
    return l2
```

**代码**：
```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    if (l1 == nullptr) return l2;
    if (l2 == nullptr) return l1;
    
    if (l1->val <= l2->val) {
        l1->next = mergeTwoLists(l1->next, l2);
        return l1;
    } else {
        l2->next = mergeTwoLists(l1, l2->next);
        return l2;
    }
}
```

</details>

---

## 🔨 动手实践

### 任务1：实现迭代版本
```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    // TODO: 使用虚拟头节点 + 迭代
}
```

### 任务2：实现递归版本
```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    // TODO: 递归实现
}
```

---

## ✅ 自检清单

- [ ] 我理解了虚拟头节点的作用
- [ ] 我能独立实现迭代版本
- [ ] 我能独立实现递归版本
- [ ] 我处理了边界情况

---

**下一步**：独立实现并对照 `solution.md`！
