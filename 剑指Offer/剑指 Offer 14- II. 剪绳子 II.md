[剑指 Offer 14- II. 剪绳子 II](https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/ "剑指 Offer 14- II. 剪绳子 II")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220214154323814-1021812563.png)

这里需要注意到数据范围已经从Ⅰ的1-58更新至1000，且需要对1e9+7取模运算，所以这里已经不能再使用dp了，因为dp需要使用max，所以不能比较出后来的和之前的哪个大，这里只能使用数学方法了，即整数定理，可以把任何一个整数分成是2和3的和，且3比2更解决e，所以需要不断把n减去3，直到n不大于4时。
```java
class Solution {
    private static final int MOD = (int)(1e9+7);
    public int cuttingRope(int n) {
        if(n < 4) {
            return n - 1;
        }
        long res = 1;
        while(n > 4) {
            n -= 3;
            res = (res * 3 % MOD);
        }
        return (int)(res * n % MOD);
    }
}
```