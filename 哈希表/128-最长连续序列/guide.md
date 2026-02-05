# 128. 最长连续序列 - 编程指导

## 🎯 学习目标
- 理解O(n)算法的设计
- 掌握哈希表的高级应用
- 学会避免重复计算

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：排序方法
<details>
<summary>点击查看提示1</summary>

**最简单的方法**：排序后扫描

```cpp
sort(nums.begin(), nums.end());
int maxLength = 1, currentLength = 1;

for (int i = 1; i < nums.size(); i++) {
    if (nums[i] == nums[i-1]) continue;  // 跳过重复
    if (nums[i] == nums[i-1] + 1) {
        currentLength++;
    } else {
        maxLength = max(maxLength, currentLength);
        currentLength = 1;
    }
}
```

**复杂度**：O(n log n)

**问题**：能否优化到 O(n)？

</details>

---

### 🌟 提示 Level 2：哈希表加速
<details>
<summary>点击查看提示2</summary>

**观察**：不需要排序，只需要快速查找

```cpp
unordered_set<int> numSet(nums.begin(), nums.end());

// 对每个数字，向后查找连续序列
for (int num : numSet) {
    int length = 0;
    while (numSet.count(num + length)) {
        length++;
    }
    maxLength = max(maxLength, length);
}
```

**问题**：这样做的复杂度是多少？

**答案**：O(n²)！因为每个数字都会扫描一遍序列。

</details>

---

### 🌟 提示 Level 3：智能扫描
<details>
<summary>点击查看提示3</summary>

**优化思路**：只从序列起点开始扫描

**如何判断起点？**
- 如果 `num-1` 存在，说明 `num` 不是起点
- 如果 `num-1` 不存在，说明 `num` 是起点

```cpp
for (int num : numSet) {
    // 跳过非起点
    if (numSet.count(num - 1)) continue;
    
    // num 是起点，开始扫描
    int currentNum = num;
    int length = 1;
    
    while (numSet.count(currentNum + 1)) {
        currentNum++;
        length++;
    }
    
    maxLength = max(maxLength, length);
}
```

**为什么是 O(n)？**
- 每个数字最多被访问2次
- 一次作为起点扫描
- 一次被其他起点扫描到

</details>

---

### 🌟 提示 Level 4：完整实现
<details>
<summary>点击查看提示4</summary>

```cpp
int longestConsecutive(vector<int>& nums) {
    if (nums.empty()) return 0;
    
    unordered_set<int> numSet(nums.begin(), nums.end());
    int maxLength = 0;
    
    for (int num : numSet) {
        // 只处理序列起点
        if (numSet.count(num - 1)) continue;
        
        int currentNum = num;
        int currentLength = 1;
        
        while (numSet.count(currentNum + 1)) {
            currentNum++;
            currentLength++;
        }
        
        maxLength = max(maxLength, currentLength);
    }
    
    return maxLength;
}
```

</details>

---

## ✅ 自检清单

- [ ] 我理解了为什么排序方法是O(n log n)
- [ ] 我知道为什么只从起点扫描能优化到O(n)
- [ ] 我能独立实现哈希表解法
- [ ] 我理解了每个数字最多被访问2次

---

**下一步**：独立实现并测试！
