# 146. LRU 缓存 - C++ 题解

## 方法一：使用 STL `std::list` + `unordered_map`

这是面试中最推荐的写法，利用 C++ 标准库的特性，代码简洁且高效。

```cpp
#include <list>
#include <unordered_map>
#include <utility>

using namespace std;

class LRUCache {
private:
    int capacity;
    // 双向链表：存储 pair<key, value>
    // list.front() 是最近使用的，list.back() 是最久未使用的
    list<pair<int, int>> cache;
    
    // 哈希表：存储 key -> 链表迭代器
    // 通过 key 可以 O(1) 找到链表中的节点位置
    unordered_map<int, list<pair<int, int>>::iterator> map;

public:
    LRUCache(int capacity) : capacity(capacity) {}

    int get(int key) {
        // 1. 查找 key 是否存在
        if (map.find(key) == map.end()) {
            return -1;
        }

        // 2. 如果存在，将该节点移动到链表头部（标记为最近使用）
        // list::splice 是 O(1) 操作，将 map[key] 指向的节点移动到 cache.begin() 之前
        cache.splice(cache.begin(), cache, map[key]);

        // 3. 返回 value
        return map[key]->second;
    }

    void put(int key, int value) {
        if (map.find(key) != map.end()) {
            // 情况 1: Key 已存在
            // 更新 value
            map[key]->second = value;
            // 移动到头部
            cache.splice(cache.begin(), cache, map[key]);
        } else {
            // 情况 2: Key 不存在
            if (cache.size() == capacity) {
                // 容量已满，删除尾部节点（最久未使用）
                int key_to_del = cache.back().first;
                cache.pop_back();
                map.erase(key_to_del);
            }
            // 插入新节点到头部
            cache.push_front({key, value});
            // 更新哈希表
            map[key] = cache.begin();
        }
    }
};
```

### 复杂度分析
- **时间复杂度**：`get` 和 `put` 均为 O(1)。`unordered_map` 的查找/插入/删除平均是 O(1)，`list` 的 `splice`、`push_front`、`pop_back` 也是 O(1)。
- **空间复杂度**：O(capacity)，用于存储缓存数据。

---

## 方法二：手写双向链表 (面试加分项)

如果面试官要求不能使用 `std::list`，或者想考察对指针操作的熟练度，可以使用此方法。

```cpp
#include <unordered_map>

using namespace std;

// 定义双向链表节点
struct DLinkedNode {
    int key, value;
    DLinkedNode* prev;
    DLinkedNode* next;
    DLinkedNode() : key(0), value(0), prev(nullptr), next(nullptr) {}
    DLinkedNode(int _key, int _value) : key(_key), value(_value), prev(nullptr), next(nullptr) {}
};

class LRUCache {
private:
    unordered_map<int, DLinkedNode*> cache;
    DLinkedNode* head;
    DLinkedNode* tail;
    int size;
    int capacity;

public:
    LRUCache(int capacity) : size(0), capacity(capacity) {
        // 使用伪头部和伪尾部节点，简化边界判断
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head->next = tail;
        tail->prev = head;
    }
    
    // 辅助函数：添加节点到头部
    void addToHead(DLinkedNode* node) {
        node->prev = head;
        node->next = head->next;
        head->next->prev = node;
        head->next = node;
    }
    
    // 辅助函数：删除节点
    void removeNode(DLinkedNode* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }
    
    // 辅助函数：移动节点到头部
    void moveToHead(DLinkedNode* node) {
        removeNode(node);
        addToHead(node);
    }
    
    // 辅助函数：删除尾部节点（返回被删除的节点，以便删除哈希映射）
    DLinkedNode* removeTail() {
        DLinkedNode* res = tail->prev;
        removeNode(res);
        return res;
    }

    int get(int key) {
        if (!cache.count(key)) {
            return -1;
        }
        // 如果 key 存在，先通过哈希表定位，再移动到头部
        DLinkedNode* node = cache[key];
        moveToHead(node);
        return node->value;
    }

    void put(int key, int value) {
        if (!cache.count(key)) {
            // 如果 key 不存在，创建一个新的节点
            DLinkedNode* node = new DLinkedNode(key, value);
            // 添加进哈希表
            cache[key] = node;
            // 添加至双向链表的头部
            addToHead(node);
            ++size;
            if (size > capacity) {
                // 如果超出容量，删除双向链表的尾部节点
                DLinkedNode* removed = removeTail();
                // 删除哈希表中对应的项
                cache.erase(removed->key);
                // 防止内存泄漏
                delete removed;
                --size;
            }
        } else {
            // 如果 key 存在，先通过哈希表定位，再修改 value，并移动到头部
            DLinkedNode* node = cache[key];
            node->value = value;
            moveToHead(node);
        }
    }
    
    // 析构函数：释放内存（面试时如果不写通常也可以，但写了更好）
    ~LRUCache() {
        DLinkedNode* curr = head;
        while (curr) {
            DLinkedNode* next = curr->next;
            delete curr;
            curr = next;
        }
    }
};
```
