[剑指 Offer 36. 二叉搜索树与双向链表](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220130170248691-1490930418.png)

![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220130170252795-1256421575.png)

![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220130170302854-164628537.png)

如果不考虑就地转换的话，可以注意到题目给出的二叉搜索树的条件，将所有的节点按照中序遍历的顺序添加进`list`中，再从头开始将`right`指针链好，再从尾部开始将`left`指针链好，再将头尾链好即可。

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val,Node _left,Node _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {
    public Node treeToDoublyList(Node root) {
        if(root == null) return root;
        List<Node> list = new ArrayList<>();
        traverse(root, list);
        for(int i = 0; i < list.size() - 1; i++) {
            list.get(i).right = list.get(i + 1);
        }
        for(int i = list.size() - 1; i > 0; i--) {
            list.get(i).left = list.get(i - 1);
        }
        list.get(0).left = list.get(list.size() - 1);
        list.get(list.size() - 1).right = list.get(0);
        return list.get(0);
    }
    private void traverse(Node node, List<Node> list) {
        if(node == null) return ;
        traverse(node.left, list);
        list.add(node);
        traverse(node.right, list);
    }
}
```
当然这种方式是可以AC的，但是空间复杂度为$O(n)$，所以我们可以考虑如何在原地将链表串好。
其实思路也是中序遍历，但是需要注意到，我们操作一个节点时，还需要之前他的前一个节点，所以用pre来存储前一个节点，并有`pre.right = cur`和`cur.left = pre`这么两个过程来链接链表。
最后还需要把首尾链接起来。

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val,Node _left,Node _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {
    Node pre, head;
    public Node treeToDoublyList(Node root) {
        if(root == null) return root;
        traverse(root);
        pre.right = head;
        head.left = pre;
        return head;
    }

    private void traverse(Node cur) {
        if(cur == null) {
            return ;
        }
        traverse(cur.left);
        // 第一个节点
        if(pre == null) {
            head = cur;
        } else {
            pre.right = cur;
            cur.left = pre;
        }
        pre = cur;
        traverse(cur.right);
    }
}
```