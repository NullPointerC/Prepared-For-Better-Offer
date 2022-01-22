[剑指 Offer 10- II. 青蛙跳台阶问题](https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220122210123328-1093446418.png)
和斐波那契数列一样的思路，这里有点不同的是，当台阶数为0的时候，有1中可达的方式，故而返回1。
```java
class Solution {
    public int numWays(int n) {
        int MOD = (int)(1e9 + 7);
        int a = 1, b = 2;
        if(n == 0) return a;
        if(n == 1) return a;
        if(n == 2) return b;
        int c = (a + b) % MOD;
        for(int i = 3; i <= n; i++) {
            c = (a + b) % MOD;
            a = b;
            b = c;
        } 
        return c;
    }
}
```