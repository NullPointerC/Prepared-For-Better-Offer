[剑指 Offer 67. 把字符串转换成整数](https://leetcode-cn.com/problems/ba-zi-fu-chuan-zhuan-huan-cheng-zheng-shu-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220209012459804-961734379.png)

这破题，麻烦得一匹的同时，还要面向测试用例编程，非常麻烦，如果用户真的输入这种，应该在前端就给return掉，给后端只会更加麻烦。😭
```java
class Solution {
    public int strToInt(String str) {
        char[] c = str.trim().toCharArray();
        if(c.length == 0) return 0;
        int res = 0, boundry = Integer.MAX_VALUE / 10;
        int start = 1, sign = 1;
        if(c[0] == '-') sign = -1;
        else if(c[0] != '+') start = 0;
        for(int i = start; i < c.length; i++) {
            if(c[i] < '0' || c[i] > '9') break;
            if(res > boundry || res == boundry && c[i] > '7') 
                  return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            res = res * 10 + (c[i] - '0');
        }
        return sign * res;
    }
}
```
没啥技巧，就是嗯模拟，同时每次WA完看自己漏掉了什么情况，补上去就是，这里是copy了一个大佬的代码，太简洁了😭。
