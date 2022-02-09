[剑指 Offer 59 - I. 滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/ "剑指 Offer 59 - I. 滑动窗口的最大值")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220210003615110-568355953.png)
理解起来不难，可以一直维护最大值及其下标即可，如果窗口内有比当前最大值更大的，则更新最大值即可，如果当前最大值已经不在窗口内了，需要在窗口中重新找过一个最大值。
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        if(n == 0) {
            return new int[0];
        }
        int len = n - k + 1;
        if (len <= 0) {
            return new int[0];
        }
        int[] res = new int[len];
        int l = 0, r = k - 1, idx = 0;
        int curMax = Integer.MIN_VALUE, maxIdx = -1;
        for (int i = 0; i <= r; i++) {
            if (nums[i] > curMax) {
                curMax = nums[i];
                maxIdx = i;
            }
        }
        while (r < n) {
            res[idx] = curMax;
            idx++;
            r++;
            l++;
            if (l > maxIdx && r < n) {
                curMax = Integer.MIN_VALUE;
                for (int i = l; i <= r; i++) {
                    if (nums[i] > curMax) {
                        curMax = nums[i];
                        maxIdx = i;
                    }
                }
            }
            if (r < n && nums[r] > curMax) {
                curMax = nums[r];
                maxIdx = r;
            }
        }
        return res;
    }
}
```
这里有一些比较坑的地方在于需要时刻确保窗口没有跑到外面去，要确保窗口右边缘$r$没有超过数组范围n。
测试用例也很坑，说好了保证k有效，
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220210003856012-417891241.png)
还来一个k=0的用例。