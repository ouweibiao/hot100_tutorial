# 46. 全排列

## 📝 题目描述

给定一个不含重复数字的数组 `nums`，返回其**所有可能的全排列**。你可以**按任意顺序**返回答案。

**示例 1：**
```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**示例 2：**
```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```

**示例 3：**
```
输入：nums = [1]
输出：[[1]]
```

**约束条件：**
- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有整数**互不相同**

---

## ⭐ 解法：回溯算法

### 思路

**回溯三要素**：
1. **选择**：从剩余元素中选择一个
2. **递归**：继续排列剩余元素
3. **撤销**：回溯，恢复状态

**使用 visited 数组**记录元素是否已使用。

### 代码实现
```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> path;
        vector<bool> visited(nums.size(), false);
        
        backtrack(nums, path, visited, result);
        return result;
    }
    
private:
    void backtrack(vector<int>& nums, vector<int>& path, 
                   vector<bool>& visited, vector<vector<int>>& result) {
        // 递归终止条件
        if (path.size() == nums.size()) {
            result.push_back(path);
            return;
        }
        
        // 遍历所有选择
        for (int i = 0; i < nums.size(); i++) {
            if (visited[i]) continue;  // 已使用，跳过
            
            // 做选择
            path.push_back(nums[i]);
            visited[i] = true;
            
            // 递归
            backtrack(nums, path, visited, result);
            
            // 撤销选择（回溯）
            path.pop_back();
            visited[i] = false;
        }
    }
};
```

### 复杂度分析
- **时间复杂度**：O(n × n!)
  - 生成 n! 个排列
  - 每个排列需要 O(n) 时间复制
- **空间复杂度**：O(n)，递归栈深度

---

## 💡 解法二：交换法

### 思路

通过交换元素位置生成排列，不需要 visited 数组。

### 代码实现
```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> result;
        backtrack(nums, 0, result);
        return result;
    }
    
private:
    void backtrack(vector<int>& nums, int start, vector<vector<int>>& result) {
        if (start == nums.size()) {
            result.push_back(nums);
            return;
        }
        
        for (int i = start; i < nums.size(); i++) {
            // 交换
            swap(nums[start], nums[i]);
            // 递归
            backtrack(nums, start + 1, result);
            // 回溯（交换回来）
            swap(nums[start], nums[i]);
        }
    }
};
```

---

## 🎯 核心知识点

### 1. 回溯算法模板

```cpp
void backtrack(选择列表, 路径, 结果) {
    if (满足结束条件) {
        结果.add(路径);
        return;
    }
    
    for (选择 in 选择列表) {
        if (不合法) continue;
        
        // 做选择
        路径.add(选择);
        标记为已使用;
        
        // 递归
        backtrack(选择列表, 路径, 结果);
        
        // 撤销选择（回溯）
        路径.remove(选择);
        标记为未使用;
    }
}
```

### 2. 决策树

以 `nums = [1,2,3]` 为例：

```
                    []
          /         |         \
        [1]        [2]        [3]
       /  \        /  \        /  \
    [1,2][1,3]  [2,1][2,3]  [3,1][3,2]
      |    |      |    |      |    |
   [1,2,3][1,3,2][2,1,3][2,3,1][3,1,2][3,2,1]
```

每个节点表示一个状态，从根到叶是一条路径（一个排列）。

### 3. 回溯 vs 递归

**递归**：
- 一直往下走，直到找到答案

**回溯**：
- 往下走（递归）
- 如果不行，退回来（撤销）
- 尝试其他路径

---

## 🔍 图解过程

以 `nums = [1,2]` 为例：

```
初始：path=[], visited=[false, false]

Step 1: 选择 1
  path=[1], visited=[true, false]
  递归 →
    Step 1.1: 选择 2
      path=[1,2], visited=[true, true]
      path.size()==2，加入结果：[1,2]
      回溯 →
    path=[1], visited=[true, false]
  回溯 →
path=[], visited=[false, false]

Step 2: 选择 2
  path=[2], visited=[false, true]
  递归 →
    Step 2.1: 选择 1
      path=[2,1], visited=[true, true]
      path.size()==2，加入结果：[2,1]
      回溯 →
    path=[2], visited=[false, true]
  回溯 →
path=[], visited=[false, false]

结果：[[1,2], [2,1]]
```

---

## 🎓 变式题目

### 1. 全排列 II（LeetCode 47）
数组包含重复元素，返回不重复的全排列。

**处理重复**：
```cpp
sort(nums.begin(), nums.end());
if (i > 0 && nums[i] == nums[i-1] && !visited[i-1]) continue;
```

### 2. 组合（LeetCode 77）
从 n 个数中选择 k 个数的组合。

**区别**：
- 排列：顺序不同算不同
- 组合：顺序相同算相同

### 3. 子集（LeetCode 78）
返回所有可能的子集。

---

## 📌 记忆要点

- **回溯三步**：选择 → 递归 → 撤销
- **终止条件**：`path.size() == nums.size()`
- **剪枝**：`if (visited[i]) continue`
- **回溯关键**：撤销选择，恢复状态

---

## 🔨 常见错误

### 错误1：忘记回溯
```cpp
// ❌ 错误：只做选择，不撤销
path.push_back(nums[i]);
visited[i] = true;
backtrack(...);
// 忘记撤销

// ✅ 正确
path.push_back(nums[i]);
visited[i] = true;
backtrack(...);
path.pop_back();
visited[i] = false;
```

### 错误2：直接push path
```cpp
// ❌ 错误：result 中所有元素都会变化
result.push_back(path);

// ✅ 正确：复制一份
result.push_back(path);  // vector自动复制
```

### 错误3：visited 判断位置错误
```cpp
// ❌ 错误：在递归后判断
backtrack(...);
if (visited[i]) continue;

// ✅ 正确：在递归前判断
if (visited[i]) continue;
backtrack(...);
```

---

**标签**：`回溯` `数组` `medium`
