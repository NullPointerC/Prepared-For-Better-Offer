[剑指 Offer 33. 二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220204011914022-1158989900.png)
我们首先需要注意到题目提供的是一颗BST树，所以我们可以知道这个性质，左子树 < 根节点 < 右子树。
又因为，题目给出的是后序遍历结果，所以也就是左子树->右子树->根节点。
因此我们可以尝试从后序遍历的尾部开始往前递归，但是，我们也需要知道左子树的长度和右子树的长度，我们每次取最后区间的一个节点作为root，从前往后遍历到第一个大于root的值的节点rNode，这个节点就是右子树的开始，设其索引为$rIdx$，因此，左子树范围就为$[l...rIdx - 1]$，右子树范围为$[rIdx...r - 1]$。
我们获取到右子树的范围后，需要判断右子树区间内的所有值是否都大于root，这样就判读好了$[l...r]$这段区间，接下来分治地判断剩余的空间，直到$l \ge r$，此时区间只有1个节点或者区间不存在，显然是正确的后续遍历序列。
```java
class Solution {
    public boolean verifyPostorder(int[] postorder) {
        int n = postorder.length;
        if(n < 2) {
            return true;
        }
        return check(postorder, 0, n - 1);
    }


    private boolean check(int[] postorder, int l, int r) {
        if(l >= r) {
            return true;
        }
        int rootVal = postorder[r];
        // 右子树的起始索引
        int rIdx = l;
        while(rIdx < r && postorder[rIdx] < rootVal) {
            rIdx++;
        }
        for(int i = rIdx; i < r; i++) {
            if(postorder[i] < rootVal) {
                return false;
            }
        }
        return true && check(postorder, l, rIdx - 1) && check(postorder, rIdx, r - 1);
    }
}
```
单调栈解法，我们观察后序遍历结果，左子树->右子树->根，对于BST而言，并没有什么规律，但是我们尝试将其反转，可以得到根->右子树->左子树，因此我们可以发现，这个序列应该是先递增后持续递减的序列，因为BST越往左值越小，越往右值越大，于是可以考虑单调栈的解法。
我们可以维护一个单调递增的栈，对于curVal > st.peek()的情况，我们知道它是右子树，将其入栈，否则，说明现在到了左子树，需要判断它是否比根节点要小，这时就需要用到单调栈来帮我们记录根节点。
```java
class Solution {
    public boolean verifyPostorder(int[] postorder) {
        Deque<Integer> stack = new ArrayDeque<>();
        int pre = Integer.MAX_VALUE;
        for(int i = postorder.length - 1; i >= 0; i--) {
            if(postorder[i] > pre) {
                return false;
            }
            while(!stack.isEmpty() && (postorder[i] < stack.peek())) {
                pre = stack.pop();
            }
            stack.push(postorder[i]);
        }
        return true;
    }
}
```