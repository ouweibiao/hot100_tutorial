# 198. 打家劫舍 - 编程指导

## 🎯 学习目标
- 理解带约束的最优化问题
- 掌握动态规划的决策过程
- 学会空间优化技巧

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：理解约束
<details>
<summary>点击查看提示1</summary>

**问题**：不能偷相邻的房屋

```
房屋: [2, 7, 9, 3, 1]
索引:  0  1  2  3  4

可行方案：
✓ 偷 [0, 2, 4]: 2 + 9 + 1 = 12
✓ 偷 [1, 3]: 7 + 3 = 10
✓ 偷 [0, 3]: 2 + 3 = 5

不可行方案：
✗ 偷 [0, 1]: 相邻
✗ 偷 [1, 2]: 相邻
```

**关键**：选择一个房屋后，不能选择相邻的。

</details>

---

### 🌟 提示 Level 2：寻找规律
<details>
<summary>点击查看提示2</summary>

**思考**：到达第 i 个房屋时，如何决策？

```
两个选择：
1. 偷第 i 个房屋
   - 不能偷第 i-1 个
   - 最大金额 = (偷到i-2的最大金额) + nums[i]

2. 不偷第 i 个房屋
   - 保持偷到 i-1 的最大金额
```

**状态转移**：
```
dp[i] = max(dp[i-1], dp[i-2] + nums[i])
```

</details>

---

### 🌟 提示 Level 3：DP实现
<details>
<summary>点击查看提示3</summary>

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n == 0) return 0;
    if (n == 1) return nums[0];
    
    vector<int> dp(n);
    dp[0] = nums[0];
    dp[1] = max(nums[0], nums[1]);
    
    for (int i = 2; i < n; i++) {
        dp[i] = max(dp[i-1], dp[i-2] + nums[i]);
    }
    
    return dp[n-1];
}
```

**手动模拟** `[2, 7, 9, 3, 1]`：
```
dp[0] = 2
dp[1] = max(2, 7) = 7
dp[2] = max(7, 2+9) = 11
dp[3] = max(11, 7+3) = 11
dp[4] = max(11, 11+1) = 12
```

</details>

---

### 🌟 提示 Level 4：空间优化
<details>
<summary>点击查看提示4</summary>

**观察**：只需要前两个状态

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n == 0) return 0;
    if (n == 1) return nums[0];
    
    int prev2 = nums[0];
    int prev1 = max(nums[0], nums[1]);
    
    for (int i = 2; i < n; i++) {
        int curr = max(prev1, prev2 + nums[i]);
        prev2 = prev1;
        prev1 = curr;
    }
    
    return prev1;
}
```

**空间**：O(n) → O(1) ✅

</details>

---

## 🔨 动手实践

### 任务1：手动计算
对 `nums = [1, 2, 3, 1]`，填写 DP 表格：

| i | nums[i] | dp[i] | 决策 |
|---|---------|-------|------|
| 0 | 1       | ?     | ?    |
| 1 | 2       | ?     | ?    |
| 2 | 3       | ?     | ?    |
| 3 | 1       | ?     | ?    |

### 任务2：实现DP数组版本
```cpp
int rob(vector<int>& nums) {
    // TODO: 实现
}
```

### 任务3：实现空间优化版本
```cpp
int rob(vector<int>& nums) {
    // TODO: 使用滚动变量
}
```

---

## ✅ 自检清单

- [ ] 我理解了相邻房屋不能偷的约束
- [ ] 我能推导状态转移方程
- [ ] 我理解了初始化的含义
- [ ] 我能实现空间优化
- [ ] 我测试了边界情况

---

**下一步**：独立实现并对照 `solution.md`！
