# 437. 路径总和 III

## 题目描述

给定一个二叉树的根节点 `root` ，和一个整数 `targetSum` ，求该二叉树里节点值之和等于 `targetSum` 的 **路径** 的数目。

**路径** 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

## 解题思路

### 方法一：双重递归 (暴力法)
- `pathSum(root)` = 以 `root` 为起点的路径数 + `pathSum(root->left)` + `pathSum(root->right)`。
- 需要写一个辅助函数 `rootSum(node, target)` 来计算以 `node` 为起点的路径数。
- 时间复杂度：O(N^2)。

### 方法二：前缀和 (Prefix Sum) —— 推荐
- **核心思想**：类似于数组的前缀和。
- 维护一个哈希表 `prefixMap`，记录 **从根节点到当前节点路径上** 各种前缀和出现的次数。
- **公式**：
  - `currSum` = 从根到当前节点的路径和。
  - 如果存在一个之前的节点（祖先），其前缀和为 `currSum - targetSum`，那么中间这段路径的和就是 `targetSum`。
  - `ans += prefixMap[currSum - targetSum]`。
- **回溯**：
  - 在进入子树前，将 `currSum` 加入 `prefixMap`。
  - 在离开子树后，将 `currSum` 从 `prefixMap` 中移除（或计数减1），因为这部分路径不能被其他分支复用。

## 复杂度
- 时间复杂度：O(N)。
- 空间复杂度：O(N)。
