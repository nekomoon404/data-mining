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
