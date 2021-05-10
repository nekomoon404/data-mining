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

面经每日一题：

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
