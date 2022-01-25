[剑指 Offer 22. 链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220125151450225-286910397.png)
一个比较简单的办法是先遍历一趟，得到长度`l`后，发现如下关系，倒数第1个节点为第`l - 1`个节点，倒数第2个节点为第`l - 2`个节点，故返回第`l - k`个节点即可。
正着数也就是`l - k - 1`索引位置处，故而遍历条件为`i <= l - k - 1`或`i < l - k`都可。
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
    public ListNode getKthFromEnd(ListNode head, int k) {
        int l = getLength(head);
        ListNode newHead = head;
        for(int i = 0;i < (l - k);i++) {
            newHead = newHead.next;
        }
        return newHead;
    }
    private int getLength(ListNode head) {
        int l = 0;
        while(head != null) {
            head = head.next;
            l++;
        }
        return l;
    }
}
```
也可以用栈来存，再依次将k - 1个元素出栈即可，所以遍历条件为`i = 0; i < k - 1`或者是`i = 1; i <= k - 1`。
最后返回栈顶节点。
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
    public ListNode getKthFromEnd(ListNode head, int k) {
        Stack<ListNode> st = new Stack<>();
        ListNode p = head;
        while(p != null) {
            st.push(p);
            p = p.next;
        }
        for(int i = 1; i < k; i++) {
            st.pop();
        }
        return st.peek();
    }
}
```
还可以使用快慢指针的方式，可以看出倒数第i个距离最后的null指针域长度为i,所以倒数第k个节点距离null的长度为k，可以先让快指针走k步，在让快慢指针同时出发，如果快指针走到了null位置，此时slow指针正好距离null指针有k个长度，此时返回慢指针即可。
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
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode fast = head, slow = head;
        for(int i = 1; i <= k; i++) {
            fast = fast.next;
        }
        while(fast != null) {
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }
}
```