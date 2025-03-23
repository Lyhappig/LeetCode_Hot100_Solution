## 和为 K 的子数组

### 问题

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回**该数组中和为 `k` 的子数组的个数** 。

子数组是数组中元素的连续非空序列。

提示：

- $1 \leq$ `nums.length` $\leq 2\times 10^4$
- $-1000 \leq$ `nums[i]` $\leq 1000$
- $-10^7 \leq k \leq 10^7$

### 思路（前缀和）

设数组 $sum$ 代表数组的前缀和数组，假设存在一个子数组 $[l, r]$ 满足 $sum[r] - sum[l - 1] = k$，移项后得 $sum[r] - k = sum[l - 1]$，又因为 $l -1 < r$，因此问题变成了**固定右边界 $r$，有多少个左边界满足前缀和等于 $sum[r] - k$**

数组中含有负数和 0，因此需要用哈希表维护前缀和的数量。

#### 时空复杂度

时间复杂度 $O(n)$

空间复杂度 $O(n)$​

#### 代码

```C++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> sum(n + 1, 0);
        unordered_map<int, int> mp;
        for (int i = 1; i <= n; i++) {
            sum[i] = sum[i - 1] + nums[i - 1];
        }
        int ans = 0;
        for (int i = 1; i <= n; i++) {
            int x = sum[i] - k;
            ans += mp[x] + (sum[i] == k);
            mp[sum[i]]++;
        }
        return ans;
    }
};
```
