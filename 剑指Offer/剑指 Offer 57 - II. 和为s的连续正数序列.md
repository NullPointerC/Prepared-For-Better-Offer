[剑指 Offer 57 - II. 和为s的连续正数序列](https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/ "剑指 Offer 57 - II. 和为s的连续正数序列")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220207234920071-2064595024.png)

想不到比较好的办法，只好暴力从每一个位置$i$往后枚举。
```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        List<List<Integer>> resList = new ArrayList<>();
        int start = 1, end = target;
        while(start < target) {
            int i = start;
            int total = 0;
            List<Integer> tmp = new ArrayList<>();
            // 枚举每一个start的位置
            for(int j = i; j < target; j++) {
                tmp.add(j);
                total += j;
                if(total < target) {
                    continue;
                } else if (total == target) {
                    resList.add(tmp);
                    break;
                } else {
                    break;
                }
            }
            start++;
        }
        int n = resList.size();
        int[][] res = new int[n][];
        for(int i = 0; i < n; i++) {
            int m = resList.get(i).size();
            res[i] = new int[m];
            for(int j = 0; j < m; j++) {
                res[i][j] = resList.get(i).get(j);
            }
        }
        return res;
    }
}
```
时间复杂度为$O(n^2)$，空间复杂度为$O(n)$
或者滑动窗口滑就完事了。
```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        List<int[]> resList = new ArrayList<>();
        for(int l = 1, r = 1, sum = 0; r < target; r++) {
            // 窗口右移
            sum += r;
            while(sum > target) {
                // 窗口左移
                sum -= l;
                l++;
            }
            if(sum == target) {
                int len = r - l + 1;
                int[] tmp = new int[len];
                for(int i = 0; i < len; i++) {
                    tmp[i] = l + i;
                }
                resList.add(tmp);
            }
        }
        int n = resList.size();
        int[][] res = new int[n][];
        for(int i = 0; i < n; i++) {
            res[i] = resList.get(i);
        }
        return res;
    }
}
```
时间复杂度为$O(n)$，空间复杂度为$O(n)$。
