[剑指 Offer 65. 不用加减乘除做加法](https://leetcode-cn.com/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220205005900244-862634150.png)

计组没学好😂，属实是不太会做，因为加法有个定理
$sum = a + b = increase + keep$，其中increase表示带有进位的结果，keep代表没有进位的结果，故而我们可以发现，$a + b$就等于$a + b$中不带进位的那一位$keep$左移一位加上进位
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220205010257909-1521285398.png)

```java
class Solution {
    public int add(int a, int b) {
        while(b != 0) {
            int carry = a & b;
            a = a ^ b;
            b = carry << 1;
        }
        return a;
    }
}
```