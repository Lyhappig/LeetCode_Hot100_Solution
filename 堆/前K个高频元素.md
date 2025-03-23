## 前K个高频元素

### 问题

给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按 **任意顺序** 返回答案。

提示：

- $1\leq$ `nums.length` $\leq 10^5$
- `k` 的取值范围是 `[1, 数组中不相同的元素的个数]`
- 题目数据保证答案唯一，换句话说，数组中前 `k` 个高频元素的集合是唯一的

### 思路（堆）

首先遍历整个数组，并使用哈希表记录每个数字出现的次数。找出原数组的前 $k$ 个高频元素，就相当于找出哈希表的前 $k$ 大的值。

建立一个小顶堆，然后遍历哈希表：

- 如果堆的元素个数小于 $k$，就可以直接插入堆中。
- 如果堆的元素个数等于 $k$，则检查堆顶与当前出现次数的大小。如果堆顶更大，说明至少有 $k$ 个数字的出现次数比当前值大，故舍弃当前值；否则，就弹出堆顶，并将当前值插入堆中。

遍历完成后，堆中的元素就代表了前 $k$ 个高频元素。

#### 时空复杂度

时间复杂度 $O(n \log n)$

空间复杂度 $O(n)$​

#### 代码

```C++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> mp;
        for (auto x: nums) {
            mp[x]++;
        }
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> q;
        for (auto &p: mp) {
            if (q.size() == k) {
                if (q.top().first < p.second) {
                    q.pop();
                    q.push({p.second, p.first});
                } 
            } else {
                q.push({p.second, p.first});
            }
        }
        vector<int> ans;
        while (!q.empty()) {
            ans.push_back(q.top().second);
            q.pop();
        }
        return ans;
    }
};
```
