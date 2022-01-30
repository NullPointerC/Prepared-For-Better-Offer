[剑指 Offer 34. 二叉树中和为某一值的路径](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220130163333196-1097972764.png)

![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220130163338011-1619372442.png)

比较常见的回溯，但是回溯需要注意的小地方还是挺多的，特别是对于全局变量，因为它是所有栈空间共享的，所以当退出当前函数栈帧时，一定要将全局变量的栈帧恢复至入栈时刻的。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    private List<List<Integer>> ans = new ArrayList<>();
    private List<Integer> path = new ArrayList<>();
        public List<List<Integer>> pathSum(TreeNode root, int target) {
        backtrack(root, target, 0);
        return ans;
    }

    private void backtrack(TreeNode node, int target, int cur) {
        if (node == null) {
            return;
        }
        cur += node.val;
        path.add(node.val);
        if (cur == target && node.left == null && node.right == null) {
            ans.add(new ArrayList(path));
            // 这里是因为找到了一条路径,需要恢复现场
            cur -= node.val;
            path.remove(path.size() - 1);
            return;
        }
        backtrack(node.left, target, cur);
        backtrack(node.right, target, cur);
        // 这里是因为左右子树遍历好了,需要恢复现场
        cur -= node.val;
        path.remove(path.size() - 1);
    }
}
```