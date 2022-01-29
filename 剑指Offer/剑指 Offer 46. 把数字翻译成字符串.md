[剑指 Offer 46. 把数字翻译成字符串](https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220125000204532-2004390677.png)
写动态规划一定一定要记得画图, 结合数组的规律的示例才能更好的理解一些。
本题要求的是我们对于一个数字，且对于数字中的每一数位，可以将其转为一个字母，要求我们可以获得的转换个数。
如果没有头绪，可以先分析一些比较简单示例，对于0-9之间的数字，只有一一对应的转换，自然返回1即可；
对于10-25之间的数字，既可以一一转换得到2个字母形成的字符串，也可以直接将整个数字转换得到一个字母(数字≥10且≤25)，故而有2种返回值；
再扩展至3位的数字，可以发现可以截取后2位得到一个新数字也可以直接一一对应地返回，也可以先截取前2位数字；所以得到如下的规律：
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220125000910972-2007976900.png)
转换为索引规律也就是i>2时，需要额外加上dp[i-2]，否则，因为对于i=1而言，dp[i-2]是dp[-1]也就是1；
```java
class Solution {
    public int translateNum(int num) {
        if(num <= 9) return 1;
        if(num <= 25) return 2;
        String str = String.valueOf(num);
        char[] chars = str.toCharArray();
        int n = chars.length;
        // 定义为chars[i]结尾的数字的翻译种类
        int[] dp = new int[n];
        Arrays.fill(dp, 1);
        for(int i = 1; i < n; i++) {
            dp[i] = dp[i - 1];
            int val = (chars[i - 1]  - '0') * 10 + (chars[i] - '0');
            if(i > 2 && val >= 10 && val <= 25) dp[i] += dp[i - 2];
            else if(val >= 10 && val <= 25) dp[i]++;
        }
        return dp[n - 1];
    }
}
```
