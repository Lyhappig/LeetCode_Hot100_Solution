## 路径总和III

### 问题

给定一个二叉树的根节点 `root` ，和一个整数 `targetSum` ，求该二叉树里节点值之和等于 `targetSum` 的 **路径** 的数目。

**路径** 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

提示：

- 二叉树的节点个数的范围是 `[0,1000]`
- $-10^9 \leq$ `Node.val` $\leq 10^9$
- `-1000 <= targetSum <= 1000` 

### 思路（前缀和递归）

限制从父节点到子节点的简单路径，简单的想，求出每个节点到所有子孙节点的路径和，统计路径和等于 `targetSum` 的个数，时间复杂度为 $O(n^2)$

反过来想，可以求每个节点有多少个祖先节点，满足它们之间的路径和为给定值。根据前缀和的思想，求出从根节点到每个节点的前缀和，任意两个节点之间的路径和可以表示为两个前缀和的差。因此对于前缀和 $s$，只需要知道从根节点到它有多少个 $t$ 满足 `s - t = targetSum`，该信息可以在递归过程中动态记录：

- 对于当前节点的前缀和 $s$，统计哈希表中 `s - targetSum` 的个数
- 递归左右子树前，增加哈希表中 $s$ 的个数
- 递归左右子树后，将跳到祖先节点（父节点以上）的另一棵子树，减去哈希表中 $s$ 的个数

注意：类似于线性前缀和中特殊的下标 0，初始要在哈希表中设置前缀和 0 出现一次。相当于在根节点上设置一个**虚拟节点，该节点的值是 0**，这样能统计到恰好由根节点到子孙节点的简单路径和。

#### 时空复杂度

时间复杂度 $O(n)$

空间复杂度 $O(n)$

```c++
class Solution {
private:
    unordered_map<long long, int> mp;
    int ans;
public:
    void dfs(TreeNode* root, long long curSum, int targetSum) {
        if (!root) return;
        curSum += root->val;
        if (mp.count(curSum - targetSum)) {
            ans += mp.count(curSum - targetSum) ? mp[curSum - targetSum] : 0;
        }
        mp[curSum]++;
        dfs(root->left, curSum, targetSum);
        dfs(root->right, curSum, targetSum);
        mp[curSum]--;
    }

    int pathSum(TreeNode* root, int targetSum) {
        mp.clear();
        ans = 0;
        mp[0] = 1;
        dfs(root, 0, targetSum);
        return ans;
    }
};
```
