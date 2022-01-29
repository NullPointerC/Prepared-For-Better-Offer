[剑指 Offer 57. 和为s的两个数字](https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220129215006676-1582666598.png)
首先就容易想到的就是暴力，但是我们一看数据范围，$10^5$。套$O(n^2)$一般来说一定会超时，经过实验也发现确实会超时。
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220129215201783-1943321182.png)

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] ans = new int[2];
        int n = nums.length;
        for(int i = 0; i < n; i++) {
            for(int j = i + 1; j < n; j++) {
                if(nums[i] + nums[j] == target) {
                    ans[0] = nums[i];
                    ans[1] = nums[j];
                    return ans;
                }
            }
        }
        return null;
    }
}
```

然后比较容易想得到的的办法就是leetcode第一题两数之和的hash法一个已出现的值，再看是否右另外一个。
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        // int[] ans = new int[2];
        int n = nums.length;
        Set<Integer> set = new HashSet<>();
        for(var num : nums) {
            if(set.contains(target - num)) {
                return new int[]{num, target - num};
            }
            set.add(num);
        }
        return null;
    }
}
```

但是这些都并不是最优解，因为都忽略了题目给出的升序数组的条件，因为我们可以使用双指针法来求解问题。
设置两个指针l和r,l指向开头，r指向结尾，若l与r之和大于所求target，说明需要使两数和小一些，r左移，若小于，需要使两数之和大一些，l右移。
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int n = nums.length, l = 0, r = n - 1;
        while(l < r) {
            int sum = nums[l] + nums[r];
            if(sum == target) {
                return new int[]{nums[l], nums[r]};
            } else if(sum > target) {
                r--;
            } else if(sum < target) {
                l++;
            }
        }
        return null;
    }
}
```