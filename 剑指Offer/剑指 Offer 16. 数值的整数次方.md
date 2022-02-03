[剑指 Offer 16. 数值的整数次方](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220204004142157-120978110.png)

自然最容易想到的就是枚举了，枚举1-n，这里有个细节就是要判断n的正负，并且n也取到了Integer.MIN_VALUE,若是将其反转，就会爆int😂，我也因此WA了好多发。
```java
class Solution {
    public double myPow(double x, int n) {
        double res = 1.0;
        if(n > 0) {
            for(int i = 1; i <= n; i++) {
                res *= x;
            }
        } else if (n < 0) {
            for(int i = 1; i <= -n; i++) {
                res /= x;
            }
        } 
        return res;
    }
}
```
O(n)枚举，在一些特殊情况下过不了，比如Integer.MAX_VALUE，数据达到了1e9，O(n)的方式就不太行了。
还有就是可以二分的求，对于$x^n$，我们可以看成$x^{\frac{n}{2} + \frac{n}{2}} = x^{\frac{n}{2}} \times x^{\frac{n}{2}}$
这里还有一些细节就是对于奇偶数需要额外判断，因为如果是$n$为奇数，那么$\frac{n}{2}$就会是$\frac{n-1}{2}$的结果，会漏掉1，因此需要额外加上去。
并且使用二分的好处在于不用担心会爆$int$，$n$每次进行的都是$/2$操作，对于正负数都是一样的。
```java
class Solution {
    public double myPow(double x, int n) {
        if(n == 0) {
            return 1.0;
        }
        if(n == 1) {
            return x;
        }
        if(n == -1) {
            return 1 / x;
        }
        double half = myPow(x, n / 2);
        double mod = myPow(x, n % 2);
        if(n % 2 == 1) {
            return half * half * mod;
        } else {
            return half * half;
        }
    }
}
```
快速幂的解法需要解决爆int的问题，这里可以进入函数后用long换掉传入的n。
```java
class Solution {
    public double myPow(double x, int n) {
        double res = 1.0;
        if(n == 0) {
            return res;
        }
        long nn = n;
        if(nn < 0) {
            x = 1 / x;
            nn = -nn;
        }
        while(nn != 0) {
            if((nn & 1) != 0) {
                res *= x;
            }
            x *= x;
            nn >>= 1;
        }
        return res;
    }
}
```
这里在进入后就需要判断是否可以换掉负的幂。
