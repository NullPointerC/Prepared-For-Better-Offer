[剑指 Offer 47. 礼物的最大价值](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220123235926188-343320947.png)
这里有一个好处在于所有值都是正的，所以处理起来不用像子数组一样处理和为负要重新选择，但是这里也是比较简单的。
可以用`dp[i][j]`记录我们走到`[i][j]`位置时所能获得的礼物最大价值，那么很显然，我们到达`[i][j]`位置有2种方式，从`[i-1][j]`即从上往下或是从`[i][j-1]`即从左往右到达，那么我们选择这两种方式到达的最大价值的那条，故而`dp[i][j] = grid[i][j] + Math.max(dp[i - 1][j], dp[i][j - 1])`。同时一直维护ans即可。
```java
class Solution {
    public int maxValue(int[][] grid) {
        int m = grid.length;
        if(m == 0) return 0;
        int n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        int ans = dp[0][0];
        for(int i = 1; i < n; i++) {
            dp[0][i] = dp[0][i - 1] + grid[0][i];
            ans = Math.max(dp[0][i], ans);
        }
        for(int i = 1; i < m; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
            ans = Math.max(dp[i][0], ans);
        }
        for(int i = 1; i < m; i++) {
            for(int j = 1; j < n; j++) {
                int currentVal = grid[i][j];
                int preVal = Math.max(dp[i - 1][j], dp[i][j - 1]);
                dp[i][j] = currentVal + preVal;
            }
        }
        return dp[m - 1][n - 1];
    }
}
```