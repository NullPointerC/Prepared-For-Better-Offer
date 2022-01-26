[剑指 Offer 52. 两个链表的第一个公共节点](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220126234205557-768790196.png)
这里是一个比较典型的追及与相遇问题,假设公共段长度为l,链表A的总长度为a,单独段长度为a - l,链表B的总长度为b,单独段的长度为b - l,假如把a和b都看做弯曲并弯曲成圆形.
假设p和q分别从A,B的头结点开始追及,且两人速度一致,每次只走1格,由于二者长度不一定一致,若令p走完后继续走B,q走完后继续走A,
可以得到在某个时刻,两个人走过的流程一定是一致的
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220126235350517-2144759256.png)
下面的图片可能描述的更好😂:
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220126235504907-1500811504.png)
所以使用双指针解法:
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode p = headA, q = headB;
        while(p != q) {
            if(p != null) {
                p = p.next;
            } else {
                p = headB;
            }
            if(q != null) {
                q = q.next;
            } else {
                q = headA;
            }
        }
        return p;
    }
}
```
O(n)的时间复杂度,O(1)的空间复杂度。