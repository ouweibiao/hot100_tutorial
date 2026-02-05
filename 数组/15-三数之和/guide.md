# 15. 三数之和 - 编程指导

## 🎯 学习目标
- 掌握"降维"思想：三数之和 → 两数之和
- 学会排序 + 双指针的组合技巧
- 理解去重的多种策略

---

## 📖 题目回顾

找出数组中所有和为 0 的三元组，不能包含重复的三元组。

**示例**：
```
输入：[-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：暴力法
<details>
<summary>点击查看提示1</summary>

**最简单的方法**：三层循环

```cpp
for (int i = 0; i < n; i++) {
    for (int j = i + 1; j < n; j++) {
        for (int k = j + 1; k < n; k++) {
            if (nums[i] + nums[j] + nums[k] == 0) {
                // 找到一组解
            }
        }
    }
}
```

**时间复杂度**：O(n³)

**问题**：
1. 太慢
2. 如何去重？（如何避免 `[-1,0,1]` 和 `[0,-1,1]` 都被加入）

</details>

---

### 🌟 提示 Level 2：降维 + 排序
<details>
<summary>点击查看提示2</summary>

**核心思想**：三数之和 → 两数之和

**步骤1**：排序数组
```
[-1,0,1,2,-1,-4] → [-4,-1,-1,0,1,2]
```

**步骤2**：固定第一个数，转化为两数之和
```
固定 nums[i] = -1
问题转化为：在后面的数组中找两个数，和为 -(-1) = 1
```

**步骤3**：用双指针解决两数之和
```
left = i + 1, right = n - 1
如果 nums[left] + nums[right] == target：找到解
如果 nums[left] + nums[right] < target：left++
如果 nums[left] + nums[right] > target：right--
```

**时间复杂度**：O(n²)

</details>

---

### 🌟 提示 Level 3：去重策略
<details>
<summary>点击查看提示3</summary>

排序后，相同元素相邻，方便去重。

**去重位置1**：固定的第一个数
```cpp
for (int i = 0; i < n - 2; i++) {
    // 跳过重复的第一个数
    if (i > 0 && nums[i] == nums[i-1]) continue;
    ...
}
```

**为什么是 `i > 0`？**
- 保证第一个元素不被跳过
- 例如 `[-1, -1, 2]`，第一个 `-1` 要保留

**去重位置2和3**：双指针找到解后
```cpp
if (sum == target) {
    result.push_back({nums[i], nums[left], nums[right]});
    
    // 跳过重复的 left
    while (left < right && nums[left] == nums[left+1]) left++;
    // 跳过重复的 right
    while (left < right && nums[right] == nums[right-1]) right--;
    
    left++;
    right--;
}
```

</details>

---

### 🌟 提示 Level 4：完整实现
<details>
<summary>点击查看提示4</summary>

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>> result;
    int n = nums.size();
    
    // 1. 排序
    sort(nums.begin(), nums.end());
    
    // 2. 固定第一个数
    for (int i = 0; i < n - 2; i++) {
        // 剪枝：最小的数 > 0，后面不可能和为 0
        if (nums[i] > 0) break;
        
        // 去重：跳过重复的第一个数
        if (i > 0 && nums[i] == nums[i-1]) continue;
        
        // 3. 双指针找另外两个数
        int left = i + 1, right = n - 1;
        int target = -nums[i];
        
        while (left < right) {
            int sum = nums[left] + nums[right];
            
            if (sum == target) {
                result.push_back({nums[i], nums[left], nums[right]});
                
                // 去重
                while (left < right && nums[left] == nums[left+1]) left++;
                while (left < right && nums[right] == nums[right-1]) right--;
                
                left++;
                right--;
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
    }
    
    return result;
}
```

</details>

---

## 🔨 动手实践

### 任务1：手动模拟
对 `nums = [-2, 0, 1, 1, 2]` 排序后手动模拟：

```
i=0, nums[0]=-2, target=2
  left=1, right=4: ?
  ...

i=1, nums[1]=0, target=0
  ...
```

### 任务2：实现代码
按照提示4的框架独立实现。

### 任务3：测试用例
```cpp
// 用例1：正常情况
nums = [-1,0,1,2,-1,-4]  // 预期: [[-1,-1,2],[-1,0,1]]

// 用例2：全部相同
nums = [0,0,0,0]  // 预期: [[0,0,0]]

// 用例3：无解
nums = [1,2,3]  // 预期: []

// 用例4：有重复
nums = [-2,0,0,2,2]  // 预期: [[-2,0,2]]
```

---

## 🎯 关键技巧

### 1. 降维模式

| 问题 | 降维方式 | 复杂度 |
|------|----------|--------|
| 两数之和 | 哈希表 | O(n) |
| 三数之和 | 固定1个 + 双指针 | O(n²) |
| 四数之和 | 固定2个 + 双指针 | O(n³) |

### 2. 排序的好处

1. **使用双指针**：有序才能对撞
2. **去重容易**：相同元素相邻
3. **剪枝优化**：`nums[i] > 0` 可直接 break

### 3. 去重要点

```cpp
// ✅ 正确的去重
if (i > 0 && nums[i] == nums[i-1]) continue;

// ❌ 错误：会跳过第一个元素
if (nums[i] == nums[i-1]) continue;
```

---

## 🚀 进阶挑战

### 挑战1：四数之和（LeetCode 18）
找四个数的和等于 target。

**提示**：再加一层循环。

### 挑战2：不使用排序
能否在不排序的情况下解决？

**提示**：使用哈希表，但去重会很复杂。

### 挑战3：优化剪枝
添加更多剪枝条件：
```cpp
// 当前最小三数之和 > 0
if (nums[i] + nums[i+1] + nums[i+2] > 0) break;

// 当前数 + 最大两数 < 0
if (nums[i] + nums[n-2] + nums[n-1] < 0) continue;
```

---

## ✅ 自检清单

- [ ] 我理解了降维思想
- [ ] 我知道为什么要先排序
- [ ] 我理解了三个位置的去重逻辑
- [ ] 我知道 `i > 0` 的作用
- [ ] 我能独立实现代码
- [ ] 我理解了时间复杂度 O(n²) 的由来

---

## 📌 口诀

> 三数之和先排序，固定一个找两个。  
> 双指针从两端走，相等找到解一组。  
> 去重三处要记牢，第一个数判 i > 0。  
> 复杂度是 n 平方，面试常考要记好。

---

**下一步**：独立实现并对照 `solution.md`！
