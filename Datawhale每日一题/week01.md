Datawhale每日一题打卡 week01 

实现语言：C++

## 05-08
### lc.206 反转链表

[力扣](https://leetcode-cn.com/problems/reverse-linked-list/)

反转一个单链表。

**示例:**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

【思路】

两个指针 `pre` 和 `cur` 指向前后两个节点，让 `cur->next` 指向 `pre` ，再让 `pre = cur` ， `cur` 等于原来的 `cur->next` ，这样“交替”进行遍历完链表即可，最后返回 `pre`。

```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* pre = NULL;
    ListNode* cur = head;
    ListNode* tmp;

    while(cur) {
        tmp = cur->next;
        cur->next = pre;
        pre = cur;
        cur = tmp;
    }
    return pre;
}
```

时间复杂度： $O(n)$，空间复杂度： $O(1)$

### 面经每日一题：基于redis的分布式锁是如何实现的？



## 05-09
### lc. 21 合并两个有序链表

[力扣](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

**提示：**

- 两个链表的节点数目范围是 `[0, 50]`
- `100 <= Node.val <= 100`
- `l1` 和 `l2` 均按 **非递减顺序** 排列

【思路】

两个链表是有序的，由归并排序的思想可以想到：

1. 建立一个虚拟头节点 `dummy` ，让它保持不动，最后我们输出 `dummy->next` 即是合并后的链表的头节点；让 `p` 指向 `dummy` ；
2. 用两个指针分别指向两个链表的头节点，可以直接用 `l1` 和 `l2` ，比较其 `val` 的大小：

   1）若 `l1->val` 小，则令 `l1` 接到 `p` 的后面， `l1` 指针向后移一位；

   2）若 `l2->val` 小，同理；

   3）最后不要忘了将 `p` 后移一位，准备下一次“接”节点；直到遍历完链表1或者链表2，就终止遍历；

3. 判断链表1 或 链表2 是否存在没有遍历到的部分（只会有一个链表有剩余），若有，就直接将其接到 `p` 的后面，最后返回 `dummy->next`；

```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode* dummy = new ListNode(-1);
    ListNode* p = dummy;

    while(l1 && l2) {
        if(l1->val < l2->val) {
            p->next = l1;
            l1 = l1->next;
        }
        else{
            p->next = l2;
            l2 = l2->next;
        }
        p = p->next;
    }
    
    if(l1) p->next = l1;
    if(l2) p->next = l2;

    return dummy->next;
}
```

时间复杂度： $O(n)$，遍历一次 `l1` 和 `l2` ；空间复杂度： $O(1)$

### 面经每日一题：页面置换算法有哪些？

[页面置换算法有哪些？](https://github.com/nekomoon404/data-mining/blob/master/Datawhale%E6%AF%8F%E6%97%A5%E4%B8%80%E9%A2%98/%E9%9D%A2%E7%BB%8F%E6%AF%8F%E6%97%A5%E4%B8%80%E9%A2%98/2.%E9%A1%B5%E9%9D%A2%E7%BD%AE%E6%8D%A2%E7%AE%97%E6%B3%95%E6%9C%89%E5%93%AA%E4%BA%9B.md)

## 05-10
### lc.160 两个链表的第一个公共节点

[力扣](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

输入两个链表，找出它们的第一个公共节点。如下面的两个链表**：**

<div align=center>
<img src=https://github.com/nekomoon404/data-mining/blob/master/Datawhale%E6%AF%8F%E6%97%A5%E4%B8%80%E9%A2%98/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210510184955.png width=50% />
</div>

【思路】

解法比较巧妙，让两个指针 `p` 和 `q` 分别指向链表A和B的头节点，两个指针同时向后移，若 `p` 到达链表A的尾部空节点，就让它指向链表B的头节点； `q` 同理。这样若交点存在，当两个指针走过的距离是 ”a + b + c"时，它们一定会在交点相遇；当交点不存在时，两个指针走过距离“a + b" 时，都会指向空节点，即相等，跳出循环，返回 `p` 即返回空节点。

<div align=center>
<img src=https://github.com/nekomoon404/data-mining/blob/master/Datawhale%E6%AF%8F%E6%97%A5%E4%B8%80%E9%A2%98/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210510190043.jpg width=40% />
</div>

```cpp
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
    ListNode* p = headA;
    ListNode* q = headB;

    while(p != q) {
        if(p) p = p->next;
        else p = headB;

        if(q) q = q->next;
        else q = headA;
    }

    return p;
}
```
时间复杂度： $O(n)$，遍历一次链表1和链表2 ；空间复杂度： $O(1)$

### 面经每日一题：MySQL的引擎了解嘛？默认的是哪个？Innodb和Myisam的区别？

## 05-11
### lc.141 环形链表

[力扣](https://leetcode-cn.com/problems/linked-list-cycle/)

给定一个链表，判断链表中是否有环。

【思路】

快慢指针，慢指针 `slow` 指向头节点 `head` ，每次向后移动一位；快指针 `fast` 指向头节点，每次向后移动两位；若链表中有环存在，则快慢指针一定会相遇；若链表中无环存在，则快指针会离慢指针越来越远，直到走到尾部的空节点。

（有些题解是让快指针起始指向 `head->next` ，其实当有环存在时，快指针无论一开始指向哪里，快慢指针总能相遇。）

```cpp
bool hasCycle(ListNode *head) {
    ListNode* fast = head;
    ListNode* slow = head;

    while(fast && fast->next) {
        fast = fast->next->next;
        slow = slow->next;
        if(fast == slow)
            return true;
    }

    return false;
}
```

时间复杂度：$O(n)$ ；空间复杂度：$O(1)$

【为什么快慢指针一定可以相遇？快指针的步长为什么要为2？步长为3、4行不行？】

如下图，假设头节点到环入口距离是x，环入口到快慢指针第一次相遇点的距离是y，相遇点到环入口距离是z。

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/微信图片_20210513100302.png" width=50% />
</div>

设头节点的下标是0，每次移动1位（步长为1）的慢指针走了 $j$ 步，到了位置 `j` ， **$j$ 是 环的长度 y + z 的整数倍中满足 $j > x$ 的最小的那个数**；快指针每次移动 k 位（步长为k, k≥2）， 因此快指针此时已走过 $k * j$步，可以理解为快指针先走到位置 `j` ，又在环中走了 $(k-1) * j$步，因此 $j$ 是环长度的整数倍，所以快指针最终又走到了位置 `j` ，快慢指针相遇。

**可见，快指针的步长大于等于2时，都是可以和慢指针相遇的。**

时间复杂度可以看慢指针走过的步数 $j$，设链表中的节点个数为n。因为$j$是环的长度 y + z 的整数倍中满足 $j > x$ 的最小的那个数：

- 若x ≤ 环长，则 $j = y + z < n$;
- 若x > 环长，则 $j < 2 * x < 2 * n$

所以时间复杂度为 $O(n)$。

>参考：[为什么用快慢指针检测链表是否有环的时候，快指针的步长选择的是2，而不是3，4，5？](https://blog.csdn.net/xgjonathan/article/details/18034825))

### 面经每日一题：线性池了解吗？参数有哪些？任务到达线程池的过程？线程池的大小如何设置？

## 05-12
### lc.142 环形链表 II Linked List Cycle ii

[力扣](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

在判断链表中是否有环的基础上，还要找链表中的环的入口。

【判断链表是否有环】一个快指针每次走两步，一个慢指针每次走一步，两个指针都从头节点出发，若链表中有环，则它们必会在环内相遇。若遍历到 `fast == NULL || fast→next == NULL`时还没有相遇，说明链表中无环。

【如何找到环的入口】

如下图，假设头节点到环入口距离是x，环入口到快慢指针第一次相遇点的距离是y，相遇点到环入口距离是z。从快慢指针出发到它们第一次相遇：

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/微信图片_20210513100302.png" width=50% />
</div>

- 慢指针走过： `x + y` ；
- 快指针一定已经在环中了，它走过： `x + (y + z) * n + y` ；

又因为快指针走过的距离是慢指针走过的两倍： `x + (y + z) * n + y = 2 * (x + y)` ，移项得： `x = (y + z) * (n - 1) + z` 。

要找环的入口就是要知道x的大小，上面的式子表面，让一个指针（如原来的慢指针）从头节点开始走，一个指针从相遇点开始走，两个指针每次走一步，它们走过相同的距离时，即相遇时就在环的入口。

```cpp
ListNode *detectCycle(ListNode *head) {
    if(!head || !head->next)  return NULL;
    
    ListNode* fast = head;
    ListNode* slow = head;

    while(fast && fast->next) {
        fast = fast->next->next;
        slow = slow->next;
        if(fast == slow) {
            slow = head;
            while(slow != fast) {
                slow = slow->next;
                fast = fast->next;
            }
            return slow;
        }
    }

    return NULL;
}
```

时间复杂度： $O(n)$；空间复杂度： $O(1)$

### 面经每日一题：介绍MVCC


## 05-13
### lc.34 在排序数组中查找元素的第一个和最后一个位置

[力扣](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

一个升序的有序数组，有重复元素，给定一个target，求target在数组中的起始和结束位置，若不存在就返回 `{-1, -1}` 

这道题考察的主要是如何去写二分查找的 `check(nums[mid])` 函数，如何判断条件去找左右端点。

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/微信图片_20210513205824.png" width=80% />
</div>



```cpp
vector<int> searchRange(vector<int>& nums, int target) {
    if(nums.empty())  return {-1, -1};
    int l = 0, r = nums.size() - 1;
    while(l < r) {
        int mid = l + r >> 1;
        if(nums[mid] >= target)  r = mid;
        else l = mid + 1;
    }

    int left;
    if(nums[r] == target)  left = r, l = r, r = nums.size() - 1;
    else return {-1, -1};

    while(l < r) {
        int mid = l + r + 1 >> 1;
        if(nums[mid] <= target)  l = mid;
        else r = mid - 1; 
    }

    return {left, l};
}
```

时间复杂度：$O(\log n)$；空间复杂度：$O(1)$

>可以参考之前写的二分板子：[二分查找](https://nekomoon404.github.io/2020/09/29/%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80%EF%BC%881%EF%BC%89/)
<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/QQ图片20201003230519.jpg" width=70% />
</div>


### 面经每日一题：Java乐观锁机制，CAS思想？缺点？是否有原子性？