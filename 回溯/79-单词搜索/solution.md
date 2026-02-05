# 79. 单词搜索 - C++ 题解

## 方法：DFS + 回溯

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        int m = board.size();
        int n = board[0].size();
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (dfs(board, word, i, j, 0)) {
                    return true;
                }
            }
        }
        return false;
    }

    bool dfs(vector<vector<char>>& board, string& word, int i, int j, int k) {
        // 边界检查或字符不匹配
        if (i < 0 || i >= board.size() || j < 0 || j >= board[0].size() || board[i][j] != word[k]) {
            return false;
        }
        
        // 匹配成功
        if (k == word.size() - 1) {
            return true;
        }

        // 标记已访问，避免走回头路
        char temp = board[i][j];
        board[i][j] = '#';

        // 向四个方向探索
        bool found = dfs(board, word, i + 1, j, k + 1) ||
                     dfs(board, word, i - 1, j, k + 1) ||
                     dfs(board, word, i, j + 1, k + 1) ||
                     dfs(board, word, i, j - 1, k + 1);

        // 回溯：恢复字符
        board[i][j] = temp;

        return found;
    }
};
```
