# 23. 合并K个升序链表

## 题目描述

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

### 示例

**示例 1：**
```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例 2：**
```
输入：lists = []
输出：[]
```

**示例 3：**
```
输入：lists = [[]]
输出：[]
```

**提示：**
- `k == lists.length`
- `0 <= k <= 10^4`
- `0 <= lists[i].length <= 500`
- `-10^4 <= lists[i][j] <= 10^4`
- `lists[i]` 按**升序**排列
- `lists[i].length` 的总和不超过 `10^4`

---

## C++基础知识

### 1. 链表定义
```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};
```

**C++知识点**：
- `struct`：结构体，成员默认public
- `nullptr`：C++11空指针常量（优于NULL）
- 构造函数初始化列表：`: val(0), next(nullptr)`

### 2. 向量（vector）
```cpp
#include <vector>
using namespace std;

vector<ListNode*> lists;  // 动态数组
lists.size();             // 获取大小
lists[i];                 // 访问元素
```

### 3. 优先队列（priority_queue）
```cpp
#include <queue>

// 最小堆
priority_queue<int, vector<int>, greater<int>> minHeap;
minHeap.push(1);          // 入队
int top = minHeap.top();  // 获取堆顶
minHeap.pop();            // 出队
```

---

## 解法一：暴力法

### 核心思路

1. 遍历所有链表，收集所有节点的值
2. 对所有值排序
3. 创建新链表

### 代码实现

```cpp
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        // 收集所有值
        vector<int> values;
        for (ListNode* head : lists) {
            ListNode* curr = head;
            while (curr) {
                values.push_back(curr->val);
                curr = curr->next;
            }
        }
        
        // 排序
        sort(values.begin(), values.end());
        
        // 构建新链表
        ListNode dummy(0);
        ListNode* curr = &dummy;
        
        for (int val : values) {
            curr->next = new ListNode(val);
            curr = curr->next;
        }
        
        return dummy.next;
    }
};
```

**C++知识点**：
- `range-based for`：`for (ListNode* head : lists)`
- `vector::push_back()`：尾部插入
- `sort()`：标准库排序算法
- `new`：动态分配内存
- `dummy node`：哑节点技巧

### 复杂度分析
- **时间复杂度**：O(N log N)
  - N是所有节点总数
  - 排序主导
- **空间复杂度**：O(N)
  - 存储所有值

---

## 解法二：优先队列（堆）⭐

### 核心思路

使用**最小堆**维护k个链表的当前最小节点。

**算法流程**：
1. 将k个链表的头节点入堆
2. 每次取堆顶（最小节点）加入结果
3. 将该节点的下一个节点入堆
4. 重复直到堆空

### 图解过程

```
lists = [[1,4,5], [1,3,4], [2,6]]

步骤1：初始化堆
堆：[1(链表0), 1(链表1), 2(链表2)]
结果：dummy

步骤2：取1(链表0)
堆：[1(链表1), 2(链表2), 4(链表0)]
结果：dummy -> 1

步骤3：取1(链表1)
堆：[2(链表2), 4(链表0), 3(链表1)]
结果：dummy -> 1 -> 1

步骤4：取2(链表2)
堆：[3(链表1), 4(链表0), 6(链表2)]
结果：dummy -> 1 -> 1 -> 2

步骤5：取3(链表1)
堆：[4(链表0), 4(链表1), 6(链表2)]
结果：dummy -> 1 -> 1 -> 2 -> 3

...
```

### 代码实现

```cpp
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        // 自定义比较器（最小堆）
        auto cmp = [](ListNode* a, ListNode* b) {
            return a->val > b->val;  // 注意：大于号表示最小堆
        };
        
        // 优先队列
        priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> minHeap(cmp);
        
        // 将所有链表的头节点入堆
        for (ListNode* head : lists) {
            if (head) {  // 注意检查空链表
                minHeap.push(head);
            }
        }
        
        // 哑节点
        ListNode dummy(0);
        ListNode* tail = &dummy;
        
        // 不断取最小节点
        while (!minHeap.empty()) {
            ListNode* node = minHeap.top();
            minHeap.pop();
            
            // 加入结果链表
            tail->next = node;
            tail = tail->next;
            
            // 将该节点的下一个节点入堆
            if (node->next) {
                minHeap.push(node->next);
            }
        }
        
        return dummy.next;
    }
};
```

**C++知识点**：
1. **Lambda表达式**：`auto cmp = [](ListNode* a, ListNode* b) { ... }`
2. **decltype**：推导类型
3. **优先队列**：`priority_queue<类型, 容器, 比较器>`
4. **取地址**：`&dummy` 获取栈对象地址
5. **指针操作**：`tail->next`

### 复杂度分析
- **时间复杂度**：O(N log k)
  - N是总节点数
  - 每次堆操作O(log k)
  - 共N次操作
- **空间复杂度**：O(k)
  - 堆中最多k个节点

---

## 解法三：分治合并（最优）⭐⭐

### 核心思路

利用**归并排序**的思想：
1. 两两合并链表
2. 递归处理，直到只剩一个链表

### 图解

```
lists = [L0, L1, L2, L3, L4, L5, L6, L7]

第1轮：两两合并
[L0+L1, L2+L3, L4+L5, L6+L7]

第2轮：两两合并
[L0+L1+L2+L3, L4+L5+L6+L7]

第3轮：两两合并
[L0+L1+L2+L3+L4+L5+L6+L7]

完成！
```

### 代码实现

```cpp
class Solution {
public:
    // 合并两个有序链表
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
        if (left == right) {
            return lists[left];
        }
        if (left > right) {
            return nullptr;
        }
        
        int mid = left + (right - left) / 2;
        ListNode* l1 = merge(lists, left, mid);
        ListNode* l2 = merge(lists, mid + 1, right);
        return mergeTwoLists(l1, l2);
    }
    
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if (lists.empty()) {
            return nullptr;
        }
        return merge(lists, 0, lists.size() - 1);
    }
};
```

**C++知识点**：
- **递归**：函数调用自身
- **成员函数**：类内定义多个函数
- **引用传递**：`vector<ListNode*>&` 避免拷贝
- **三元运算符**：可用于简化if-else

### 迭代版本（更高效）

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

### 复杂度分析
- **时间复杂度**：O(N log k)
  - 每轮合并：O(N)
  - 共log k轮
- **空间复杂度**：O(1)
  - 不计递归栈
  - 递归版本：O(log k)

---

## 三种方法对比

| 方法 | 时间复杂度 | 空间复杂度 | 优点 | 缺点 |
|------|-----------|-----------|------|------|
| 暴力排序 | O(N log N) | O(N) | 简单 | 浪费空间 |
| 优先队列 | O(N log k) | O(k) | 高效 | 需要堆 |
| 分治合并 | O(N log k) | O(1) | 最优 | 略复杂 |

**推荐**：
- **面试首选**：优先队列（代码清晰）
- **追求极致**：分治合并（最优解）

---

## C++特性总结

### 1. 智能指针（可选优化）

```cpp
#include <memory>

// 使用unique_ptr管理内存
unique_ptr<ListNode> head(new ListNode(1));
```

### 2. 自定义比较器的多种写法

```cpp
// 方法1：Lambda表达式
auto cmp = [](ListNode* a, ListNode* b) { return a->val > b->val; };

// 方法2：仿函数
struct Compare {
    bool operator()(ListNode* a, ListNode* b) {
        return a->val > b->val;
    }
};
priority_queue<ListNode*, vector<ListNode*>, Compare> minHeap;

// 方法3：函数指针
bool cmp(ListNode* a, ListNode* b) { return a->val > b->val; }
```

### 3. 范围for循环

```cpp
// C++11风格
for (ListNode* head : lists) {
    // ...
}

// 引用避免拷贝
for (const auto& head : lists) {
    // ...
}
```

### 4. nullptr vs NULL

```cpp
// C++11推荐
ListNode* p = nullptr;  // 类型安全

// 旧风格（不推荐）
ListNode* p = NULL;     // 可能引起歧义
```

---

## 常见错误

### 错误1：忘记检查空链表
```cpp
// ❌ 错误
for (ListNode* head : lists) {
    minHeap.push(head);  // head可能为nullptr
}

// ✅ 正确
for (ListNode* head : lists) {
    if (head) {
        minHeap.push(head);
    }
}
```

### 错误2：堆比较器方向错误
```cpp
// ❌ 错误（变成最大堆）
auto cmp = [](ListNode* a, ListNode* b) { return a->val < b->val; };

// ✅ 正确（最小堆）
auto cmp = [](ListNode* a, ListNode* b) { return a->val > b->val; };
```

### 错误3：内存泄漏
```cpp
// ❌ 可能泄漏
ListNode* node = new ListNode(1);
// 忘记delete

// ✅ 使用智能指针
unique_ptr<ListNode> node = make_unique<ListNode>(1);
```

---

## 实战应用

### 场景1：多路归并排序
```cpp
// 合并多个有序数组
vector<int> mergeKArrays(vector<vector<int>>& arrays) {
    auto cmp = [](pair<int, pair<int,int>> a, 
                  pair<int, pair<int,int>> b) {
        return a.first > b.first;
    };
    
    priority_queue<pair<int, pair<int,int>>, 
                   vector<pair<int, pair<int,int>>>,
                   decltype(cmp)> minHeap(cmp);
    
    // 初始化堆
    for (int i = 0; i < arrays.size(); i++) {
        if (!arrays[i].empty()) {
            minHeap.push({arrays[i][0], {i, 0}});
        }
    }
    
    vector<int> result;
    while (!minHeap.empty()) {
        auto [val, pos] = minHeap.top();
        auto [arrIdx, idx] = pos;
        minHeap.pop();
        
        result.push_back(val);
        
        if (idx + 1 < arrays[arrIdx].size()) {
            minHeap.push({arrays[arrIdx][idx+1], {arrIdx, idx+1}});
        }
    }
    
    return result;
}
```

---

## 总结

### 核心要点
1. ✅ **优先队列是最常用解法**（O(N log k)）
2. ✅ **分治合并空间最优**（O(1)）
3. ✅ **掌握Lambda表达式和自定义比较器**
4. ✅ **注意空链表的边界处理**

### C++算法模板

**优先队列版本**：
```cpp
auto cmp = [](ListNode* a, ListNode* b) { return a->val > b->val; };
priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> minHeap(cmp);

for (ListNode* head : lists) {
    if (head) minHeap.push(head);
}

ListNode dummy(0);
ListNode* tail = &dummy;

while (!minHeap.empty()) {
    ListNode* node = minHeap.top();
    minHeap.pop();
    tail->next = node;
    tail = tail->next;
    if (node->next) minHeap.push(node->next);
}

return dummy.next;
```

这道题是**链表**和**优先队列**的综合应用，必须熟练掌握！
