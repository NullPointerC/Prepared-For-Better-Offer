![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220215234147184-2073112906.png)
太恶心了这题,处理边界处理了半天,这里提到不能用`*`,`/`,`%`,所以就考虑了使用减法来模拟除法。
思路是首先需要做特判,比如`a`被除数等于0,可以直接返回,`b`除数等于0,则不是有效的除法运算,需要抛异常。
还有当`a`为最小整数，b为`-1`时，二者相除会得到超过整数最大值的数，需要返回`Integer.MAX_VALUE`。
接下来就是当`b`被除数等于1时，也可以直接返回`a`即可。剩下的情况就是正常的情况，可以不断做差，做差需要使用绝对值之差来判断，如果二者绝对值之差大于等于0，那么说明还可以继续执行除法，否则说明不能整除，可以直接退出。
```java
class Solution {
    public int divide(int a, int b) {
        if(a == 0) {
            return 0;
        }
        if(b == 0) {
            throw new IllegalArgumentException("b can'n be zero!");
        }
        if(a == Integer.MIN_VALUE && b == -1) {
            return Integer.MAX_VALUE;
        }
        int res = 0;
        int diff = Math.abs(a) - Math.abs(b);
        if(b == 1) {
            return a;
        }
        if(diff == 0) {
            if(a != b) {
                return -1;
            } else {
                return 1;
            }
        }
        while(diff >= 0) {
            diff -= Math.abs(b);
            res++;
        }
        if((a > 0 && b > 0) || (a < 0 && b < 0)) {
            return res;
        } else {
            return -res;
        }
    }
}
```
位运算的做法我自己确实是想不太出来了😂.
```java
class Solution {
    public int divide(int a, int b) {
        if(a==Integer.MIN_VALUE&&b==-1){
            return Integer.MAX_VALUE;
        }
        // 判断是否同号
        boolean flag = a>0&&b>0||a<0&&b<0;
        // 转long防止后面相加溢出
        long d = a;
        long v = b;
        // 都变为正数
        d = d>0?d:-d;
        v = v>0?v:-v;
        int res = 0;
        while(true){
            if(d<v){
                break;
            }
            int cur = 1;
            long tmp = v;
            while(tmp+tmp<=d){
                tmp+=tmp;
                cur+=cur;
            }
            res += cur;
            d-=tmp;
        }
        return flag?res:-res;
    }
}
```