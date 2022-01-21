[剑指 Offer 27. 二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220121212922617-931360805.png)
感觉自己最近是咋了，好多以前写过的题都过了一段时间又拿起来做就又不会了😅。给👴整笑了，一开始就只想到了用bfs，再搜索每一层的时候反转过来，结果去翻自己之前写过的代码，用dfs写那么简单😂。
## dfs解法
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
    public TreeNode mirrorTree(TreeNode root) {
        return dfs(root);
    }

    private TreeNode dfs(TreeNode node) {
        if(node == null) {
            return node;
        }
        TreeNode tmp = node.left;
        node.left = node.right;
        node.right = tmp;
        dfs(node.left);
        dfs(node.right);
        return node;
    }
}
```
## bfs解法
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
    public TreeNode mirrorTree(TreeNode root) {
        if(root == null) {
            return root;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);
        while(!queue.isEmpty()) {
            int size = queue.size();
            for(int i = 0; i < size; i++) {
                TreeNode peek = queue.poll();
                TreeNode tmp = peek.right;
                peek.right = peek.left;
                peek.left = tmp;
                if(peek.left != null) queue.offer(peek.left);
                if(peek.right != null) queue.offer(peek.right);
            }
        }
        return root;
    }
}
```
都比较简单，并不太难，主要要对树的dfs和bfs比较了解，清楚每种写法的注意事项。