# 155. 最小栈 - C++ 题解

## 方法：辅助栈

```cpp
#include <stack>
#include <algorithm>
#include <climits>

using namespace std;

class MinStack {
private:
    stack<int> data_st; // 数据栈
    stack<int> min_st;  // 最小栈，栈顶永远是当前的最小值

public:
    MinStack() {
        // 为了方便比较，可以先放入一个最大值，或者在 push 时判空
        min_st.push(INT_MAX);
    }
    
    void push(int val) {
        data_st.push(val);
        // 比较 val 和 min_st.top()，将较小者压入 min_st
        // 这样 min_st 的大小始终和 data_st 保持一致，一一对应
        min_st.push(min(val, min_st.top()));
    }
    
    void pop() {
        data_st.pop();
        min_st.pop();
    }
    
    int top() {
        return data_st.top();
    }
    
    int getMin() {
        return min_st.top();
    }
};
```

### 优化空间
上面的写法 `min_st` 和 `data_st` 元素个数一样。
其实如果 `val > min_st.top()`，`min_st` 可以不 push（或者 push 重复的最小值）。
但为了实现简单（pop 时不用判断），**同步 push/pop** 是最不容易出错的写法。

如果想优化空间：
```cpp
void push(int val) {
    data_st.push(val);
    // 只有当 val <= 当前最小值时，才入最小栈
    if (min_st.empty() || val <= min_st.top()) {
        min_st.push(val);
    }
}

void pop() {
    int top = data_st.top();
    data_st.pop();
    // 只有当出栈元素等于当前最小值时，最小栈才出栈
    if (top == min_st.top()) {
        min_st.pop();
    }
}
```
这种优化在重复最小值较少时有效，但在最坏情况（递减序列）下空间复杂度一样。**面试时写第一种同步栈更稳。**
