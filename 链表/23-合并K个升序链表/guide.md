# 23. 合并K个升序链表 - 渐进式学习指南

> 💡 **学习目标**：掌握优先队列和分治算法，学习C++模板和Lambda表达式

---

## 🎯 问题理解

### 核心问题
合并k个已排序的链表为一个有序链表。

### 示例
```
输入：
链表1: 1 → 4 → 5
链表2: 1 → 3 → 4
链表3: 2 → 6

输出：
1 → 1 → 2 → 3 → 4 → 4 → 5 → 6
```

### 关键观察
1. 每个链表已经有序
2. 需要找全局最小值
3. K可能很大（10^4）

---

## 💭 思考引导

### 问题1：如何合并2个链表？
你会怎么合并两个有序链表？

<details>
<summary>💡 提示</summary>

**双指针法**：
```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode dummy(0);
    ListNode* tail = &dummy;
    
    while (l1 && l2) {
        if (l1->val < l2->val) {
            tail->next = l1;
            l1 = l1->next;
        } else {
            tail->next = l2;
            l2 = l2->next;
        }
        tail = tail->next;
    }
    
    tail->next = l1 ? l1 : l2;
    return dummy.next;
}
```

**时间复杂度**：O(n1 + n2)

</details>

### 问题2：如何扩展到K个链表？
有了合并2个的方法，如何合并K个？

<details>
<summary>💡 提示</summary>

**思路1：逐个合并**
```
result = lists[0]
for i = 1 to k-1:
    result = merge(result, lists[i])
```
**时间复杂度**：O(kN) - 效率低

**思路2：分治合并**
```
两两合并，递归处理
第1轮：k个 → k/2个
第2轮：k/2个 → k/4个
...
```
**时间复杂度**：O(N log k) - 更好！

</details>

### 问题3：如何快速找最小值？
每次需要从k个链表中找最小值，有更快的方法吗？

<details>
<summary>💡 提示</summary>

**数据结构**：**最小堆（优先队列）**

**原理**：
- 维护k个链表的当前节点
- 每次O(log k)取最小
- 总时间：O(N log k)

**C++实现**：
```cpp
priority_queue<ListNode*, vector<ListNode*>, 自定义比较器> minHeap;
```

</details>

---

## 📚 C++基础知识

### Level 1：链表结构 ⭐

<details>
<summary>点击查看</summary>

#### 链表定义

```cpp
struct ListNode {
    int val;              // 值
    ListNode *next;       // 指向下一个节点的指针
    
    // 构造函数
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};
```

#### C++知识点

**1. struct vs class**
```cpp
struct Node { int val; };  // 默认public
class Node { int val; };   // 默认private
```

**2. 初始化列表**
```cpp
// 构造函数初始化列表（推荐）
ListNode(int x) : val(x), next(nullptr) {}

// 等价于（但效率低）
ListNode(int x) {
    val = x;
    next = nullptr;
}
```

**3. nullptr**
```cpp
// C++11推荐
ListNode* p = nullptr;  // 类型安全

// 旧风格（不推荐）
ListNode* p = NULL;     // 实际是整数0
```

#### 常用操作

```cpp
// 创建节点
ListNode* node = new ListNode(1);

// 访问值
int val = node->val;

// 移动指针
node = node->next;

// 哑节点技巧
ListNode dummy(0);
ListNode* tail = &dummy;  // 取地址
```

</details>

---

### Level 2：优先队列（堆）⭐⭐

<details>
<summary>点击查看</summary>

#### 基础用法

```cpp
#include <queue>
using namespace std;

// 最大堆（默认）
priority_queue<int> maxHeap;
maxHeap.push(1);          // 入队
int top = maxHeap.top();  // 获取堆顶
maxHeap.pop();            // 出队

// 最小堆
priority_queue<int, vector<int>, greater<int>> minHeap;
```

#### 自定义比较器

**方法1：Lambda表达式**
```cpp
auto cmp = [](int a, int b) { return a > b; };  // 最小堆
priority_queue<int, vector<int>, decltype(cmp)> pq(cmp);
```

**方法2：仿函数**
```cpp
struct Compare {
    bool operator()(ListNode* a, ListNode* b) {
        return a->val > b->val;  // 最小堆
    }
};
priority_queue<ListNode*, vector<ListNode*>, Compare> pq;
```

#### 注意事项

**⚠️ 比较器方向**
```cpp
// ❌ 错误：变成最大堆
return a->val < b->val;

// ✅ 正确：最小堆
return a->val > b->val;  // 注意是大于号！
```

**原因**：`priority_queue`默认最大堆，比较器返回true时，第一个元素优先级**低**

</details>

---

### Level 3：优先队列解法 ⭐⭐⭐

<details>
<summary>点击查看</summary>

#### 算法流程

```
1. 初始化：将k个链表的头节点入堆
2. 循环：
   - 取堆顶（最小节点）
   - 加入结果链表
   - 将该节点的next入堆
3. 直到堆空
```

#### 完整代码

```cpp
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        // 1. 定义最小堆
        auto cmp = [](ListNode* a, ListNode* b) {
            return a->val > b->val;
        };
        priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> minHeap(cmp);
        
        // 2. 初始化：所有头节点入堆
        for (ListNode* head : lists) {
            if (head) {  // 注意空链表
                minHeap.push(head);
            }
        }
        
        // 3. 哑节点
        ListNode dummy(0);
        ListNode* tail = &dummy;
        
        // 4. 主循环
        while (!minHeap.empty()) {
            // 取最小节点
            ListNode* node = minHeap.top();
            minHeap.pop();
            
            // 加入结果
            tail->next = node;
            tail = tail->next;
            
            // 下一个节点入堆
            if (node->next) {
                minHeap.push(node->next);
            }
        }
        
        return dummy.next;
    }
};
```

#### 关键C++特性

**1. Lambda表达式**
```cpp
auto cmp = [捕获列表](参数列表) { 函数体 };

// 本题
auto cmp = [](ListNode* a, ListNode* b) {
    return a->val > b->val;
};
```

**2. decltype**
```cpp
// 推导cmp的类型
decltype(cmp)

// 等价于手写类型（但太长）
function<bool(ListNode*, ListNode*)>
```

**3. Range-based for**
```cpp
// C++11风格
for (ListNode* head : lists) {
    // ...
}

// 旧风格
for (int i = 0; i < lists.size(); i++) {
    ListNode* head = lists[i];
    // ...
}
```

#### 执行过程

```cpp
lists = [[1,4,5], [1,3,4], [2,6]]

初始堆：[1(L0), 1(L1), 2(L2)]

步骤1：取1(L0)，4入堆
堆：[1(L1), 2(L2), 4(L0)]
结果：1

步骤2：取1(L1)，3入堆
堆：[2(L2), 3(L1), 4(L0)]
结果：1 → 1

步骤3：取2(L2)，6入堆
堆：[3(L1), 4(L0), 6(L2)]
结果：1 → 1 → 2

...
```

</details>

---

### Level 4：分治解法 ⭐⭐⭐⭐

<details>
<summary>点击查看</summary>

#### 算法思想

**归并排序的思想**：
```
合并K个 = 合并(前K/2个, 后K/2个)
递归处理，直到只剩1个
```

#### 递归版本

```cpp
class Solution {
public:
    // 合并两个链表
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (!l1) return l2;
        if (!l2) return l1;
        
        if (l1->val < l2->val) {
            l1->next = mergeTwoLists(l1->next, l2);
            return l1;
        } else {
            l2->next = mergeTwoLists(l1, l2->next);
            return l2;
        }
    }
    
    // 分治合并
    ListNode* merge(vector<ListNode*>& lists, int left, int right) {
        if (left == right) return lists[left];
        if (left > right) return nullptr;
        
        int mid = left + (right - left) / 2;
        ListNode* l1 = merge(lists, left, mid);
        ListNode* l2 = merge(lists, mid + 1, right);
        return mergeTwoLists(l1, l2);
    }
    
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if (lists.empty()) return nullptr;
        return merge(lists, 0, lists.size() - 1);
    }
};
```

#### 迭代版本（更优）

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode dummy(0);
        ListNode* tail = &dummy;
        
        while (l1 && l2) {
            if (l1->val < l2->val) {
                tail->next = l1;
                l1 = l1->next;
            } else {
                tail->next = l2;
                l2 = l2->next;
            }
            tail = tail->next;
        }
        
        tail->next = l1 ? l1 : l2;
        return dummy.next;
    }
    
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if (lists.empty()) return nullptr;
        
        // 两两合并
        while (lists.size() > 1) {
            vector<ListNode*> merged;
            
            for (int i = 0; i < lists.size(); i += 2) {
                ListNode* l1 = lists[i];
                ListNode* l2 = (i + 1 < lists.size()) ? lists[i + 1] : nullptr;
                merged.push_back(mergeTwoLists(l1, l2));
            }
            
            lists = merged;
        }
        
        return lists[0];
    }
};
```

#### 图解过程

```
lists = [L0, L1, L2, L3]

第1轮：
L0 + L1 → M0
L2 + L3 → M1
lists = [M0, M1]

第2轮：
M0 + M1 → 最终结果
```

</details>

---

## 🔨 动手实践

### 任务1：实现两个链表合并
```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    // TODO: 实现
}
```

### 任务2：实现优先队列版本
```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {
    // TODO: 使用优先队列
}
```

### 任务3：测试Lambda表达式
```cpp
// 尝试不同的比较器写法
auto cmp1 = [](ListNode* a, ListNode* b) { return a->val > b->val; };

struct Compare {
    bool operator()(ListNode* a, ListNode* b) {
        return a->val > b->val;
    }
};
```

---

## ✅ 自我检查清单

- [ ] 理解优先队列的原理
- [ ] 掌握Lambda表达式语法
- [ ] 理解decltype的作用
- [ ] 能实现最小堆的自定义比较器
- [ ] 理解分治算法的思想
- [ ] 能独立实现两种解法
- [ ] 知道nullptr和NULL的区别
- [ ] 理解哑节点技巧

---

## 🎓 C++知识总结

### 1. 优先队列模板
```cpp
// 最小堆
auto cmp = [](Type a, Type b) { return a > b; };
priority_queue<Type, vector<Type>, decltype(cmp)> pq(cmp);
```

### 2. Lambda表达式
```cpp
[捕获](参数) { 函数体 }

// 无捕获
[](int a) { return a * 2; }

// 捕获所有局部变量
[=](int a) { return a + x; }
```

### 3. 智能指针（进阶）
```cpp
#include <memory>

unique_ptr<ListNode> head = make_unique<ListNode>(1);
// 自动管理内存
```

---

## 💡 常见错误

### 错误1：堆比较器错误
```cpp
// ❌ 最大堆
return a->val < b->val;

// ✅ 最小堆
return a->val > b->val;
```

### 错误2：忘记检查nullptr
```cpp
// ❌
minHeap.push(lists[i]);

// ✅
if (lists[i]) minHeap.push(lists[i]);
```

### 错误3：内存泄漏
```cpp
// ❌
ListNode* node = new ListNode(1);
// 忘记delete

// ✅ 使用智能指针
auto node = make_unique<ListNode>(1);
```

---

## 📖 学习建议

1. **先理解优先队列**：最重要的数据结构
2. **掌握Lambda表达式**：现代C++必备
3. **手动模拟过程**：画图理解
4. **对比两种解法**：优先队列 vs 分治
5. **练习自定义比较器**：多种写法

完成后查看 `solution.md` 获取完整解答！🎉
