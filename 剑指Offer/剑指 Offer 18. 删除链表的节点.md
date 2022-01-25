[剑指 Offer 18. 删除链表的节点](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220125151026999-41819594.png)
一开始还以为是那题，给定了结点，删除该节点的题目，但是后来发现两题有些不同，那题说明了要删除的节点一定不是尾节点，而这里没有保证，所以使用双指针即可。
pre值要删除节点的前一个节点，cur指向要删除的节点。找到要删除的节点后，将前一个节点的next指针指向要删除节点的下一个即可。
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
    public ListNode deleteNode(ListNode head, int val) {
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode pre = dummy, cur = head;
        while(cur.val != val) {
            pre = cur;
            cur = cur.next;
        }
        pre.next = cur.next;
        return dummy.next;
    }
}
```