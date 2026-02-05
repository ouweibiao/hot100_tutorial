# 238. 除自身以外数组的乘积 - 编程指导

## 🎯 学习目标
- 理解前缀积和后缀积的概念
- 掌握空间优化技巧
- 学会不使用除法解决问题

---

## 💭 思考提示（分4级）

### 🌟 提示 Level 1：暴力思路
<details>
<summary>点击查看提示1</summary>

**最简单的方法**：
```cpp
for (int i = 0; i < n; i++) {
    int product = 1;
    for (int j = 0; j < n; j++) {
        if (j != i) {
            product *= nums[j];
        }
    }
    answer[i] = product;
}
```

**复杂度**：O(n²)，太慢！

</details>

---

### 🌟 提示 Level 2：分解问题
<details>
<summary>点击查看提示2</summary>

**观察**：
```
nums = [1, 2, 3, 4]

answer[1] = 1 * 3 * 4
          = (左侧元素的乘积) * (右侧元素的乘积)
          = 1 * (3*4)
```

**核心思想**：
```
answer[i] = 左侧乘积[i] * 右侧乘积[i]
```

</details>

---

### 🌟 提示 Level 3：两次遍历
<details>
<summary>点击查看提示3</summary>

```cpp
// 第一次：计算左侧乘积
left[0] = 1;
for (int i = 1; i < n; i++) {
    left[i] = left[i-1] * nums[i-1];
}

// 第二次：计算右侧乘积
right[n-1] = 1;
for (int i = n-2; i >= 0; i--) {
    right[i] = right[i+1] * nums[i+1];
}

// 合并
for (int i = 0; i < n; i++) {
    answer[i] = left[i] * right[i];
}
```

</details>

---

### 🌟 提示 Level 4：空间优化
<details>
<summary>点击查看提示4</summary>

**优化**：用 answer 存左侧乘积，用变量滚动右侧乘积

```cpp
// 正向：存左侧乘积
answer[0] = 1;
for (int i = 1; i < n; i++) {
    answer[i] = answer[i-1] * nums[i-1];
}

// 反向：乘上右侧乘积
int rightProduct = 1;
for (int i = n-1; i >= 0; i--) {
    answer[i] *= rightProduct;
    rightProduct *= nums[i];
}
```

**空间**：O(1) ✅

</details>

---

## ✅ 自检清单

- [ ] 我理解了左右乘积的含义
- [ ] 我能实现 O(n) 时间的解法
- [ ] 我能优化到 O(1) 空间
- [ ] 我理解了为什么不用除法

---

**下一步**：独立实现并测试！
