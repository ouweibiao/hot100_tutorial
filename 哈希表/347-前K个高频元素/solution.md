# 347. 前 K 个高频元素 - C++ 题解

## 方法一：小顶堆 (Priority Queue)

利用 C++ STL 的 `priority_queue`，配合自定义比较函数（或 pair 的默认比较）。

```cpp
#include <vector>
#include <unordered_map>
#include <queue>

using namespace std;

class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        // 1. 统计频率
        unordered_map<int, int> countMap;
        for (int num : nums) {
            countMap[num]++;
        }

        // 2. 定义小顶堆
        // 堆中存放 pair<频率, 元素>
        // priority_queue 默认是大顶堆，所以需要 greater
        // pair 默认先比较 first (频率)，所以可以直接用
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;

        // 3. 遍历哈希表
        for (auto& it : countMap) {
            pq.push({it.second, it.first}); // first是频率，second是元素
            if (pq.size() > k) {
                pq.pop(); // 弹出频率最小的
            }
        }

        // 4. 取出结果
        vector<int> result;
        while (!pq.empty()) {
            result.push_back(pq.top().second);
            pq.pop();
        }
        return result;
    }
};
```

---

## 方法二：桶排序 (O(n) 时间复杂度)

```cpp
#include <vector>
#include <unordered_map>

using namespace std;

class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        // 1. 统计频率
        unordered_map<int, int> countMap;
        for (int num : nums) {
            countMap[num]++;
        }

        // 2. 桶排序
        // 频率作为下标，范围是 [0, n]
        int n = nums.size();
        vector<vector<int>> buckets(n + 1);
        for (auto& it : countMap) {
            int freq = it.second;
            int num = it.first;
            buckets[freq].push_back(num);
        }

        // 3. 从高频到低频收集结果
        vector<int> result;
        for (int i = n; i >= 0 && result.size() < k; --i) {
            if (!buckets[i].empty()) {
                for (int num : buckets[i]) {
                    result.push_back(num);
                    if (result.size() == k) break;
                }
            }
        }
        return result;
    }
};
```
