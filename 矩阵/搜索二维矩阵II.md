## 搜索二维矩阵II

### 问题

编写一个高效的算法来搜索 $m \times n$ 矩阵 `matrix` 中的一个目标值 `target` 。该矩阵具有以下特性：

- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

提示：

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= n, m <= 300`
- $-10^9 \leq $ `matrix[i][j]` $\leq 10^9$
- 每行的所有元素从左到右升序排列
- 每列的所有元素从上到下升序排列
- $-10^9 \leq $ `target` $\leq 10^9$

图示：

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/11/25/searchgrid2.jpg)

### 思路一（二分查找）

遍历所有的行，每行都是单调递增的序列，对每行进行二分查找。

#### 时空复杂度

时间复杂度 $O(n \times \log m)$

空间复杂度 $O(1)$​

#### 代码

```C++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int n = matrix.size(), m = matrix[0].size();
        for (int i = 0; i < n; i++) {
            int l = 0, r = m - 1;
            while (l < r) {
                int mid = (l + r) >> 1;
                if (matrix[i][mid] < target) l = mid + 1;
                else r = mid;
            }
            if (matrix[i][l] == target) return true;
        }
        return false;
    }
};
```

### 思路二（二分优化）

可以观察到矩阵左上角为最小值，右下角为最大值。考虑对一个子矩阵寻找目标值，若目标值**小于子矩阵最小值**或**大于子矩阵最大值**。

将矩阵划分为**四等份子矩阵**，采用同样的方式递归划分子矩阵，判断 `target` 是否在每块中。这样期望在 $O(\log n + \log m)$ 的时间复杂度下找到目标值。

#### 时空复杂度

最优时间复杂度 $O(\log n + \log m)$，最坏时间复杂度 $O(n + m)$

空间复杂度 $O(n \times m)$​

#### 代码

```C++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int n = matrix.size(), m = matrix[0].size();
        function<bool(int, int, int, int)> dfs = [&](int x1, int y1, int x2, int y2) -> bool {
            if (x1 > x2 || y1 > y2) return false;
            if (x1 == x2 && y1 == y2) {
                return matrix[x1][y1] == target;
            }
            if (target < matrix[x1][y1] || target > matrix[x2][y2]) return false;
            int midX = (x1 + x2) >> 1;
            int midY = (y1 + y2) >> 1;
            return dfs(x1, y1, midX, midY) || dfs(x1, midY + 1, midX, y2) ||    
                    dfs(midX + 1, y1, x2, midY) || dfs(midX + 1, midY + 1, x2, y2);
        };
        return dfs(0, 0, n - 1, m - 1);
    }
};
```

### 思路三（思维）

将矩阵逆时针旋转 45° ，并将其转化为图形式，其类似于**二叉搜索树**，即对于每个元素，其左分支元素更小、右分支元素更大。因此，通过从 “根节点” 开始搜索，遇到比 target 大的元素就向左，反之向右，即可找到目标值 target 。

<img src="https://pic.leetcode-cn.com/6584ea93812d27112043d203ea90e4b0950117d45e0452d0c630fcb247fbc4af-Picture1.png" alt="Picture1.png" style="zoom:50%;" />

在从右上角逐步移动指针的过程中，逐个比较的最坏时间复杂度为 $O(n + m)$，但指针在向左或者向右移动过程中，可以采用倍增优化，得到期望 $O(\log n + \log m)$ 的时间复杂度。

#### 时空复杂度

最优时间复杂度 $O(\log n + \log m)$，最坏时间复杂度 $O(n \times m)$

空间复杂度 $O(1)$

#### 代码

```C++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int n = matrix.size(), m = matrix[0].size();
        int i = 0, j = m - 1;
        while (i >= 0 && i < n && j >= 0 && j < m) {
            if (matrix[i][j] == target)
                return true;
            else if (matrix[i][j] < target) {
                i++;
            } else {
                j--;
            }
        }
        return false;
    }
};
```

