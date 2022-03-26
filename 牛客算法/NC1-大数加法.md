[**NC1** **大数加法**](https://www.nowcoder.com/practice/11ae12e8c6fe48f883cad618c2e81475?tpId=196&tqId=37176&rp=1&ru=/exam/oj&qru=/exam/oj&sourceUrl=%2Fexam%2Foj%3Ftab%3D%25E7%25AE%2597%25E6%25B3%2595%25E7%25AF%2587%26topicId%3D196%26page%3D1&difficulty=undefined&judgeStatus=undefined&tags=&title=)

![image-20220216000515283](http://static.codenote.xyz/img/20220216000515.png)

思路其实就是先把字符串反转过来，让低位在前，高位在后，因为加法是从地位开始模拟的，所以我们也需要从地位开始模拟，这里就可以先把字符串反转过来，再从低位开始累计，并且，由于10以内的加法可能产生进位，所以使用一个变量`carry`来保留进位，每一步的和也就是`s[i]+t[i]+carry`，之后把这个和的个位存进当前的结果中，继续保留进位，直到枚举完全`s`和`t`。

返回结果时，也需要注意，因为这里是从地位开始记录的，所以`res`也是低位在前，所以需要逆转过来，再返回结果。

```java
import java.util.*;


public class Solution {
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     * 计算两个数之和
     * @param s string字符串 表示第一个整数
     * @param t string字符串 表示第二个整数
     * @return string字符串
     */
    public String solve (String s, String t) {
        // write code here
        int l1 = s.length(), l2 = t.length();
        s = new StringBuilder(s).reverse().toString();
        t = new StringBuilder(t).reverse().toString();
        StringBuilder res = new StringBuilder();
        int carry = 0;
        for(int i = 0; i < l1 || i < l2; i++)
        {
            if(i < l1) carry += s.charAt(i) - '0';
            if(i < l2) carry += t.charAt(i) - '0';
            res.append(carry % 10);
            carry /= 10;
        }
        if(carry != 0) res.append(carry);
        return res.reverse().toString();
    }
}
```

