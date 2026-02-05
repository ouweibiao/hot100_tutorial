# 56. 合并区间 - 编程指导

## 🎯 学习目标
- 掌握区间问题的处理技巧
- 理解排序在区间问题中的作用
- 学会判断区间重叠

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：理解重叠
<details>
<summary>点击查看提示1</summary>

**什么是重叠？**

```
[1,3] 和 [2,6]：重叠 ✓
   1---3
     2-----6
合并：1-------6

[1,3] 和 [5,7]：不重叠 ✗
   1---3   5---7
```

**重叠条件**：
```
区间1: [a, b]
区间2: [c, d]
重叠: c <= b
```

</details>

---

### 🌟 提示 Level 2：排序的作用
<details>
<summary>点击查看提示2</summary>

**为什么要排序？**

```
未排序：[[8,10],[1,3],[2,6],[15,18]]
难以判断 [1,3] 和 [2,6] 应该合并

排序后：[[1,3],[2,6],[8,10],[15,18]]
相邻区间依次比较即可
```

**排序规则**：按起始位置升序。

</details>

---

### 🌟 提示 Level 3：合并策略
<details>
<summary>点击查看提示3</summary>

```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    // 1. 排序
    sort(intervals.begin(), intervals.end());
    
    vector<vector<int>> result;
    result.push_back(intervals[0]);
    
    // 2. 遍历
    for (int i = 1; i < intervals.size(); i++) {
        int start = intervals[i][0];
        int end = intervals[i][1];
        int lastEnd = result.back()[1];
        
        if (start <= lastEnd) {
            // 重叠，合并
            result.back()[1] = max(lastEnd, end);
        } else {
            // 不重叠，加入
            result.push_back(intervals[i]);
        }
    }
    
    return result;
}
```

</details>

---

### 🌟 提示 Level 4：边界情况
<details>
<summary>点击查看提示4</summary>

**边界1**：完全包含
```
[1,6] + [2,3]
合并：[1, max(6,3)] = [1,6]
```

**边界2**：端点相接
```
[1,3] + [3,5]
3 <= 3，重叠
合并：[1,5]
```

**边界3**：单个区间
```
intervals = [[1,4]]
直接返回
```

</details>

---

## ✅ 自检清单

- [ ] 我理解了区间重叠的条件
- [ ] 我知道为什么要排序
- [ ] 我能独立实现合并逻辑
- [ ] 我处理了边界情况

---

**下一步**：独立实现并测试！
