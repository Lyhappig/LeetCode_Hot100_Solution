## 二叉搜索树中第k小的元素

### 问题

给定一个二叉搜索树的根节点 `root` ，和一个整数 `k` ，请你设计一个算法查找其中第 `k` 小的元素（从 1 开始计数）。

提示：

- 树中的节点数为 `n` 。
- $1\leq k\leq n\leq 10^4$
- $0\leq$ `Node.val` $\leq10^4$

### 思路一（中序遍历）

- 采用递归或迭代，在中序遍历得到有序序列后，直接输出第 $k$ 小元素

- 采用递归或迭代，在过程中记录加入中序序列的元素个数，从而找到第 $k$ 小元素

#### 时空复杂度

时间复杂度 $O(n)$

空间复杂度 $O(n)$

#### 代码

```c++
class Solution {
private:
    int count = 0;
    int ans = -1;
public:
    int kthSmallest(TreeNode* root, int k) {
        inorder(root, k);
        return ans;
    }

    void inorder(TreeNode* node, int& k) {
        if (!node || ans != -1) return;
        inorder(node->left, k);
        if (++count == k) {
            ans = node->val;
            return;
        }
        inorder(node->right, k);
    }
};
```

### 思路二（记录子树大小）

首先求出每个节点为根的子树大小，然后采用如下迭代的方法：

- 记当前子树根节点的左子树大小加上一个根节点的数目是 $rank$

- 若 $rank$ 恰好等于 $k$，则返回该节点的值；
- 否则：
  - 若 $rank > k$，前往左子树查找；
  - 否则 $k = k - rank$，前往右子树查找；

#### 时空复杂度

时间复杂度 $O(n)$

空间复杂度 $O(n)$​

#### 代码

```C++
class Solution {
private:
    unordered_map<TreeNode*, int> treeSize;
public:
    int dfs(TreeNode* root) {
        if(root == nullptr) return 0;
        return treeSize[root] = dfs(root->left) + dfs(root->right) + 1;
    }

    int kthSmallest(TreeNode* root, int k) {
        dfs(root);
        while (root != nullptr) {
            int rank = treeSize[root->left] + 1;
            if(rank == k) {
                return root->val;
            } else if(rank < k) {
                k -= rank;
                root = root->right;
            } else {
                root = root->left;
            }
        }
        return 0;      
    }
};
```
