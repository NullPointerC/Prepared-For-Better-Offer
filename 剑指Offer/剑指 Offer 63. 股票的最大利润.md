[剑指 Offer 63. 股票的最大利润](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220122211451187-1883646742.png)
正经的dp做法应该是用dp[i]记录第i天售出股票所能获取的最大利润,然后从第i天起往前遍历,如果前面的购入价`prices[j]`有比今天的售价`prices[i] = cur`更低的,那么我们就尝试出售,并将获取的利润`cur - prices[j]`作为利润和`dp[i]`进行比较更新,最后取`dp[i]`中的最大值即可。
```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        // dp[i]为在第i天时卖出可以得到的最大利润
        int[] dp = new int[n + 1];
        int ans = Integer.MIN_VALUE;
        for(int i = 0; i < n; i++) {
            int cur = prices[i];
            for(int j = i; j >= 0; j--) {
                if(cur > prices[j]) dp[i] = Math.max(dp[i], cur - prices[j]);
            }
            ans = Math.max(ans, dp[i]);
        }
        return ans == Integer.MIN_VALUE ? 0 : ans;
    }
}
```
时间复杂度为$O(n^2)$，空间复杂度为$O(n)$。
也有不那么正经的贪心做法，即我们尝试从第一天开始起，记录下最小的购入价`minBuy`，并以当前价`prices[i]` - 购入价`minBuy`得到利润，维护最大的利润和最小的购入价。
```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        int ans = Integer.MIN_VALUE;
        int minBuy = Integer.MAX_VALUE;
        for(int i = 0; i < n; i++) {
            minBuy = Math.min(minBuy, prices[i]);
            ans = Math.max(prices[i] - minBuy, ans);
        }
        return ans == Integer.MIN_VALUE ? 0 : ans;
    }
}
```
贪心的做法时间复杂度为$O(n)$，空间复杂度为$O(1)$。但是可能没有对题目特比熟悉的话，在面试中并不是那么容易想到。
