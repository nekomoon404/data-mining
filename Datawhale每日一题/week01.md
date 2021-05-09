Datawhale每日一题打卡 week01 实现语言：C++

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

1. 建立一个虚拟头节点 `dummy` ，让它保持不动，最后我们输出 `dummy->next` 即是合并后的链表；让 `p` 指向 `dummy` ；
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
