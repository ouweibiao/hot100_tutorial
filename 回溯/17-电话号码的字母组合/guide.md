# 17. 电话号码的字母组合

## 题目描述

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。
- 2: abc
- 3: def
- 4: ghi
- 5: jkl
- 6: mno
- 7: pqrs
- 8: tuv
- 9: wxyz

## 解题思路

### 方法：回溯 (Backtracking)
- **映射表**：首先建立数字到字母的映射数组 `string phoneMap[10]`。
- **递归函数**：`backtrack(index, path)`
  - `index`：当前处理到 `digits` 字符串的第几位。
  - `path`：当前生成的字母组合。
- **流程**：
  - 终止条件：`index == digits.size()`，说明已生成一个完整组合，加入答案。
  - 递归过程：
    - 取出当前数字 `digit = digits[index]`。
    - 找到对应的字母字符串 `letters = phoneMap[digit]`。
    - 遍历 `letters` 中的每个字母，将其加入 `path`，递归调用 `backtrack(index + 1, path)`，然后回溯（pop_back）。

## 复杂度
- 时间复杂度：O(3^M * 4^N)，其中 M 是对应 3 个字母的数字个数，N 是对应 4 个字母的数字个数。
- 空间复杂度：O(M + N)，递归栈深度。
