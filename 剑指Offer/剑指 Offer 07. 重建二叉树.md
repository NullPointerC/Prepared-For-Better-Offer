[剑指 Offer 07. 重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220203233856110-1954145169.png)

这里主要是要往分治上想,并且联系到中序序列和前序序列的关系。
我们知道中序序列，对于val而言，出现在val左边的值都在它的左子树上，出现在右侧的值都在它的右子树上。
那么我们考虑，遍历中序序列，将中序序列的值和其出现的索引位置映射，这样，我们就能比较容易的分割开左子树和右子树。
我们获取到值val在中序序列中的索引idx，假定我们选取的这一段中序序列区间为$[l...r]$，那么左子树的长度len为$idx - l$。
由于同一颗子树的前序遍历长度和中序遍历长度一定是一样的，因此我们可以把所求得的长度len运用到前序遍历的结果中去。
利用以下三个结论：
①.前序遍历的首元素 为 树的根节点 node 的值。
②.在中序遍历中搜索根节点 node 的索引 ，可将 中序遍历 划分为 [ 左子树 | 根节点 | 右子树 ] 。
③.根据中序遍历中的左（右）子树的节点数量，可将 前序遍历 划分为 [ 根节点 | 左子树 | 右子树 ] 。
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220203235912542-507470.png)

因此代码如下所示：
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
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        // 存储中序遍历的节点和索引关系
        Map<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return build(preorder, 0, preorder.length - 1, inorder, 0, inorder.length - 1, map);
    }

    private TreeNode build(int[] preorder, int preStart, int preEnd, int[] inorder, int inStart, int inEnd, Map<Integer, Integer> map) {
        if(preStart > preEnd || inStart > inEnd) {
            return null;
        }
        int rootVal = preorder[preStart];
        TreeNode root = new TreeNode(rootVal);
        // 获取root在中序遍历结果中的位置
        int midIndex = map.get(rootVal);
        // 获取左子树的长度
        int leftLength = midIndex - inStart;
        root.left = build(preorder, preStart + 1, preStart + leftLength, inorder, inStart, midIndex - 1, map);
        root.right = build(preorder, preStart + leftLength + 1, preEnd, inorder, midIndex + 1, inEnd, map);
        return root;
    }


}
```