[剑指 Offer 25. 合并两个排序的链表](https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220126232635867-709734757.png)
双路归并即可, 总的时间复杂度为O(n),关键在于怎么优化空间,可以是O(n),也可以是O(1)。
这题可以利用给好的空间在原空间上操作，可以进一步优化空间，每次new出新空间比较费空间，因为也是存一样的数字，没必要new出空间来了。
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1), tail = dummy;
        // 两个都不为空时,采用双路归并将较小的那个添加进入
        while(l1 != null && l2 != null) {
            if(l1.val < l2.val) {
                tail.next = l1;
                l1 = l1.next;
            } else {
                tail.next = l2;
                l2 = l2.next;
            }
            tail = tail.next;
        }
        if(l1 != null) tail.next = l1;
        if(l2 != null) tail.next = l2;
        return dummy.next;
    }
}
```
用一个tail指针指向链表的尾节点，每次将归并过程中较小的那个链接至tail后面，再更新正在遍历的指针和尾节点即可。