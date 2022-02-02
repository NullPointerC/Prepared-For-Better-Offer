[剑指 Offer 64. 求1+2+…+n](https://leetcode-cn.com/problems/qiu-12n-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220202223457230-1659287232.png)
这里之前做过类似的,所以直接一步到位了
```java
class Solution {
    public int sumNums(int n) {
        int ans = n;
        boolean flag = (n > 0) && (ans += sumNums(n - 1)) > 0;
        return ans;
    }
}
```
用条件来短路递归,使递归结束。
