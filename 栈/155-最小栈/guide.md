# 155. 最小栈

## 题目描述

设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈。

实现 `MinStack` 类:
- `MinStack()` 初始化堆栈对象。
- `void push(int val)` 将元素 val 推入堆栈。
- `void pop()` 删除堆栈顶部的元素。
- `int top()` 获取堆栈顶部的元素。
- `int getMin()` 获取堆栈中的最小元素。

**示例 1:**
```
输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]
```

## 解题思路

### 核心难点
如何在 `push` 和 `pop` 的同时，始终能在 O(1) 时间内知道当前的最小值？

### 方法一：辅助栈 (推荐)
- 使用两个栈：
  1. `dataStack`：正常的栈，存数据。
  2. `minStack`：辅助栈，存**当前状态下**的最小值。
- **Push(x)**：
  - `dataStack` 直接 push x。
  - `minStack`：
    - 如果为空，直接 push x。
    - 如果不为空，比较 x 和 `minStack.top()`，将**较小值** push 进 `minStack`。
    - **关键**：这保证了 `minStack` 的栈顶永远是 `dataStack` 栈顶对应范围内的最小值。
- **Pop()**：
  - 两个栈同时 pop。
- **GetMin()**：
  - 直接返回 `minStack.top()`。

### 方法二：存储差值 (极客解法)
- 栈中存储 `x - min` 的差值。
- 需要一个变量 `min_value` 记录当前最小值。
- 优点是省空间，但要注意数值溢出问题，且逻辑复杂，面试不推荐首选。

## 总结
使用辅助栈是最清晰、最标准的解法。
