# 1. 两数之和

## 📝 题目描述

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出和为目标值 `target` 的那两个整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

**示例 1：**
```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9，返回 [0, 1]
```

**示例 2：**
```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例 3：**
```
输入：nums = [3,3], target = 6
输出：[0,1]
```

**约束条件：**
- `2 <= nums.length <= 10^4`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`
- 只会存在一个有效答案

---

## 💡 解法一：暴力枚举（不推荐）

### 思路
使用双层循环，枚举所有可能的两个数的组合，判断和是否等于 target。

### 代码实现
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (nums[i] + nums[j] == target) {
                    return {i, j};
                }
            }
        }
        return {};
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n²)，双层循环
- **空间复杂度**：O(1)，只使用常量额外空间

### 缺点
当数组很大时效率极低，不适合实际应用。

---

## ⭐ 解法二：哈希表（最优解）

### 思路
遍历数组的同时，使用哈希表记录已经遍历过的元素及其下标。对于当前元素 `nums[i]`，计算 `complement = target - nums[i]`，然后在哈希表中查找 `complement` 是否存在。

**关键点：**
- 一次遍历同时完成"查找"和"记录"
- 利用哈希表 O(1) 的查找时间

### 代码实现
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> hashMap; // 值 -> 下标
        
        for (int i = 0; i < nums.size(); i++) {
            int complement = target - nums[i];
            
            // 查找互补数是否已存在
            if (hashMap.find(complement) != hashMap.end()) {
                return {hashMap[complement], i};
            }
            
            // 记录当前元素
            hashMap[nums[i]] = i;
        }
        
        return {};
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n)，只需遍历一次数组
- **空间复杂度**：O(n)，哈希表最多存储 n 个元素

### 为什么比暴力法快？
- 暴力法：对每个元素都要遍历后续所有元素 → O(n²)
- 哈希表：对每个元素只需 O(1) 查找互补数 → O(n)

---

## 🎯 核心知识点

### 1. 哈希表的使用
- **C++ 中的哈希表**：`unordered_map<key, value>`
- **常用操作**：
  ```cpp
  // 插入/更新
  hashMap[key] = value;
  
  // 查找
  if (hashMap.find(key) != hashMap.end()) { ... }
  // 或者
  if (hashMap.count(key)) { ... }
  
  // 访问
  int val = hashMap[key];
  ```

### 2. 以空间换时间
这道题是经典的"以空间换时间"思想：
- 暴力法：时间 O(n²)，空间 O(1)
- 哈希法：时间 O(n)，空间 O(n)

### 3. 互补思想
不直接找两个数，而是：
1. 固定一个数 `nums[i]`
2. 计算互补数 `complement = target - nums[i]`
3. 查找 `complement` 是否存在

---

## 🔍 常见错误

### 错误1：重复使用同一元素
```cpp
// 错误示例
if (hashMap.count(nums[i])) {  // 当前元素可能和自己配对
    return {hashMap[nums[i]], i};
}
hashMap[nums[i]] = i;
```

**解决**：先查找互补数，再存入当前元素。

### 错误2：忘记处理重复元素
```
输入：nums = [3, 3], target = 6
```
上面的正确实现可以处理这种情况，因为：
- 遍历到第一个 3 时，哈希表为空，没有互补数，存入 `{3: 0}`
- 遍历到第二个 3 时，查找到互补数 3，返回 `{0, 1}`

---

## 🎓 变式题目

1. **三数之和**（LeetCode 15）：扩展到找三个数
2. **四数之和**（LeetCode 18）：扩展到找四个数
3. **两数之和 II**（LeetCode 167）：输入数组已排序，可用双指针

---

## 📌 记忆要点

- **模式**：查找配对问题 → 哈希表
- **技巧**：边遍历边查找，避免重复
- **复杂度**：O(n) 时间，O(n) 空间
- **适用场景**：无序数组的快速查找

---

**标签**：`数组` `哈希表` `easy`
