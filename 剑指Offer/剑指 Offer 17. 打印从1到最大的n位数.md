[剑指 Offer 17. 打印从1到最大的n位数](https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/ "剑指 Offer 17. 打印从1到最大的n位数")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220213144137204-1299434321.png)
注意这里的n是表示位数,所以最大的数也就是枚举每一位都是9的情况。
最后从1枚举到最后一位即可。
```java
class Solution {
    public int[] printNumbers(int n) {
        if(n <= 0) {
            return new int[0];
        }
        int total = 0;
        while(n-- != 0) {
            total = total * 10 + 9;
        }
        int[] res = new int[total];
        for(int i = 0;i < total; i++) {
            res[i] = i + 1;
        }
        return res;
    }
}
```