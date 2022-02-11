[剑指 Offer 37. 序列化二叉树](https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/ "剑指 Offer 37. 序列化二叉树")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220211194346346-110033532.png)

这里想到的比较容易的方式是使用层序遍历的结果来序列化,再利用层序遍历的方式来反序列树。
但是需要注意的是，由于我们要序列化的树是唯一的，所以我们需要标记空节点。
并且，我们将序列化的结果按照顺序加入到数组中，查看根节点和子树的关系。
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220211194652925-2067858246.png)
对于某个节点node,它在序列中的位置确定好后，就可以确定好子树的索引。
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
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder res = new StringBuilder();
        if(root != null) {
            Queue<TreeNode> q = new ArrayDeque<>();
            q.offer(root);
            res.append(root.val);
            res.append(",");
            while(!q.isEmpty()) {
                int n = q.size();
                for(int i = 0; i < n; i++) {
                    TreeNode node = q.poll();
                    if(node.left != null) {
                        res.append(node.left.val);
                        res.append(",");
                        q.offer(node.left);
                    } else {
                        res.append("#,");
                    }
                    if(node.right != null) {
                        res.append(node.right.val);
                        res.append(",");
                        q.offer(node.right);
                    } else {
                        res.append("#,");
                    }
                }
            }
        }
        // System.out.println(Arrays.toString(res.toString().split(",")));
        return res.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if(data == null || data.length() == 0) {
            return null;
        }
        String[] vals = data.split(",");
        if(vals == null || vals.length == 0) {
            return null;
        } else {
            int idx = 0;
            Queue<TreeNode> q = new ArrayDeque<>();
            TreeNode root = new TreeNode(Integer.parseInt(vals[idx]));
            q.offer(root);
            idx += 1;
            while(!q.isEmpty() && idx < vals.length) {
                TreeNode node = q.poll();
                if(!vals[idx].equals("#")) {
                    node.left = new TreeNode(Integer.parseInt(vals[idx]));
                    q.offer(node.left);
                } else {
                    node.left = null;
                }
                if(idx + 1 < vals.length && !vals[idx +1].equals("#")) {
                    node.right = new TreeNode(Integer.parseInt(vals[idx + 1]));
                    q.offer(node.right);
                } else {
                    node.right = null;
                }
                idx += 2;
            }
            return root;
        }
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));
```