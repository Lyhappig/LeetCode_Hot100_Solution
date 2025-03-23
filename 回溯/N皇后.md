## N皇后

### 问题

按照国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回所有不同的 **n 皇后问题** 的解决方案。

每一种解法包含一个不同的 **n 皇后问题** 的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

提示：

- `1 <= n <= 9`

### 思路（搜索）

考虑递归搜索每一行，算法流程为：

- 若搜索完前 $n$ 行，则更新答案。
- 对于第 $r$ 行，枚举第 $c$ 列，判断该位置是否可以放置皇后，即第 $c$ 列、位置 $(r,c)$ 对应的主对角线和次对角线是否已经防止了皇后。放置后更新三个方向的标记，递归搜索下一层。
- 回溯时撤销三个方向的标记。

注意：对于二维表格中任意位置 $(i,j)$，$i+j$ 唯一表示了次对角线，$i-j$ 唯一表示了主对角线。

#### 时空复杂度

时间复杂度 $O(n\times n!)$

空间复杂度 $O(n)$​

#### 代码

```C++
class Solution {
public:
    int n;
    vector<int> pos;
    vector<vector<string>> ans;
    vector<bool> col;
    vector<bool> md;
    vector<bool> sd;

    void getAns() {
        string s;
        for (int i = 0; i < n; i++) s += '.';
        vector<string> res(n, s);
        for (int r = 0, c; r < n; r++) {
            c = pos[r];
            res[r][c] = 'Q';
        }
        ans.push_back(res);
    }

    void dfs(int r) {
        if (r >= n) {
            getAns();
            return;
        }
        for (int c = 0, x, y; c < n; c++) {
            x = r - c + n - 1;
            y = r + c;
            if (col[c] || md[x] || sd[y]) continue;
            col[c] = md[x] = sd[y] = true;
            pos.push_back(c);
            dfs(r + 1);
            pos.pop_back();
            col[c] = md[x] = sd[y] = false;
        }
    }

    vector<vector<string>> solveNQueens(int N) {
        n = N;
        col.resize(n, false);
        md.resize(2 * n, false);
        sd.resize(2 * n, false);
        dfs(0);
        return ans;
    }
};
```
