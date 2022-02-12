[剑指 Offer 48. 最长不含重复字符的子字符串](https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220125144207235-1682034437.png)
对于字符串区间类题目，一般可以考虑使用滑动窗口来求解问题。
在滑动窗口中一般设置l和r两个指针，l指针指向窗口的左边缘，r指针指向窗口的右边缘，整个窗口的大小为`r - l + 1`。
在本题中，再用一个map或者set来查看窗口是否有重复数字，这里我为了方便一些用的是长为128的数组，正好可以存储下所有字符类型。
遍历到r位置的字符时，将r位置字符的次数加上1，若其正好为1，则说明之前的窗口中没有出现过r位置的字符，窗口扩大，r指针前移;
若r位置的字符出现次数大于1，则说明之前的窗口中出现过这个字符，窗口需要从左边缘开始收缩窗口，收缩时，需要满足`l <= r && map[chars[r]] > 1`，也就是确保窗口中不会有重复字符且不让窗口的左边缘超过右边缘。收缩的过程为:l指针前移，窗口中l指针位置指向的字符次数相应减少。此时窗口中没有重复字符了，继续扩大窗口r++。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        if(n == 0) return 0;
        if(n == 1) return 1;
        char[] chars = s.toCharArray();
        int l = 0, r = 0;
        int[] map = new int[128];
        while(r < n) {
            map[chars[r]]++;
            if(map[chars[r]] == 1) {
                // 窗口前移
                ans = Math.max(r - l + 1, ans);
                r++;
            } else if (map[chars[r]] > 1){
                // 窗口缩小
                while(l <= r && map[chars[r]] > 1) {
                    map[chars[l]]--;
                    l++;
                }
                // 无重复字符了,进一步扩大窗口
                r++;
           }
        }
        return ans;
    }
}
```
