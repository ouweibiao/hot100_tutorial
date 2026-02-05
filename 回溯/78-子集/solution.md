# 78. 子集

## 题目描述

给你一个整数数组 `nums`，数组中的元素**互不相同**。返回该数组所有可能的子集（幂集）。

解集**不能**包含重复的子集。你可以按**任意顺序**返回解集。

### 示例

**示例 1：**
```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**示例 2：**
```
输入：nums = [0]
输出：[[],[0]]
```

**提示：**
- `1 <= nums.length <= 10`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有元素**互不相同**

---

## 解法一：回溯（递归）

### 核心思路

子集问题是经典的回溯问题。对于每个元素，我们有两种选择：
1. **选择**这个元素
2. **不选择**这个元素

关键点：
- 每个元素只能选择一次
- 需要记录所有决策路径（不只是完整路径）
- 每层递归都是一个有效的子集

### 决策树

```
                    []
                /        \
            [1]            []
          /     \        /     \
      [1,2]   [1]    [2]      []
      /  \    /  \   /  \    /  \
[1,2,3][1,2][1,3][1][2,3][2][3] []
```

### 代码实现

```cpp
#include <vector>
#include <functional>
using namespace std;

class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> path;
        
        // 使用lambda表达式作为辅助函数
        function<void(int)> backtrack = [&](int start) {
            // 每个状态都是一个有效子集
            result.push_back(path);
            
            // 枚举下一个位置的所有选择
            for (int i = start; i < nums.size(); i++) {
                // 选择当前元素
                path.push_back(nums[i]);
                // 递归（注意是i+1，避免重复选择）
                backtrack(i + 1);
                // 撤销选择
                path.pop_back();
            }
        };
        
        backtrack(0);
        return result;
    }
};

// 测试
// Solution sol;
// vector<int> nums = {1, 2, 3};
// vector<vector<int>> res = sol.subsets(nums);
// [[],[1],[1,2],[1,2,3],[1,3],[2],[2,3],[3]]
```

### 复杂度分析
- **时间复杂度**：O(n × 2^n)
  - 子集总数：2^n
  - 复制每个子集：O(n)
- **空间复杂度**：O(n)（递归栈深度）

---

## 解法二：迭代法

### 核心思路

利用数学思想：
- 初始子集只有空集：`[[]]`
- 每次添加一个新元素时，将现有的每个子集都复制一份并加上新元素

### 图解过程

```
初始：[[]]

加入1：
[[]] → [[],[1]]

加入2：
[[],[1]] → [[],[1],[2],[1,2]]

加入3：
[[],[1],[2],[1,2]] → [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

### 代码实现

```cpp
vector<vector<int>> subsets_iterative(vector<int>& nums) {
    /*
    迭代法生成所有子集
    */
    vector<vector<int>> result;
    result.push_back({});  // 初始只有空集
    
    for (int num : nums) {
        // 复制现有的所有子集，并加上当前元素
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

### 复杂度分析
- **时间复杂度**：O(n × 2^n)
- **空间复杂度**：O(1)（不计结果数组）

---

## 解法三：位运算

### 核心思路

利用二进制表示：
- n个元素的子集总数为2^n
- 用0到2^n-1的二进制表示每个子集
- 二进制位为1表示选择该元素

### 图解

```
nums = [1,2,3]，共3个元素

000(0) → []
001(1) → [3]
010(2) → [2]
011(3) → [2,3]
100(4) → [1]
101(5) → [1,3]
110(6) → [1,2]
111(7) → [1,2,3]
```

### 代码实现

```cpp
vector<vector<int>> subsets_bit(vector<int>& nums) {
    /*
    位运算法生成所有子集
    */
    int n = nums.size();
    vector<vector<int>> result;
    
    // 遍历0到2^n-1的所有数字
    for (int mask = 0; mask < (1 << n); mask++) {  // 1 << n = 2^n
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

// 测试
// subsets_bit({1,2,3});
```

### 复杂度分析
- **时间复杂度**：O(n × 2^n)
- **空间复杂度**：O(1)（不计结果数组）

---

## 三种方法对比

| 方法 | 时间复杂度 | 空间复杂度 | 优点 | 缺点 |
|------|-----------|-----------|------|------|
| 回溯 | O(n×2^n) | O(n) | 思路清晰，易于理解 | 需要递归栈 |
| 迭代 | O(n×2^n) | O(1) | 无递归，空间效率高 | 不够直观 |
| 位运算 | O(n×2^n) | O(1) | 代码简洁 | 不适合大数组 |

---

## 关键知识点

### 1. 回溯模板
```cpp
void backtrack(参数) {
    if (满足结束条件) {
        记录结果;
        return;
    }
    
    for (选择 : 选择列表) {
        做选择;
        backtrack(参数);
        撤销选择;
    }
}
```

### 2. 子集问题特点
- ✅ **每层都记录结果**（不同于排列/组合）
- ✅ 不需要终止条件（for循环自然结束）
- ✅ 需要去重（通过start参数）

### 3. 时间复杂度分析
- 子集总数：**2^n**（每个元素选或不选）
- 复制每个子集：**O(n)**
- 总时间：**O(n × 2^n)**

---

## 常见变式

### 1. [90. 子集 II](https://leetcode.cn/problems/subsets-ii/)
- 数组包含**重复元素**
- 需要跳过重复子集
- **解法**：排序 + 剪枝

### 2. [784. 字母大小写全排列](https://leetcode.cn/problems/letter-case-permutation/)
- 字母可以大小写变换
- 本质也是子集问题

### 3. [491. 递增子序列](https://leetcode.cn/problems/increasing-subsequences/)
- 需要保持递增顺序
- 不能排序（会破坏原顺序）

---

## 实战应用

### 场景1：测试用例生成
```cpp
vector<vector<string>> generate_test_cases(vector<string>& features) {
    /* 生成所有功能组合的测试用例 */
    // 这里可以使用之前的 subsets 逻辑，只需将 int 换成 string
    vector<vector<string>> result;
    vector<string> path;
    function<void(int)> backtrack = [&](int start) {
        result.push_back(path);
        for (int i = start; i < features.size(); i++) {
            path.push_back(features[i]);
            backtrack(i + 1);
            path.pop_back();
        }
    };
    backtrack(0);
    return result;
}

// 测试3个功能的所有组合
// vector<string> features = {"登录", "支付", "评论"};
// vector<vector<string>> test_cases = generate_test_cases(features);
// [[], ["登录"], ["登录","支付"], ...]
```

### 场景2：组合优惠方案
```cpp
vector<int> find_best_discount(vector<int>& coupons, int max_discount = INT_MAX) {
    /* 找出最优优惠组合 */
    // 假设 subsets 已实现
    Solution sol;
    vector<vector<int>> all_combinations = sol.subsets(coupons);
    vector<int> best;
    int max_value = 0;
    
    for (auto& combo : all_combinations) {
        if (can_combine(combo)) {  // 检查是否可叠加（假设函数存在）
            int value = 0;
            for(int v : combo) value += v;
            
            if (value > max_value && value <= max_discount) {
                max_value = value;
                best = combo;
            }
        }
    }
    
    return best;
}
```

---

## 调试技巧

### 可视化决策树
```cpp
vector<vector<int>> subsets_debug(vector<int>& nums) {
    vector<vector<int>> result;
    vector<int> path;
    
    function<void(int, int)> backtrack = [&](int start, int depth) {
        string indent = "";
        for(int k=0; k<depth; ++k) indent += "  ";
        
        cout << indent << "进入 start=" << start << ", path=[";
        for(size_t i=0; i<path.size(); ++i) cout << path[i] << (i<path.size()-1?",":"");
        cout << "]" << endl;
        
        result.push_back(path);
        
        for (int i = start; i < nums.size(); i++) {
            path.push_back(nums[i]);
            cout << indent << "选择 " << nums[i] << ", path=[";
            for(size_t j=0; j<path.size(); ++j) cout << path[j] << (j<path.size()-1?",":"");
            cout << "]" << endl;
            
            backtrack(i + 1, depth + 1);
            
            path.pop_back();
            cout << indent << "撤销 " << nums[i] << ", path=[";
            for(size_t j=0; j<path.size(); ++j) cout << path[j] << (j<path.size()-1?",":"");
            cout << "]" << endl;
        }
    };
    
    backtrack(0, 0);
    return result;
}

// 测试
// subsets_debug({1,2,3});
```

---

## 总结

### 核心要点
1. ✅ **子集 = 每个元素选或不选的所有组合**
2. ✅ **回溯每层都记录结果**（不同于排列/组合）
3. ✅ **三种解法各有优劣**（回溯最常用）
4. ✅ **时间复杂度必然是O(n×2^n)**（子集总数决定）

### 推荐解法
- **面试首选**：回溯法（易于扩展）
- **性能优化**：迭代法（无递归栈）
- **代码简洁**：位运算（适合小数组）

### 学习建议
1. 先掌握回溯法（最通用）
2. 理解决策树结构
3. 对比排列/组合/子集的区别
4. 练习变式题目（如子集II）
