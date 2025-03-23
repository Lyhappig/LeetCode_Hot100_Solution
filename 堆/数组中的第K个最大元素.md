## 数组中的第K个最大元素

### 问题

给定整数数组 `nums` 和整数 `k`，请返回数组中第 `k` 个最大的元素。

请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。

你必须设计并实现时间复杂度为 `O(n)` 的算法解决此问题。

提示：

- $1\leq$ `k <= nums.length` $\leq 10^5$
- $-10^4 \leq$ `nums[i]` $\leq 10^4$

### 思路一（堆）

二叉堆是一棵**完全二叉树**，且任意节点的优先级总是不小于其子节点。根据父节点与孩子节点的关系，可以分为大根堆和小根堆：

- 大根堆：父结点的权值总是**大于或等于**任何一个子结点的权值

- 小根堆：父结点的权值总是**小于或等于**任何一个子节点的权值

本题需要使用大根堆，可以使用手写二叉堆，也可以使用优先队列。

#### 时空复杂度

时间复杂度 $O(n \log n)$

空间复杂度 $O(n)$​

#### 代码

```C++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int> q;
        for (auto x : nums) {
            q.push(x);
        }
        for (int i = 1; i < k; i++) {
            q.pop();
        }
        return q.top();
    }
};
```

### 思路二（快速排序）

在快速排序中每次选择的分界值，其下标正好对应排序后的位置，可以通过改进快速排序算法解决问题。在快速排序的过程当中，我们会对子数组进行划分，如果划分后基准数 $\text{pivot}$ 所处位置 $q$ 正好是第 $k$ 小，则直接返回；如果 $q$​ 比目标下标小，就递归右子区间，否则递归左子区间。这样就可以把原来递归两个区间变成只递归一个区间，提高了时间效率。该算法又被称为「快速选择」算法。

在随机选择基准数的情况下，该算法期望时间复杂度为：
$$
O(\frac{n}{2} + \frac{n}{4} + \frac{n}{8} + \ldots) \approx O(n)
$$
在快速排序的三种实现中，三向切分快排表现最佳。

#### 时空复杂度

时间复杂度 $O(n)$

空间复杂度 $O(\log n)$​

#### 代码

```C++
class Solution {
public:
    int quick_sort(vector<int>& nums, int l, int r, int kth) {
        if (l == r) return nums[l];
        int pos = rand() % (r - l + 1) + l;
        swap(nums[l], nums[pos]);
        int i = l, k = l + 1, j = r;
        int pivot = nums[l];
        while (k <= j) {
            if (nums[k] < pivot) {
                swap(nums[k], nums[i]);
                i++;
                k++;
            } else if (nums[k] > pivot) {
                swap(nums[k], nums[j]);
                j--;
            } else {
                k++;
            }
        }
        if (i <= kth && kth <= j) return nums[i];
        if (kth < i) return quick_sort(nums, l, i - 1, kth);
        return quick_sort(nums, j + 1, r, kth);
    }

    int findKthLargest(vector<int>& nums, int k) {
        k = nums.size() - k;
        return quick_sort(nums, 0, nums.size() - 1, k);
    }
};
```

