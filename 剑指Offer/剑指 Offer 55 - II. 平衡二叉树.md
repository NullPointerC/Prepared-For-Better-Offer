[剑指 Offer 55 - II. 平衡二叉树](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220201164035235-1887296401.png)

我们注意到题目要求我们验证所有的节点是否平衡，所以需要遍历到每一个节点，这里使用前序遍历的方式来遍历到每一个节点。
并且，对于每一个节点，需要满足的条件是左右子树高度差不超过1且左右子树均是平衡的。
所以可以写出如下代码：
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
    public boolean isBalanced(TreeNode root) {
        return check(root);
    }

    // 检测某个节点是否是平衡的
    private boolean check(TreeNode root) {
        if(root == null) {
            return true;
        }
        var lDepth = getDepth(root.left);
        var rDepth = getDepth(root.right);
        return Math.abs(lDepth - rDepth) <= 1 && check(root.left) && check(root.right);
    }

    // 得到某个节点的深度
    private int getDepth(TreeNode root) {
        if(root == null) {
            return 0;
        }
        return 1 + Math.max(getDepth(root.left), getDepth(root.right));
    }
}
```
这个方法虽然好，但是可以发现有着许多重复计算在求`depth`时，getDepth函数对于许多节点有重复计算。
时间复杂度为$O(nlogn)$，最差可能有n层，每一层的复杂度为$O(logn)$。
接下来就是考虑后序遍历，后序遍历是自底向上，可以避免一些重复计算，而前序遍历是自顶向下，中间会有很多重复计算。
后序遍历则是，对于每一个节点，我们判断其左右子树是否平衡，如果平衡，继续返回这个节点的高度，否则返回一个退出的flag值，这里设置成-1。
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
    public boolean isBalanced(TreeNode root) {
        return check(root) != -1;
    }
    // 检测node为根的子树是否平衡,平衡返回node的高度,不平衡返回-1
    private int check(TreeNode node) {
        if(node == null) {
            return 0;
        }
        int leftHeight = check(node.left);
        if(leftHeight == -1) {
            return -1;
        }
        int rightHeight = check(node.right);
        if(rightHeight == -1) {
            return -1;
        }
        if(Math.abs(leftHeight - rightHeight) <= 1) {
            return 1 + Math.max(leftHeight, rightHeight);
        } else {
            return -1;
        }
    }
}
```