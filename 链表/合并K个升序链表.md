## 合并 K 个升序链表

### 问题

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

提示：

- `k == lists.length`
- $0\leq$ `k` $\leq 10^4$
- `0 <= lists[i].length <= 500`
- $-10^4\leq $ `lists[i][j]` $\leq 10^4$
- `lists[i]` 按 **升序** 排列
- `lists[i].length` 的总和不超过 $10^4$

### 思路一（分治）

在合并两个有序链表问题的基础上，可以逐步合并链表，共需要合并 $n - 1$ 次，时间复杂度 $O(k^2 n)$

使用分治的方法进行合并：

- 将 $k$ 个链表配对并将同一对中的链表合并；
- 第一轮合并后 $k$ 个链表被合并成了 $k / 2$ 个链表，平均长度为 $2 n / k$，然后合并 $k/4$ 个链表，$k/8$ 个链表，以此类推；
- 重复这一过程，直到得到了最终的有序链表；

![img](https://pic.leetcode-cn.com/6f70a6649d2192cf32af68500915d84b476aa34ec899f98766c038fc9cc54662-image.png)

#### 时空复杂度

时间复杂度 $O(kn \times \log k)$

空间复杂度 $O(\log k)$​

#### 代码

```C++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode *a, ListNode *b) {
        if ((!a) || (!b)) return a ? a : b;
        ListNode head, *tail = &head, *aPtr = a, *bPtr = b;
        while (aPtr && bPtr) {
            if (aPtr->val < bPtr->val) {
                tail->next = aPtr; aPtr = aPtr->next;
            } else {
                tail->next = bPtr; bPtr = bPtr->next;
            }
            tail = tail->next;
        }
        tail->next = (aPtr ? aPtr : bPtr);
        return head.next;
    }

    ListNode* merge(vector <ListNode*> &lists, int l, int r) {
        if (l == r) return lists[l];
        if (l > r) return nullptr;
        int mid = (l + r) >> 1;
        return mergeTwoLists(merge(lists, l, mid), merge(lists, mid + 1, r));
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return merge(lists, 0, lists.size() - 1);
    }
};
```

### 思路二（堆）

维护 $k$ 个指针，每个指针初始指向 $k$ 个链表的头部：

- 对于当前所有 $k$ 个指针的最小值，一定是合并后链表的下一个元素
- 因为每个链表都是有序的，所以最小值指针向后移动一步
- 重复上述过程直到不重不漏地合并完所有链表

快速动态求出 $k$ 个数的最小值，可以借助堆在 $O(\log k)$ 的时间内完成。

#### 时空复杂度

时间复杂度 $O(k n \times \log k)$

空间复杂度 $O(k)$​

#### 代码

```C++
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        auto cmp = [](ListNode* x, ListNode *y) {
            return x->val > y->val;
        };

        priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> pq;
        int n = lists.size();
        for(int i = 0; i < n; i++) {
            if(lists[i] != nullptr)
                pq.push(lists[i]);
        }

        ListNode *head = new ListNode();
        ListNode *now = head;
        
        while(!pq.empty()) {
            auto cur = pq.top();
            pq.pop();
            if(cur->next != nullptr) {
                pq.push(cur->next);
            }
            now->next = new ListNode(cur->val);
            now = now->next;
        }
        head = head->next;
        return head;
    }
};
```

