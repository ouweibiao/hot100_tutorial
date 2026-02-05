# 295. 数据流的中位数

## 题目描述

**中位数**是有序整数列表中间的那个数。如果列表的大小是偶数，则没有中间的数，中位数是两个中间数的平均值。

例如 `arr = [2,3,4]` 的中位数是 `3` 。
例如 `arr = [2,3]` 的中位数是 `(2 + 3) / 2 = 2.5` 。

实现 `MedianFinder` 类:
- `void addNum(int num)` 将数据流中的整数 `num` 添加到数据结构中。
- `double findMedian()` 返回到目前为止所有元素的中位数。

**示例 1：**
```
输入
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]
输出
[null, null, null, 1.5, null, 2.0]
```

---

## 解法：双堆（对顶堆）

### 核心思路
我们需要实时维护一个有序序列，并快速找到中间位置。
我们可以把所有数字分成两半：
1. **较小的一半**：放在一个**大顶堆**（maxHeap）里。堆顶是这一半的最大值（靠近中间）。
2. **较大的一半**：放在一个**小顶堆**（minHeap）里。堆顶是这一半的最小值（靠近中间）。

**平衡策略**：
- 我们可以规定：`maxHeap` 的大小要么等于 `minHeap`，要么比 `minHeap` 多 1。
- 这样，中位数要么是 `maxHeap.top()`，要么是 `(maxHeap.top() + minHeap.top()) / 2.0`。

**添加数字 `num` 的流程**：
1. 先把 `num` 放入 `maxHeap`。
2. 把 `maxHeap` 的堆顶弹出，放入 `minHeap`（确保 `maxHeap` 里的数都比 `minHeap` 小）。
3. 如果 `minHeap` 的大小超过了 `maxHeap`，把 `minHeap` 的堆顶弹出，放回 `maxHeap`（维持大小平衡）。

### 代码实现

```cpp
#include <queue>
#include <vector>
using namespace std;

class MedianFinder {
private:
    priority_queue<int> maxHeap; // 存较小的一半
    priority_queue<int, vector<int>, greater<int>> minHeap; // 存较大的一半

public:
    MedianFinder() {

    }
    
    void addNum(int num) {
        // 1. 先放入大顶堆
        maxHeap.push(num);
        
        // 2. 将大顶堆的最大值移到小顶堆，保证顺序性
        minHeap.push(maxHeap.top());
        maxHeap.pop();
        
        // 3. 维持平衡：规定 maxHeap 的大小 >= minHeap 的大小
        if (minHeap.size() > maxHeap.size()) {
            maxHeap.push(minHeap.top());
            minHeap.pop();
        }
    }
    
    double findMedian() {
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.top();
        } else {
            return (maxHeap.top() + minHeap.top()) / 2.0;
        }
    }
};
```

### 复杂度分析
- **时间复杂度**：
  - `addNum`: O(log N)，堆的插入和弹出。
  - `findMedian`: O(1)，直接取堆顶。
- **空间复杂度**：O(N)，存储所有元素。

### 易错点
- 维持两个堆的平衡逻辑要清晰。
- 也可以规定 `minHeap` 比 `maxHeap` 多 1，逻辑类似。
- 注意 `findMedian` 返回的是 `double`。

---

## 总结
- 双堆法是解决“动态中位数”问题的标准解法。
- 这种结构也被称为“对顶堆”。
