[剑指 Offer 20. 表示数值的字符串](https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/ "剑指 Offer 20. 表示数值的字符串")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220209010138717-1776284980.png)

这种大模拟题真的就是磨练心态的，太麻烦了，wa得没脾气。
总结起来就是一定要有数字，小数点前必须有数字并且小数点前面不能有e，e前面必须有数字且前面不能有e,有e之后为了有指数，需要重置$numCnt$，符号必须出现在第一位或者在e后面。
```java
class Solution {
    public boolean isNumber(String s) {
        if(s == null || s.length() == 0) {
            return false;
        }
        s = s.trim();
        int eCnt = 0, numCnt = 0, dotCnt = 0, symbolCnt = 0;
        for(int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if(c >= '0' && c <= '9') {
                // 出现数字直接加
                numCnt++;
            } else if(c == '.') {
                // 出现小数点,需要之前没有出现过.和e
                if(dotCnt != 0 || eCnt != 0) {
                    return false;
                } else {
                    dotCnt++;
                }
            } else if(c == 'e' || c == 'E') {
                // 出现了e,需要之前出现了数字且没有出现过e
                if(numCnt == 0 || eCnt != 0) {
                    return false;
                } else {
                    eCnt++;
                    numCnt = 0;
                }
            } else if(c == '+' || c == '-') {
                // 符号要出现在第一位
                if(i != 0 && (s.charAt(i - 1) != 'e' && s.charAt(i - 1) != 'E')) {
                    return false;
                } else {
                    symbolCnt++;
                }
            } else {
                return false;
            }
        }
        return numCnt > 0;

    }
}
```