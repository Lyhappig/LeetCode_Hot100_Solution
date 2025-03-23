## 跳跃游戏II

### 问题

给定一个长度为 `n` 的 **0 索引**整数数组 `nums`。初始位置为 `nums[0]`。

每个元素 `nums[i]` 表示从索引 `i` 向后跳转的最大长度。换句话说，如果你在 `nums[i]` 处，你可以跳转到任意 `nums[i + j]` 处:

- `0 <= j <= nums[i]` 
- `i + j < n`

返回到达 `nums[n - 1]` 的最小跳跃次数。生成的测试用例可以到达 `nums[n - 1]`。

提示：

- $1\leq$ `nums.length` $\leq10^4$
- `0 <= nums[i] <= 1000`
- 题目保证可以到达 `nums[n-1]`

### 思路一（动态规划）

类似于《**跳跃游戏**》，只要存在一个位置 $x$，它本身可以到达，并且它跳跃的最大长度满足 $x+nums[x]\geq y$，那么位置 $y$ 也可以到达。

使用有序集合维护二元组 `[该位置出发可达的最远位置，跳跃次数]`，从前向后遍历序列，对于每个下标，在有序集合中二分查找大于等于当前下标的第一个可到达位置，该二元组设为 $[p,q]$：

- 若最远可到达位置 $p=n-1$，则直接返回最小跳跃次数 $q$；
- 若有序集合最后一个二元组的最远可到达位置小于 $i+nums[i]$，则向有序集合中插入二元组 $[i+nums[i],q+1]$。

#### 时空复杂度

时间复杂度 $O(n\log n)$

空间复杂度 $O(n)$​

#### 代码

```C++
class Solution {
public:
    int jump(vector<int>& nums) {
        map<int, int> mp;
        mp[0 + nums[0]] = 1;
        int n = nums.size();
        for (int i = 1; i < n; i++) {
            auto it = mp.lower_bound(i);
            if (i == n - 1) return it->second;
            auto last = mp.rbegin();
            if (last->first < i + nums[i]) {
                mp[i + nums[i]] = it->second + 1;
            }
        }
        return 0;
    }
};
```

### 思路二（贪心）

如果位置 $i$ 可以跳跃的距离是 $x$，那么位置 $[i+1, i+x]$ 都可以作为下一次的起跳点。

1. 对每一个能作为起跳点的位置 $i$ 都尝试跳一次，不断更新最远可到达位置 $p$。
2. 当一次跳跃结束时，从下一个位置 $i+1$ 开始，到最远可到达位置 $p$，都是下一次跳跃的起点，因此更新下一次起跳点的范围 $[i+1,p]$，并更新累计的跳跃次数。

最终到达终点时的累计跳跃次数就是答案。

#### 时空复杂度

时间复杂度 $O(n^2)$

空间复杂度 $O(1)$​

#### 代码

```C++
class Solution {
public:
    int jump(vector<int>& nums) {
        int n = nums.size();
        int steps = 0;
        int l = 0, r = 0;
        while (r < n - 1) {
            int maxPos = 0;
            for (int i = l; i <= r; i++) {
                maxPos = max(maxPos, i + nums[i]);
            }
            l = r + 1;
            r = maxPos;
            ++steps;
        }
        return steps;
    }
};
```

### 思路三（贪心优化）

在思路二的内层循环中，对于 $[l,r]$ 中所有的位置 $j$，只有 $\max\{j+nums[j\}$ 是有效的。因此维护当前能够到达的最大下标位置、上一个跳跃区间的边界、最小跳跃次数。从左到右遍历数组，到达边界时，更新边界并将跳跃次数增加 1。

注意在遍历数组时不访问最后一个元素，这是因为在访问最后一个元素之前，边界一定大于等于最后一个位置。

#### 时空复杂度

时间复杂度 $O(n)$

空间复杂度 $O(1)$​

#### 代码

```C++
class Solution {
public:
    int jump(vector<int>& nums) {
        int pos = 0;
        int preMax = 0;
        int steps = 0;
        for (int i = 0; i < nums.size() - 1; i++) {
            preMax = max(preMax, i + nums[i]);
            if (i == pos) {
                pos = preMax;
                steps++;
            }
        }
        return steps;
    }
};
```

