# 141. 环形链表 - 编程指导

## 🎯 学习目标
- 理解快慢指针的原理
- 掌握 Floyd 判圈算法
- 学会分析环形结构

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：理解环
<details>
<summary>点击查看提示1</summary>

**什么是环？**

```
无环链表：
1 → 2 → 3 → 4 → 5 → null

有环链表：
1 → 2 → 3 → 4 → 5
        ↑         ↓
        └─────────┘
```

**如何遍历有环链表？**
- 如果直接遍历，会进入无限循环
- 需要一种方法检测是否有环

</details>

---

### 🌟 提示 Level 2：哈希表思路
<details>
<summary>点击查看提示2</summary>

**方法**：记录访问过的节点

```cpp
unordered_set<ListNode*> visited;

while (curr != nullptr) {
    if (visited.count(curr)) {
        return true;  // 再次访问，有环
    }
    visited.insert(curr);
    curr = curr->next;
}

return false;
```

**缺点**：空间 O(n)

**能否优化到 O(1) 空间？**

</details>

---

### 🌟 提示 Level 3：快慢指针
<details>
<summary>点击查看提示3</summary>

**龟兔赛跑算法**：

```
慢指针（乌龟）：每次走1步
快指针（兔子）：每次走2步

如果有环：兔子会在环内追上乌龟
如果无环：兔子会先到达终点
```

**为什么能追上？**
```
环内相对速度 = 2 - 1 = 1步/次
每次距离缩短1步，必然相遇
```

**代码**：
```cpp
ListNode* slow = head;
ListNode* fast = head;

while (fast != nullptr && fast->next != nullptr) {
    slow = slow->next;
    fast = fast->next->next;
    
    if (slow == fast) {
        return true;
    }
}

return false;
```

</details>

---

### 🌟 提示 Level 4：边界处理
<details>
<summary>点击查看提示4</summary>

**边界情况**：

```cpp
// 1. 空链表
if (head == nullptr) return false;

// 2. 单节点（无环）
if (head->next == nullptr) return false;

// 3. 循环条件
// 必须同时判断 fast 和 fast->next
while (fast != nullptr && fast->next != nullptr)
```

**为什么？**
```
fast->next 为 null 时
fast->next->next 会崩溃
```

</details>

---

## 🔨 动手实践

### 任务1：实现快慢指针
```cpp
bool hasCycle(ListNode *head) {
    // TODO: 实现快慢指针
}
```

### 任务2：手动模拟
对链表 `1→2→3→4→5→(3)`，模拟快慢指针的移动过程。

---

## ✅ 自检清单

- [ ] 我理解了什么是环
- [ ] 我知道为什么快指针能追上慢指针
- [ ] 我能独立实现快慢指针
- [ ] 我处理了边界条件

---

**下一步**：独立实现并对照 `solution.md`！
