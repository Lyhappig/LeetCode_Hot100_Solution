## LRU 缓存

### 问题

请你设计并实现一个满足 **LRU (最近最少使用) 缓存** 约束的数据结构。

实现 `LRUCache` 类：

- `LRUCache(int capacity)` 以 **正整数** 作为容量 `capacity` 初始化 LRU 缓存
- `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，并将其设置为**最近才访问的键**，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value`，并将其设置为**最近才访问的键**；如果不存在，则向缓存中插入该组 `key-value`，并将其设置为**最近才访问的键**。如果插入操作导致关键字数量超过 `capacity` ，则应该 **逐出** 最久未使用的关键字。

函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。

提示：

- `1 <= capacity <= 3000`
- `0 <= key <= 10000`
- $0\leq$ `value` $\leq 10^5$
- 最多调用 $2 \times 10^5$ 次 `get` 和 `put`

### 思路（哈希表 + 双向链表）

采用**哈希表**和双向链表实现 `LRUCache`，设双向链表头部节点代表**最近使用节点**，尾部节点代表**最久未使用节点**

算法流程为：

- 插入键值对时，代表该数据为最新访问的数据
  - 若键值对已经存在于 `LRUCache`，则将它移动到链表头部
  - 若键值对不存在，则新增该数据到链表头部，并将键插入哈希表。若此时链表容量溢出，从哈希表删除尾部节点的键，并删去链表尾部的节点。
- 查询键值对时，代表该数据为最新访问的数据
  - 若在哈希表查询到键，则直接从哈希表访问数据
  - 若在哈希表未查询到键，则返回 `-1`

具体原理如下图所示：

<img src="https://pic.leetcode.cn/1696039105-PSyHej-146-3-c.png" alt="img" style="zoom: 33%;" />

#### 时空复杂度

时间复杂度 $O(1)$

空间复杂度 $O(n)$​

#### 代码

```C++
class DListNode {
public:
    int key;
    int value;
    DListNode *prev;
    DListNode *next;

    DListNode() {}

    DListNode(int key, int value) {
        this->key = key;
        this->value = value;
        prev = next = nullptr;
    }
};

class LRUCache {
private:
    int capacity;
    DListNode *dummy;
    unordered_map<int, DListNode*> mp;
public:

    LRUCache(int capacity) {
        this->capacity = capacity;
        dummy = new DListNode(-1, -1);
    }

    void move2Head(DListNode *now) {
        DListNode *head = dummy->prev;
        head->next = now;
        now->prev = head;
        now->next = dummy;
        dummy->prev = now;
    }

    void delNode(DListNode *now) {
        now->prev->next = now->next;
        now->next->prev = now->prev;
    }
    
    int get(int key) {
        if (!mp.count(key)) {
            return -1;
        }
        auto &now = mp[key];
        int value = now->value;
        if (now != dummy->prev) {
            delNode(now);
            move2Head(now);
        }
        return value;
    }
    
    void put(int key, int value) {
        if (mp.count(key)) {
            auto &now = mp[key];
            now->value = value;
            if (now != dummy->prev) {
                delNode(now);
                move2Head(now);
            }
            return;
        }
        DListNode *now = new DListNode(key, value);
        mp[key] = now;
        if (dummy->prev == nullptr) {
            dummy->prev = now;
            dummy->next = now;
            now->prev = dummy;
            now->next = dummy;
        } else {
            move2Head(now);
        }
        if (mp.size() > capacity) {
            mp.erase(dummy->next->key);
            delNode(dummy->next);
        }
    }
};
```

