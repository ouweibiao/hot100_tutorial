# 78. 子集 - 渐进式学习指南

> 💡 **学习目标**：理解子集问题的本质，掌握回溯法的应用

---

## 🎯 问题理解

### 核心问题
给定数组 `[1,2,3]`，找出所有可能的子集。

### 示例
```
输入：[1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

### 关键观察
1. 子集包括**空集**
2. 每个元素可以**选或不选**
3. 共有**2^n**个子集（n=3时有8个）

---

## 💭 思考引导

在查看提示前，先思考以下问题：

### 问题1：暴力思路
如果用最直观的方法，你会怎么生成所有子集？

<details>
<summary>💡 提示</summary>

可以这样思考：
- 对于 `[1,2,3]`，先考虑**不含1**的子集
- 再考虑**含1**的子集
- 递归处理剩余元素

</details>

### 问题2：数学规律
你能发现子集数量和元素个数的关系吗？

<details>
<summary>💡 提示</summary>

- 1个元素：2个子集 `[]`, `[1]`
- 2个元素：4个子集 `[]`, `[1]`, `[2]`, `[1,2]`
- 3个元素：8个子集
- **规律**：n个元素有**2^n**个子集

**原因**：每个元素都有"选"或"不选"两种状态

</details>

### 问题3：决策树
你能画出生成子集的决策树吗？

<details>
<summary>💡 提示</summary>

```
                []
            /        \
        选1           不选1
        [1]            []
       /   \          /   \
   选2      不选2   选2    不选2
  [1,2]    [1]    [2]      []
   / \     / \    / \      / \
 选3 不选3 选3 ...
```

每个叶子节点都是一个子集！

</details>

---

## 📚 渐进式提示

### Level 1：理解问题本质 ⭐

<details>
<summary>点击查看</summary>

#### 子集问题的本质

**子集 = 从n个元素中选出k个的所有组合**（k从0到n）

**关键区别**：
| 问题 | 元素可重复使用 | 考虑顺序 | 示例[1,2] |
|------|--------------|---------|----------|
| 排列 | ❌ | ✅ | [1,2], [2,1] |
| 组合 | ❌ | ❌ | [1,2] |
| 子集 | ❌ | ❌ | [], [1], [2], [1,2] |

**核心特征**：
- ✅ 每个元素**选或不选**
- ✅ 不考虑顺序（`[1,2]` 和 `[2,1]` 是同一个子集）
- ✅ 包括**空集**

#### 手动模拟

对于 `[1,2]`：
1. 空集：`[]`
2. 只选1：`[1]`
3. 只选2：`[2]`
4. 都选：`[1,2]`

**思考**：能推广到3个元素吗？

</details>

---

### Level 2：回溯思路 ⭐⭐

<details>
<summary>点击查看</summary>

#### 回溯法框架

**核心思想**：
- 对每个元素做"选"或"不选"的决策
- 递归处理剩余元素
- **关键**：每层递归都记录当前状态（不同于排列问题）

#### 伪代码

```
function backtrack(start, path):
    记录当前path（这就是一个子集！）
    
    for i from start to end:
        选择元素i
        backtrack(i+1, path+[i])  # 递归
        撤销选择
```

#### 图解流程

```
初始：path=[], start=0
├─ 记录 []
├─ 选择1: path=[1], start=1
│  ├─ 记录 [1]
│  ├─ 选择2: path=[1,2], start=2
│  │  ├─ 记录 [1,2]
│  │  └─ 选择3: path=[1,2,3]
│  │     └─ 记录 [1,2,3]
│  └─ 选择3: path=[1,3]
│     └─ 记录 [1,3]
└─ 选择2: path=[2], start=2
   └─ ...
```

#### 关键问题

**Q1：为什么每层都要记录结果？**
A：因为每个状态都是一个有效子集

**Q2：如何避免重复？**
A：通过`start`参数，确保每次只考虑当前位置之后的元素

**Q3：和排列问题的区别？**
A：排列问题只在叶子节点记录结果，子集问题每层都记录

</details>

---

### Level 3：代码实现 ⭐⭐⭐

<details>
<summary>点击查看</summary>

#### 完整代码

```cpp
vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> result;
    vector<int> path;
    
    function<void(int)> backtrack = [&](int start) {
        // 关键！每个状态都记录
        result.push_back(path);
        
        // 枚举下一个位置的选择
        for (int i = start; i < nums.size(); i++) {
            // 做选择
            path.push_back(nums[i]);
            // 递归
            backtrack(i + 1);
            // 撤销选择
            path.pop_back();
        }
    };
    
    backtrack(0);
    return result;
}
```

#### 代码解析

**关键点1**：`result.push_back(path)`
- 自动复制：C++中 `push_back` 会自动创建副本

**关键点2**：`backtrack(i + 1)`
- 确保不重复选择元素
- `i+1` 表示下次从下一个元素开始

**关键点3**：每层都记录
```cpp
result.push_back(path);  // 先记录
for (int i = start; i < nums.size(); i++) { ... } // 再递归
```

#### 执行轨迹

```text
nums = [1,2,3]

backtrack(0):
  result.push_back({})   → [[]]
  i=0: path=[1]
    backtrack(1):
      result.push_back([1]) → [[],[1]]
      i=1: path=[1,2]
        backtrack(2):
          result.push_back([1,2]) → [[],[1],[1,2]]
          i=2: path=[1,2,3]
            backtrack(3):
              result.push_back([1,2,3]) → [[],[1],[1,2],[1,2,3]]
```

</details>

---

### Level 4：优化与变式 ⭐⭐⭐⭐

<details>
<summary>点击查看</summary>

#### 迭代法（更高效）

**思想**：每次加入新元素时，复制现有子集并添加新元素

```cpp
vector<vector<int>> subsets_iterative(vector<int>& nums) {
    vector<vector<int>> result;
    result.push_back({});  // 初始只有空集
    
    for (int num : nums) {
        // 对每个现有子集，复制并加上num
        int n = result.size();
        for (int i = 0; i < n; i++) {
            vector<int> newSubset = result[i];
            newSubset.push_back(num);
            result.push_back(newSubset);
        }
    }
    
    return result;
}
```

**执行过程**：
```
初始：[[]]
加入1：[[],[1]]
加入2：[[],[1],[2],[1,2]]
加入3：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

#### 位运算法（极致简洁）

**思想**：用二进制表示选择状态

```cpp
vector<vector<int>> subsets_bit(vector<int>& nums) {
    int n = nums.size();
    vector<vector<int>> result;
    
    // 枚举0到2^n-1
    for (int mask = 0; mask < (1 << n); mask++) {
        vector<int> subset;
        for (int i = 0; i < n; i++) {
            // 检查第i位是否为1
            if (mask & (1 << i)) {
                subset.push_back(nums[i]);
            }
        }
        result.push_back(subset);
    }
    
    return result;
}
```

**原理**：
```
nums = [1,2,3]
000 → []
001 → [3] (假设第0位对应3，或反之，具体取决于实现)
...
```

#### 子集II（含重复元素）

如果数组有重复元素，如何去重？

```cpp
vector<vector<int>> subsetsWithDup(vector<int>& nums) {
    sort(nums.begin(), nums.end());  // 先排序
    vector<vector<int>> result;
    vector<int> path;
    
    function<void(int)> backtrack = [&](int start) {
        result.push_back(path);
        
        for (int i = start; i < nums.size(); i++) {
            // 跳过重复元素
            if (i > start && nums[i] == nums[i-1]) {
                continue;
            }
            path.push_back(nums[i]);
            backtrack(i + 1);
            path.pop_back();
        }
    };
    
    backtrack(0);
    return result;
}
```

</details>

---

## 🔨 动手实践

### 任务1：手动模拟
在纸上画出 `[1,2,3]` 的完整决策树，标出所有8个子集。

### 任务2：实现基础版本
```cpp
vector<vector<int>> subsets(vector<int>& nums) {
    // TODO: 实现回溯法
    return {};
}

// 测试
// vector<int> nums = {1, 2, 3};
// assert(subsets(nums).size() == 8);
```

### 任务3：调试版本
添加打印语句，观察递归过程：
```cpp
vector<vector<int>> subsets_debug(vector<int>& nums) {
    vector<vector<int>> result;
    vector<int> path;
    
    function<void(int, int)> backtrack = [&](int start, int depth) {
        string indent = "";
        for(int k=0; k<depth; ++k) indent += "  ";
        
        cout << indent << "start=" << start << ", path=[";
        for(size_t i=0; i<path.size(); ++i) cout << path[i] << (i<path.size()-1?",":"");
        cout << "]" << endl;
        
        result.push_back(path);
        
        for (int i = start; i < nums.size(); i++) {
            path.push_back(nums[i]);
            cout << indent << "→ 选择" << nums[i] << endl;
            backtrack(i + 1, depth + 1);
            path.pop_back();
            cout << indent << "← 撤销" << nums[i] << endl;
        }
    };
    
    backtrack(0, 0);
    return result;
}
```

### 任务4：实现迭代版本
```cpp
vector<vector<int>> subsets_iterative(vector<int>& nums) {
    // TODO: 实现迭代法
    vector<vector<int>> result;
    result.push_back({});
    // 你的代码
    return result;
}
```

---

## ✅ 自我检查清单

完成以下检查后，再查看完整解答：

- [ ] 理解子集问题的本质（每个元素选或不选）
- [ ] 能画出决策树
- [ ] 理解为什么每层都记录结果
- [ ] 掌握回溯法的标准模板
- [ ] 理解如何避免重复（通过start参数）
- [ ] 能独立实现回溯法
- [ ] 理解迭代法的原理
- [ ] 知道如何处理重复元素

---

## 🎓 知识拓展

### 1. 子集 vs 排列 vs 组合

| 特征 | 子集 | 排列 | 组合 |
|------|------|------|------|
| 元素个数 | 0到n | 固定n | 固定k |
| 考虑顺序 | ❌ | ✅ | ❌ |
| 记录时机 | 每层 | 叶子 | 叶子 |
| 总数 | 2^n | n! | C(n,k) |

### 2. 回溯三大经典

1. **排列问题**：`used[]` 标记已使用
2. **组合问题**：`start` 参数控制起点
3. **子集问题**：每层都记录

### 3. 实际应用

- **测试用例生成**：功能开关的所有组合
- **菜单系统**：套餐的所有可选项
- **优惠券组合**：找出最优优惠方案

---

## 🚀 进阶挑战

### 挑战1：子集和问题
找出和为目标值的所有子集

### 挑战2：最大子集
找出满足某条件的最大子集

### 挑战3：子集异或
计算所有子集的异或和

---

## 💡 常见错误

### 错误1：忘记复制path
**注意**：在 Python/Java 中，直接添加对象通常是添加引用，需要显式复制。而在 C++ 中，`std::vector` 的 `push_back` 会自动执行深拷贝。

```cpp
// C++: 正确 (自动复制)
result.push_back(path);

// Python: result.append(path[:])
// Java: result.add(new ArrayList<>(path));
```

### 错误2：只在叶子记录
```cpp
// ❌ 错误（这是排列的做法）
if (start == nums.size()) {
    result.push_back(path);
    return;
}

// ✅ 正确（子集每层都记录）
result.push_back(path);
for (int i = start; i < nums.size(); i++) {
    // ...
}
```

### 错误3：起点错误
```cpp
// ❌ 错误
backtrack(start + 1);  // 会跳过元素

// ✅ 正确
backtrack(i + 1);      // i是当前选择的位置
```

---

## 📖 学习建议

1. **先理解本质**：子集 = 每个元素选或不选
2. **画决策树**：可视化递归过程
3. **对比其他问题**：理解排列/组合/子集的区别
4. **练习变式**：子集II、分割问题等
5. **掌握多种方法**：回溯、迭代、位运算

完成这些后，查看 `solution.md` 获取完整解答！🎉
