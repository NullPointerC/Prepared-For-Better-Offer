[剑指 Offer 04. 二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/ "剑指 Offer 04. 二维数组中的查找")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220216203800096-1855492975.png)
首先需要注意到题目下方的数据范围，`n`和`m`可能是0，所以当`n`等于0时，需要及时返回`false`，因为此时矩阵都不存在了，所以也就不存在有`target`了。
这里还需要注意到题目上说到的是，从左到右依次递增，从上到下依次递增，所以我们取左上角作为中点进行二分，当当前位置`(curX,curY)`的值等于要找的值`target`时，我们返回`true`即可，
否则若更小时，说明需要往大的方向上去找，就应该往下继续找`curX`需要加1，否则说明要往小的地方找，`curY`需要减1。
```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        int n = matrix.length;
        if(n == 0) {
            return false;
        }
        int m = matrix[0].length;
        // 左上角才可以代表中点
        int curX = 0, curY = m - 1;
        while(curX <= n - 1 && curY >= 0) {
            if(matrix[curX][curY] == target) {
                return true;
            } else if(matrix[curX][curY] > target) {
                // 往更小的地方去找
                curY--;
            } else if(matrix[curX][curY] < target) {
                curX++;
            }
        }
        return false;
    }
}
```