# 1. 两数之和 - 编程指导

## 🎯 学习目标
通过这道题，你将掌握：
- 哈希表的实际应用场景
- 如何从暴力解法优化到最优解
- "以空间换时间"的算法思维

---

## 📖 题目回顾

给定数组 `nums` 和目标值 `target`，找出两个数的下标，使得这两个数的和等于 `target`。

**示例**：
```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
```

---

## 💭 思考提示（分4级，逐步深入）

### 🌟 提示 Level 1：基础思路
<details>
<summary>点击查看提示1</summary>

**问题1**：最简单的方法是什么？

如果不考虑效率，你会怎么找这两个数？

**思考方向**：
- 可以枚举所有的数对吗？
- 需要用几层循环？

</details>

---

### 🌟 提示 Level 2：发现瓶颈
<details>
<summary>点击查看提示2</summary>

暴力法的代码框架：
```cpp
for (int i = 0; i < n; i++) {
    for (int j = i + 1; j < n; j++) {
        if (nums[i] + nums[j] == target) {
            return {i, j};
        }
    }
}
```

**问题2**：这个方法的时间复杂度是多少？

**思考**：
- 外层循环 n 次，内层循环平均 n/2 次
- 总共需要 n × n/2 ≈ n² 次操作
- 当 n = 10000 时，需要 1 亿次操作！

**关键问题**：内层循环在做什么？
- 在查找 `target - nums[i]` 是否存在
- 这个查找操作能否优化？

</details>

---

### 🌟 提示 Level 3：优化方向
<details>
<summary>点击查看提示3</summary>

**核心思想**：把"查找"操作从 O(n) 优化到 O(1)

**问题3**：什么数据结构可以实现 O(1) 查找？

**答案**：哈希表（Hash Table）

**C++ 中的哈希表**：
- `unordered_map<key, value>` → 键值对存储
- `unordered_set<key>` → 只存储键

**在这道题中**：
- **Key**：数组元素的值
- **Value**：数组元素的下标

**算法框架**：
```cpp
unordered_map<int, int> hashMap; // 值 -> 下标

for (int i = 0; i < nums.size(); i++) {
    int complement = target - nums[i];
    
    // 在哈希表中查找互补数
    if (hashMap中存在complement) {
        return {找到的下标, i};
    }
    
    // 把当前元素存入哈希表
    hashMap[nums[i]] = i;
}
```

**思考**：为什么要先查找再存入？

</details>

---

### 🌟 提示 Level 4：完整实现
<details>
<summary>点击查看提示4</summary>

**关键点1**：哈希表的查找语法
```cpp
// 方法1：使用 find
if (hashMap.find(key) != hashMap.end()) {
    // key 存在
}

// 方法2：使用 count（更简洁）
if (hashMap.count(key)) {
    // key 存在
}
```

**关键点2**：先查找，后存入
```cpp
// ✅ 正确
if (hashMap.count(complement)) {
    return {hashMap[complement], i};
}
hashMap[nums[i]] = i;

// ❌ 错误（可能让一个元素和自己配对）
hashMap[nums[i]] = i;
if (hashMap.count(complement)) {
    return {hashMap[complement], i};
}
```

**完整代码**：
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> hashMap;
        
        for (int i = 0; i < nums.size(); i++) {
            int complement = target - nums[i];
            
            if (hashMap.find(complement) != hashMap.end()) {
                return {hashMap[complement], i};
            }
            
            hashMap[nums[i]] = i;
        }
        
        return {};
    }
};
```

</details>

---

## 🔨 动手实践

### 任务1：实现暴力解法
先写出暴力解法，确保理解问题。

### 任务2：优化为哈希表解法
按照提示3的框架，实现哈希表版本。

### 任务3：测试边界情况
测试以下用例：
```cpp
// 用例1：正常情况
nums = [2, 7, 11, 15], target = 9  // 预期: [0, 1]

// 用例2：重复元素
nums = [3, 3], target = 6  // 预期: [0, 1]

// 用例3：负数
nums = [-1, -2, -3, -4, -5], target = -8  // 预期: [2, 4]
```

---

## 🎯 核心技巧总结

### 1. 互补思想
不直接找两个数，而是：
- 固定一个数 `a`
- 找它的互补数 `b = target - a`

### 2. 哈希表加速查找
- **传统方法**：在数组中查找 → O(n)
- **哈希表**：直接查找 → O(1)

### 3. 遍历顺序
- 边遍历边查找边存储
- 先查找互补数，再存入当前数

---

## 📊 复杂度对比

| 方法 | 时间复杂度 | 空间复杂度 | 适用场景 |
|------|------------|------------|----------|
| 暴力枚举 | O(n²) | O(1) | 数据量小 |
| 哈希表 | O(n) | O(n) | 推荐使用 |

---

## 🚀 进阶挑战

### 挑战1：如果数组已排序？
输入：`nums = [2, 7, 11, 15]`（已排序），`target = 9`

可以使用**双指针**代替哈希表：
- 左指针指向开头，右指针指向结尾
- 根据 `nums[left] + nums[right]` 与 `target` 的关系移动指针

### 挑战2：找出所有满足条件的数对？
返回所有和为 `target` 的数对（不是下标）。

---

## ✅ 自检清单

在查看完整答案前，确认：
- [ ] 我理解了暴力法的瓶颈在哪里
- [ ] 我知道为什么要用哈希表
- [ ] 我能独立写出哈希表的代码
- [ ] 我理解"先查找后存入"的原因
- [ ] 我测试了边界情况

---

**下一步**：独立编码实现，然后对照 `solution.md` 的标准答案！
