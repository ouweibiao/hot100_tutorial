# 283. 移动零 - 编程指导

## 🎯 学习目标
通过这道题，你将掌握：
- 双指针（快慢指针）的经典应用
- 原地修改数组的技巧
- 从简单解法到最优解的优化思路

---

## 📖 题目回顾

将数组中的所有 0 移动到末尾，保持非零元素的相对顺序，且必须原地操作。

**示例**：
```
输入：[0, 1, 0, 3, 12]
输出：[1, 3, 12, 0, 0]
```

---

## 💭 思考提示（分4级，逐步深入）

### 🌟 提示 Level 1：理解约束
<details>
<summary>点击查看提示1</summary>

**问题1**：如果允许使用额外空间，怎么做？

**简单方法**：
1. 创建新数组 `result`
2. 遍历原数组，先放入所有非零元素
3. 再在末尾补充零

```cpp
vector<int> result;
int zeroCount = 0;

for (int num : nums) {
    if (num != 0) {
        result.push_back(num);
    } else {
        zeroCount++;
    }
}

for (int i = 0; i < zeroCount; i++) {
    result.push_back(0);
}
```

**缺点**：使用了 O(n) 额外空间，不符合题目要求！

**问题2**：如何在原数组上操作？

</details>

---

### 🌟 提示 Level 2：朴素原地操作
<details>
<summary>点击查看提示2</summary>

**思路**：两次遍历
1. **第一次遍历**：把所有非零元素移到前面
2. **第二次遍历**：后面的位置填充零

**关键问题**：如何把非零元素移到前面？

**引入一个变量**：`slow` 指向"下一个非零元素应该放的位置"

```cpp
int slow = 0;

// 第一次遍历：移动非零元素
for (int fast = 0; fast < nums.size(); fast++) {
    if (nums[fast] != 0) {
        nums[slow] = nums[fast];
        slow++;
    }
}

// 第二次遍历：填充零
for (int i = slow; i < nums.size(); i++) {
    nums[i] = 0;
}
```

**手动模拟**：`nums = [0, 1, 0, 3, 12]`

```
第一次遍历：
fast=0, nums[0]=0 (是零，跳过)
fast=1, nums[1]=1 (非零): nums[0]=1, slow=1
fast=2, nums[2]=0 (是零，跳过)
fast=3, nums[3]=3 (非零): nums[1]=3, slow=2
fast=4, nums[4]=12 (非零): nums[2]=12, slow=3

第一次遍历后：nums = [1, 3, 12, 3, 12], slow=3

第二次遍历：
nums[3]=0, nums[4]=0

最终：nums = [1, 3, 12, 0, 0]
```

**这就是双指针（快慢指针）的雏形！**

</details>

---

### 🌟 提示 Level 3：优化为一次遍历
<details>
<summary>点击查看提示3</summary>

**观察**：能否把两次遍历合并成一次？

**优化思路**：用交换代替赋值 + 填充

```cpp
int slow = 0;

for (int fast = 0; fast < nums.size(); fast++) {
    if (nums[fast] != 0) {
        swap(nums[slow], nums[fast]);
        slow++;
    }
}
```

**为什么可以用交换？**
- 当 `fast` 找到非零元素时，把它和 `slow` 位置的元素交换
- `slow` 位置要么是 0（需要移到后面），要么是还未处理的元素
- 交换后，`slow` 位置变成非零元素，原位置变成 0 或未处理元素

**手动模拟**：`nums = [0, 1, 0, 3, 12]`

```
初始：slow=0, fast=0

fast=0: nums[0]=0 (是零，跳过)
        slow=0

fast=1: nums[1]=1 (非零)
        swap(nums[0], nums[1]) → [1, 0, 0, 3, 12]
        slow=1

fast=2: nums[2]=0 (是零，跳过)
        slow=1

fast=3: nums[3]=3 (非零)
        swap(nums[1], nums[3]) → [1, 3, 0, 0, 12]
        slow=2

fast=4: nums[4]=12 (非零)
        swap(nums[2], nums[4]) → [1, 3, 12, 0, 0]
        slow=3

完成！
```

**一次遍历，原地操作，完美！**

</details>

---

### 🌟 提示 Level 4：细节优化
<details>
<summary>点击查看提示4</summary>

**问题**：考虑这个情况：`nums = [1, 2, 3]`（没有零）

```
fast=0, slow=0: swap(nums[0], nums[0])  // 自己和自己交换
fast=1, slow=1: swap(nums[1], nums[1])
fast=2, slow=2: swap(nums[2], nums[2])
```

虽然结果正确，但进行了无意义的交换操作。

**优化**：只在 `slow != fast` 时才交换。

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int slow = 0;
        
        for (int fast = 0; fast < nums.size(); fast++) {
            if (nums[fast] != 0) {
                if (slow != fast) {  // 避免自己和自己交换
                    swap(nums[slow], nums[fast]);
                }
                slow++;
            }
        }
    }
};
```

**完整优化版本完成！**

</details>

---

## 🔨 动手实践

### 任务1：实现两次遍历版本
```cpp
void moveZeroes(vector<int>& nums) {
    // TODO: 实现第一次遍历（移动非零元素）
    
    // TODO: 实现第二次遍历（填充零）
}
```

### 任务2：实现一次遍历 + 交换版本
```cpp
void moveZeroes(vector<int>& nums) {
    // TODO: 使用快慢指针 + swap
}
```

### 任务3：手动模拟
对数组 `[0, 0, 1]`，手动模拟快慢指针的变化过程。

### 任务4：测试用例
```cpp
// 用例1：正常情况
nums = [0, 1, 0, 3, 12]  // 预期: [1, 3, 12, 0, 0]

// 用例2：没有零
nums = [1, 2, 3]  // 预期: [1, 2, 3]

// 用例3：全是零
nums = [0, 0, 0]  // 预期: [0, 0, 0]

// 用例4：单个元素
nums = [0]  // 预期: [0]

// 用例5：零在末尾
nums = [1, 2, 0]  // 预期: [1, 2, 0]
```

---

## 🎯 双指针模板

### 快慢指针（原地修改数组）
```cpp
int slow = 0;  // 慢指针：指向写入位置

for (int fast = 0; fast < nums.size(); fast++) {  // 快指针：遍历数组
    if (满足条件(nums[fast])) {
        处理(nums[slow], nums[fast]);  // 赋值或交换
        slow++;
    }
}
```

**应用场景**：
- 移动元素（本题）
- 删除元素（LeetCode 27）
- 去重（LeetCode 26）
- 分割数组（LeetCode 75）

---

## 🚀 进阶挑战

### 挑战1：删除数组中的元素（LeetCode 27）
给定 `nums` 和 `val`，原地删除所有等于 `val` 的元素，返回新长度。

**提示**：和本题几乎一样，只是条件从 `!= 0` 变成 `!= val`。

### 挑战2：颜色分类（LeetCode 75）
数组只包含 `0, 1, 2`，原地排序使得相同数字相邻。

**提示**：需要两个慢指针，分别指向 0 和 1 的下一个位置。

### 挑战3：优化交换次数
在本题基础上，统计总共进行了多少次交换操作。

---

## ✅ 自检清单

- [ ] 我理解了为什么不能使用额外数组
- [ ] 我知道什么是快慢指针
- [ ] 我理解了慢指针的含义：下一个非零元素应该放的位置
- [ ] 我能手动模拟快慢指针的移动过程
- [ ] 我理解了为什么可以用交换代替两次遍历
- [ ] 我知道为什么要判断 `slow != fast`
- [ ] 我能独立实现一次遍历 + 交换的版本

---

## 📌 核心要点

**口诀**：
> 原地修改数组题，快慢指针是利器。  
> 慢指针指向写入处，快指针遍历找元素。  
> 满足条件就处理，交换赋值看需求。  
> 一次遍历效率高，空间复杂度 O(1)。

**双指针的本质**：
- **分离读写操作**：快指针负责读，慢指针负责写
- **原地修改**：不需要额外空间
- **保持相对顺序**：慢指针按顺序写入

---

**下一步**：独立实现代码，然后对照 `solution.md` 验证！
