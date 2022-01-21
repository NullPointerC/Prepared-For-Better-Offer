[剑指 Offer 28. 对称的二叉树](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220121221902352-696949350.png)
太fw了，又是之前写了的题，结果又不会做，已经快要被打击得没有自信了🤣。之前也不知道是不是自己写得，用bfs写的，一开始也是想用bfs，结果没有一点头绪，只好转向dfs，结果dfs也不会写🤣。
还是在评论区看到了怎么分析这题，下定决心不能再混了，太fw了，做过的题反复不会做太不应该了😂。
首先我们考虑我们要判断是一根二叉树是否为对称的，那么就需要判断他的左右子树是否对称即可。那么这个判断过程就应该如下：
①.左子树或者右子树为空，此时当且仅当左子树等于右子树才能使得对称，否则不对称，即`p == q`;
②.左子树和右子树的值相同,
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220121222544154-2032312759.png)
③.左右子树不等,可退出,因为此时显然不满足对称条件
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
    public boolean isSymmetric(TreeNode root) {
        if(root == null) return true;
        return dfs(root.left, root.right);
    }

    private boolean dfs(TreeNode p, TreeNode q) {
        if(p == null || q == null) return p == q;
        if(p.val != q.val) return false;
        return dfs(p.left, q.right) && dfs(p.right, q.left);
    }
}
​```java
最早的想法就比较简单，就是想着根据题意，把二叉树反转一遍，再判断二者是否相等就好了，但是一定要注意，不能在原来的树上翻转，一定一定要新建过树，代码如下：
class Solution {
    // 最开始的思路 翻转成对称树 再比较
    // key point: 一定不能再原来的树上翻转！！！要新建一个树翻转！
    public boolean isSymmetric(TreeNode root) {
        TreeNode tmp = root;
        TreeNode mirrorRoot = mirrorTree(tmp);
        return isEquals(mirrorRoot, root);
    }

    private TreeNode mirrorTree(TreeNode root) {
        if (root == null) return null;
        // here ！！！
        TreeNode newRoot = new TreeNode(root.val);
        TreeNode tmp = root.left;
        newRoot.left = mirrorTree(root.right);
        newRoot.right = mirrorTree(tmp);
        return newRoot;
    }

    private boolean isEquals(TreeNode root1, TreeNode root2) {
        if (root1 == null || root2 == null) return root1 == root2;
        return root1.val == root2.val && isEquals(root1.left, root2.left)
                && isEquals(root1.right, root2.right);
    }
}
```