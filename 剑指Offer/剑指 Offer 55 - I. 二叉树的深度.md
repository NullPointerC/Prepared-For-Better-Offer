[剑指 Offer 55 - I. 二叉树的深度](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220201161630486-1602950432.png)
比较常见的递归,但是可能在面试过程中会要求要写非递归写法。
递归写法:
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
    public int maxDepth(TreeNode root) {
        return deep(root);
    }

    private int deep(TreeNode node) {
        if(node == null) return 0;
        return 1 + Math.max(deep(node.left), deep(node.right));
    }
}
```
时间复杂度$O(logn)$，空间复杂度$O(1)$，其中n为节点数量，最多遍历$logn$层。
非递归写法：
①.层次遍历
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
    public int maxDepth(TreeNode root) {
        int ans = 0;
        if(root == null) {
            return ans;
        }
        Queue<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        while(!q.isEmpty()) {
            var size = q.size();
            for(int i = 0; i < size; i++) {
                var node = q.poll();
                if(node.left != null) {
                    q.offer(node.left);
                }
                if(node.right != null) {
                    q.offer(node.right);
                }
            }
            ans++;
        }
        return ans;
    }
}
```
②.非递归dfs
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
    public int maxDepth(TreeNode root) {
        if(root == null) {
            return 0;
        }
        // 这里不仅要存节点,还需要存节点的高度
        Stack<Pair<TreeNode, Integer>> st = new Stack<>();
        int depth = 0, max = 0;
        TreeNode cur = root;
        while(!st.isEmpty() || cur != null) {
            while(cur != null) {
                st.push(new Pair<>(cur, ++depth));
                cur = cur.left;
            }
            var top = st.pop();
            cur = top.getKey();
            depth = top.getValue();
            max = Math.max(max, depth);
            cur = cur.right;
        }
        return max;
    }
}
```