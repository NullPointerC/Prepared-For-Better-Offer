[剑指 Offer 10- I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220122203937235-197616112.png)
这里需要注意的地方有两个:
①.这里要求的是第n项,但是有有第一项是F(0),所以也就是说要求的第n项也就是F(n-1);
②.对于结果需要对1e9+7取模;
所以写代码的时候需要注意,循环多少次,从哪里开始循环,循环的过程中怎么取模。
我们要a代表F(n-2)，b代表F(n-1)，可以令c = F(n) = F(n - 1) + F(n - 2) = a + b;
此时再进行下一次循环时,F(n-2)就变为了上一次的F(n-1)，F(n-1)就变为了上一次的F(n)，故而有a = b, b = c;
且每一次求和后，需对c进行取模,即c % MOD。
```java
class Solution {
    private static final int MOD = (int)(1e9+7);
    public int fib(int n) {
        int a = 0, b = 1;
        if(n == 0) return a;
        if(n == 1) return b;
        int c = (a + b) % MOD;
        for(int i = 2; i <= n; i++) {
            c = (a + b) % MOD;
            a = b;
            b = c;
        }
        return c;
    }
}
```