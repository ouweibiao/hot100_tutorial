# 206. 反转链表 - 编程指导

## 🎯 学习目标
- 掌握链表的基本操作
- 理解迭代和递归两种反转方法
- 学会用指针操作链表

---

## 📖 题目回顾

反转一个单链表。

**示例**：
```
输入：1 → 2 → 3 → 4 → 5 → null
输出：5 → 4 → 3 → 2 → 1 → null
```

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：理解链表
<details>
<summary>点击查看提示1</summary>

**链表节点定义**：
```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};
```

**原链表**：
```
1 → 2 → 3 → null
```
表示：
- 节点1的 next 指向节点2
- 节点2的 next 指向节点3
- 节点3的 next 指向 null

**反转后**：
```
null ← 1 ← 2 ← 3
```
表示：
- 节点3的 next 指向节点2
- 节点2的 next 指向节点1
- 节点1的 next 指向 null

**核心操作**：改变每个节点的 `next` 指针方向。

</details>

---

### 🌟 提示 Level 2：迭代思路
<details>
<summary>点击查看提示2</summary>

**问题**：直接修改 `curr->next` 会丢失后续链表

```
1 → 2 → 3 → null
↑
curr

// 如果直接 curr->next = null
1 → null   (后面的 2→3 丢失了！)
```

**解决**：使用三个指针

```
prev: 指向前一个节点（初始为 null）
curr: 指向当前节点
next: 临时保存下一个节点
```

**每次操作**：
1. `next = curr->next`（保存下一个节点）
2. `curr->next = prev`（反转指针）
3. `prev = curr`（移动 prev）
4. `curr = next`（移动 curr）

**动画演示**：
```
Step 0:
null    1 → 2 → 3
↑       ↑
prev   curr

Step 1: (next=2, curr->next=prev, 移动)
null ← 1    2 → 3
       ↑    ↑
      prev curr

Step 2: (next=3, curr->next=prev, 移动)
null ← 1 ← 2    3
              ↑    ↑
            prev  curr

Step 3: (next=null, curr->next=prev, 移动)
null ← 1 ← 2 ← 3    null
                 ↑    ↑
               prev  curr

返回 prev
```

</details>

---

### 🌟 提示 Level 3：递归思路
<details>
<summary>点击查看提示3</summary>

**递归定义**：
- `reverseList(head)`：反转以 head 开头的链表，返回新头节点

**递归过程**（以 `1→2→3→null` 为例）：

```
reverseList(1)
  先递归反转 2→3
  ↓
reverseList(2)
  先递归反转 3
  ↓
reverseList(3)
  3 后面是 null，返回 3
  ↑
回到 reverseList(2)
  此时：1→2→3→null, newHead=3
  操作：2->next->next = 2  (即让3指向2)
        2->next = null     (断开2→3)
  结果：1→2←3, newHead=3
  ↑
回到 reverseList(1)
  此时：1→2←3, newHead=3
  操作：1->next->next = 1  (即让2指向1)
        1->next = null     (断开1→2)
  结果：null←1←2←3, newHead=3
  
返回 newHead (即3)
```

**关键代码**：
```cpp
ListNode* newHead = reverseList(head->next);  // 递归
head->next->next = head;  // 让下一个节点指向当前节点
head->next = nullptr;     // 断开当前节点的指针
return newHead;
```

</details>

---

### 🌟 提示 Level 4：完整实现
<details>
<summary>点击查看提示4</summary>

**迭代法**：
```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* curr = head;
    
    while (curr != nullptr) {
        ListNode* next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }
    
    return prev;
}
```

**递归法**：
```cpp
ListNode* reverseList(ListNode* head) {
    if (head == nullptr || head->next == nullptr) {
        return head;
    }
    
    ListNode* newHead = reverseList(head->next);
    head->next->next = head;
    head->next = nullptr;
    
    return newHead;
}
```

</details>

---

## 🔨 动手实践

### 任务1：画图模拟
对链表 `1→2→3→null`，手动画出迭代法每一步的指针变化。

### 任务2：实现迭代法
```cpp
ListNode* reverseList(ListNode* head) {
    // TODO: 实现三指针迭代法
}
```

### 任务3：实现递归法
```cpp
ListNode* reverseList(ListNode* head) {
    // TODO: 实现递归法
}
```

### 任务4：测试
```cpp
// 用例1：正常链表
1→2→3→4→5  // 预期: 5→4→3→2→1

// 用例2：单节点
1  // 预期: 1

// 用例3：空链表
null  // 预期: null

// 用例4：两个节点
1→2  // 预期: 2→1
```

---

## 🎯 关键技巧

### 1. 迭代 vs 递归

| 方法 | 时间 | 空间 | 优点 | 缺点 |
|------|------|------|------|------|
| 迭代 | O(n) | O(1) | 空间最优 | 代码稍复杂 |
| 递归 | O(n) | O(n) | 代码简洁 | 栈空间消耗 |

### 2. 三指针口诀

> 保存下一个（next）  
> 反转当前指针（curr->next = prev）  
> 移动 prev 和 curr

### 3. 递归口诀

> 递归到底找新头  
> 回溯反转指针  
> 断开原有连接

---

## 🚀 进阶挑战

### 挑战1：反转链表的一部分
反转链表从位置 m 到 n 的部分。

**示例**：
```
输入：1→2→3→4→5, m=2, n=4
输出：1→4→3→2→5
```

### 挑战2：两两交换节点
交换链表中相邻的两个节点。

**示例**：
```
输入：1→2→3→4
输出：2→1→4→3
```

### 挑战3：K个一组翻转
每K个节点一组进行翻转。

---

## ✅ 自检清单

- [ ] 我理解了链表的结构
- [ ] 我知道为什么需要三个指针
- [ ] 我能手动画出每一步的指针变化
- [ ] 我理解了递归的过程
- [ ] 我能独立实现两种方法
- [ ] 我知道两种方法的优缺点

---

## 📌 核心要点

**迭代法**：
```
三指针：prev, curr, next
四步骤：保存、反转、移动、移动
```

**递归法**：
```
边界：head == null || head->next == null
递归：newHead = reverseList(head->next)
反转：head->next->next = head
断开：head->next = null
```

---

## 🐛 调试技巧

### 1. 打印链表
```cpp
void printList(ListNode* head) {
    while (head) {
        cout << head->val << " → ";
        head = head->next;
    }
    cout << "null" << endl;
}
```

### 2. 检查指针
在每一步打印指针的值：
```cpp
cout << "prev: " << (prev ? prev->val : -1) << endl;
cout << "curr: " << (curr ? curr->val : -1) << endl;
```

---

**下一步**：独立实现并对照 `solution.md`！
