[剑指 Offer 68 - II. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220202232538510-742011153.png)
同理，由于给定的是二叉树，所以只有两个方向上的选择，对于p,q的公共祖先，如果p,q分别在`root`的一左一右，那么显然root就是最近的公共祖先，否则，就只有在同一侧了，如果在哪侧，就继续去遍历那一侧即可。
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null || root == p || root == q) {
            return root;
        }
        var left = lowestCommonAncestor(root.left, p, q);
        var right = lowestCommonAncestor(root.right, p, q);
        if(left != null && right != null) {
            return root;
        } else if(left != null && right == null) {
            return left;
        } else if(left == null && right != null){
            return right;
        } else {
            return null;
        }

    }
}
```
时间复杂度平均为$O(logn)$，最差退化到链表，$O(n)$，空间复杂度为$O(1)$。
