[剑指 Offer 26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220121173231239-589449901.png)
这题实在是坑太多了，稍不留心就掉进坑了。
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220121173257247-1092855292.png)
也是足足WA了3次并且参考了题解发现自己的思路一些小问题才最后能够AC。
这里要非常注意到题目给出的一个信息：**(约定空树不是任意一个树的子结构)**，再分析出现空树的情形。
①:A为空树B不为空树,此时B显然不可能是空树的一个子结构，返回false；
②:A不为空树B为空树,此时根据题意，B不能是任意一个树的子结构，所以也返回false;
③:A,B都为空树，此时空树也不能是空树的子结构，所以也返回false;
分析完空树的情形，再分析一般情况，我们需要遍历A树中的每一个节点，并以正在遍历的节点为新根，判断此时新树是否能够包含B，若包含，则说明是子结构。
那么再分析判断是否包含的辅助函数如何编写。还是需要先敲定根节点是否为空，如果B节点为空，那么显然能够被A所包含，否则A为空就不能包含B。
其他情形则判断根节点值是否相同，若相同，再判断左右子树是否能够包含。
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
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        // 已经约定空树不是任意一个树的子结构
        if(A == null && B == null) return false;
        if(A == null && B != null) return false;
        if(A != null && B == null) return false;
        return contains(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B);
    }
    // 以A为根节点的树是否包含有以B为根节点的树
    private boolean contains(TreeNode A, TreeNode B) {
        // A为空自然不能包含非空的树
        if(A == null && B != null) return false;
        // 非空的树默认包含有空树
        if(A != null && B == null) return true;
        // 二者都为空也可以认为A包含有B
        if(A == null && B == null) return true;
        if(A.val == B.val) {
            return contains(A.left, B.left) && contains(A.right, B.right);
        }
        return false;
    }
}
```
上述判断条件其实在编写的过程还是应该每一步说明清楚，等全部写完了再考虑把if判断精简，评论区很多大佬是代码很简洁，但是理解起来就没有那么好理解了，而且在面试的过程中，肯定是会略显紧张的，所以还是应该把每一个分支判断都写出来。
