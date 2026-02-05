# 11. 盛最多水的容器

## 📝 题目描述

给定一个长度为 `n` 的整数数组 `height`。有 `n` 条垂直线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])`。

找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

**示例 1：**
```
输入：height = [1,8,6,2,5,4,8,3,7]
输出：49
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。
在此情况下，容器能够容纳水的最大值为 49。
```

**示例 2：**
```
输入：height = [1,1]
输出：1
```

**约束条件：**
- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

---

## 💡 解法一：暴力枚举（会超时）

### 思路
枚举所有可能的两条线的组合，计算面积，取最大值。

### 代码实现
```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int maxWater = 0;
        int n = height.size();
        
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int width = j - i;
                int h = min(height[i], height[j]);
                int area = width * h;
                maxWater = max(maxWater, area);
            }
        }
        
        return maxWater;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n²)
- **空间复杂度**：O(1)

---

## ⭐ 解法二：双指针（最优解）

### 思路

使用两个指针：
- **左指针 `left`**：从数组开头开始
- **右指针 `right`**：从数组末尾开始

**核心观察**：
- 面积 = `宽度 × 高度`
- 宽度 = `right - left`
- 高度 = `min(height[left], height[right])`（木桶短板效应）

**移动策略**：
- 每次移动较矮的那一边
- 因为移动较高的一边不可能得到更大的面积

**为什么移动较矮的一边？**
- 如果移动较高的一边，宽度减小，高度最多不变（受限于另一边）
- 如果移动较矮的一边，虽然宽度减小，但高度可能增加

### 代码实现
```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int left = 0, right = height.size() - 1;
        int maxWater = 0;
        
        while (left < right) {
            // 计算当前面积
            int width = right - left;
            int h = min(height[left], height[right]);
            int area = width * h;
            maxWater = max(maxWater, area);
            
            // 移动较矮的一边
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        
        return maxWater;
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，只需遍历一次
- **空间复杂度**：O(1)

---

## 🎯 核心知识点

### 1. 双指针（对撞指针）

与"快慢指针"不同，这是"对撞指针"：
- 两个指针从两端向中间移动
- 每次移动一个指针
- 直到两指针相遇

### 2. 贪心思想

**关键问题**：每次应该移动哪个指针？

**贪心策略**：移动较矮的一边
- **移动较高的**：宽度-1，高度不变或减小 → 面积必然减小
- **移动较矮的**：宽度-1，但高度可能增加 → 面积有可能增加

### 3. 面积计算

```
面积 = 宽度 × 高度
     = (right - left) × min(height[left], height[right])
```

**木桶短板效应**：容器的高度取决于较短的那条边。

---

## 🔍 图解过程

以 `height = [1,8,6,2,5,4,8,3,7]` 为例：

```
初始状态：
height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
          ↑                       ↑
        left                    right

面积 = (8-0) × min(1,7) = 8 × 1 = 8
height[left]=1 < height[right]=7，移动 left

Step 1：
height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
             ↑                    ↑
           left                 right

面积 = (8-1) × min(8,7) = 7 × 7 = 49 ✓ 最大
height[left]=8 > height[right]=7，移动 right

Step 2：
height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
             ↑                 ↑
           left              right

面积 = (7-1) × min(8,3) = 6 × 3 = 18
height[left]=8 > height[right]=3，移动 right

... 继续直到 left >= right

最大面积：49
```

---

## 🎓 证明正确性

**反证法**：假设最优解在 `(i, j)` 且 `height[i] < height[j]`

如果按照我们的算法，当指针在 `(0, n-1)` 时：
- 如果 `i > 0`，说明我们移动了左指针跳过了 `(0, j)`
  - 但 `(0, j)` 的面积 ≤ `(i, j)` 的面积（宽度更大，高度受限于 `height[0]` 或 `height[j]`）
  - 所以跳过 `(0, j)` 是合理的

**结论**：我们的算法不会错过最优解。

---

## 📌 记忆要点

- **模式**：在有序或两端查找 → 双指针（对撞指针）
- **策略**：移动较小的一边
- **原因**：移动较大的一边必然导致面积减小
- **复杂度**：O(n) 时间，O(1) 空间

---

## 🔨 常见错误

### 错误1：移动错误的指针
```cpp
// ❌ 错误：移动较高的一边
if (height[left] > height[right]) {
    left++;
} else {
    right--;
}
```

### 错误2：相等时的处理
```cpp
// 当 height[left] == height[right] 时，移动哪边都可以
// 但要确保移动其中一边
if (height[left] < height[right]) {
    left++;
} else {
    right--;  // 包含了相等的情况
}
```

---

## 🎓 相似题目

### 1. 接雨水（LeetCode 42）
给定一个数组表示高度图，计算能接多少雨水。

**区别**：
- 盛水容器：只考虑两端
- 接雨水：考虑所有柱子之间的凹陷

### 2. 三数之和（LeetCode 15）
同样使用对撞指针，但配合外层循环。

---

**标签**：`数组` `双指针` `贪心` `medium`
