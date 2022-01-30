[剑指 Offer 45. 把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220130222821518-1931676051.png)

这里比较自然和联想到，我们选取字典序较小的在前面，而字典序较大的放在后面。但是这里也存在比如3和30这样的数如何排序的问题。
我们假设这样的两个字符串分别是x和y，组合在一起就是xy或yx，我们需要将较小的放在前面，所以只需要比较xy和yx的大小即可，若xy比yx小，则我们把x放在y的前面，反之亦然。
这里的难点在于如何证明这个比较规则是正确的：
我们定义A,B,C为三个元素，a,b,c为各自的位数。
如果A<B，则AB<BA，故而A * 10 ^ b + B < B * 10 ^ a + A
故A * (10 ^ b - 1) < B * (10 ^ a - 1)
故A / (10 ^ a - 1) < B / (10 ^ b - 1)
同理，若B<C,则BC<CB，得B / (10 ^ b - 1) < C / (10 ^ c - 1)
得A < C，所以传递性得证。易证自反性和对称性，故而原规则有效。
所以可以写出如下代码：
```java
class Solution {
    public String minNumber(int[] nums) {
        int n = nums.length;
        String[] strs = new String[n];
        for(int i = 0; i < n; i++) strs[i] = String.valueOf(nums[i]);
        Arrays.sort(strs, (a, b) -> {return (a.concat(b)).compareTo(b.concat(a));});
        StringBuilder sb = new StringBuilder();
        for(var str : strs) sb.append(str);
        return sb.toString();
    }
}
```