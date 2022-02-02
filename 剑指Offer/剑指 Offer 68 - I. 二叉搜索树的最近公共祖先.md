[剑指 Offer 68 - I. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220202224948471-2139306337.png)
注意到这里给出的树是一颗BST树，所以满足有序条件，对于p,q两个节点来说，要找公共祖先且要求深度足够深，所以自然是从root开始找，如果p,q分别位于root的两侧，自然可以说明root是p,q的最近公共祖先，否则，则需要判断p,q是否分别位于root的同一侧，这时就可以通过值来判断，如果p,q的值都比root小，说明此时需要去左子树找公共祖先，否则说明在右子树找公共祖先。
代码如下，这里判断是否位于同一侧用的是做差相减，可以归并几种情况，代码更简洁高效。
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
    private TreeNode ancestor;
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        find(root, p, q);
        return ancestor;
    }
    private void find(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null || p == null || q == null) {
            ancestor = root;
            return ;
        }
        // 取出各节点的值
        int pVal = p.val, qVal = q.val, rVal = root.val;
        // 计算p,q两节点与root的差判断是否在同侧
        int diff = (rVal - pVal) * (rVal - qVal);
        // 如果乘积小于0,说明root在p和q的中间,这时可以保证深度最深,所以直接返回root
        if(diff <= 0) {
            ancestor = root;
            return ;
        } else {
            // 乘积小于0,说明p,q在root的同一侧
            if(pVal < rVal) {
                // 如果p的值比root的值小,说明需要去坐标找公共祖先
                find(root.left, p, q);
            } else {
                find(root.right, p, q);
            }
        }
    }
}
```
时间复杂度$O(logn)$，空间复杂度$O(1)$。