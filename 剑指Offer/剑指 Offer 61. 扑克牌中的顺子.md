[剑指 Offer 61. 扑克牌中的顺子](https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220130232425454-1161151327.png)

这里我们可以先对数组排序，这样就更加方便地找递增位置。
如果i与i+1位置的元素相差恰好为1，那么说明此时恰为递增，故不需要用尝试用0来填充，如果否则记录下此时二者之间需要用多少张牌填充，即为$nums[i + 1] - nums[i] - 1$，最后返回0的个数是否比此数大即可。
```java
class Solution {
    public boolean isStraight(int[] nums) {
        Arrays.sort(nums);
        int zeros = 0, diff = 0;
        for(int i = 0; i < nums.length - 1; i++) {
            if(nums[i] == 0) {
                zeros++;
            } else {
                if(nums[i] == nums[i + 1]) return false;
                if(nums[i] + 1 != nums[i + 1]) {
                    diff += nums[i + 1] - nums[i] - 1;
                }
            }
        }
        return zeros >= diff;
    }
}
```