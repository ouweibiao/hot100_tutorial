# 51. N 皇后 - C++ 题解

## 方法：回溯 + 集合判重

```cpp
#include <vector>
#include <string>
#include <unordered_set>
using namespace std;

class Solution {
public:
    vector<vector<string>> ans;
    unordered_set<int> cols;
    unordered_set<int> diag1; // 主对角线 row - col
    unordered_set<int> diag2; // 副对角线 row + col

    vector<vector<string>> solveNQueens(int n) {
        vector<string> board(n, string(n, '.'));
        backtrack(board, 0, n);
        return ans;
    }

    void backtrack(vector<string>& board, int row, int n) {
        if (row == n) {
            ans.push_back(board);
            return;
        }

        for (int col = 0; col < n; ++col) {
            if (cols.count(col) || diag1.count(row - col) || diag2.count(row + col)) {
                continue;
            }

            // 做出选择
            board[row][col] = 'Q';
            cols.insert(col);
            diag1.insert(row - col);
            diag2.insert(row + col);

            // 进入下一行
            backtrack(board, row + 1, n);

            // 撤销选择 (回溯)
            board[row][col] = '.';
            cols.erase(col);
            diag1.erase(row - col);
            diag2.erase(row + col);
        }
    }
};
```
