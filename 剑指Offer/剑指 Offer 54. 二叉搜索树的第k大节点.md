[剑指 Offer 54. 二叉搜索树的第k大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220130183942315-1141670664.png)

由于是BST，那么我们很容易想到将中序遍历的结果取第k大的数即可。
所以我们先用一个list把中序遍历得到的结果存储起来，再从中取第k大的那个即可。
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
    List<Integer> path = new ArrayList<>();
    public int kthLargest(TreeNode root, int k) {
        traverse(root);
        return path.get(path.size() - k);
    }

    private void traverse(TreeNode node) {
        if(node == null) {
            return ;
        }
        traverse(node.left);
        path.add(node.val);
        traverse(node.right);
    }
}
```

上述算法的时间复杂度为$O(lgn)$，n个节点个数，空间复杂度为$O(n)$，我们可以发现，其实很多节点是不用存储的，并且list新建和插入比较耗时，于是考虑如何降低空间，我们用一个`cnt`表示当前是遍历到第`cnt`个，每遍历一个cnt对应相加，直到`cnt = k`，返回ans即可。
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
    int ans, cnt;
    public int kthLargest(TreeNode root, int k) {
        dfs(root, k);
        return ans;
    }

    private void dfs(TreeNode node, int k) {
        if(node == null) return ;
        dfs(node.right, k);
        cnt++;
        if(cnt == k) {
            ans = node.val;
            return ;
        }
        dfs(node.left, k);
    }
}
```