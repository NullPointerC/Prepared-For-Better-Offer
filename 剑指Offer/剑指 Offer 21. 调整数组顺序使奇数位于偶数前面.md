[剑指 Offer 21. 调整数组顺序使奇数位于偶数前面](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220129211322122-396712785.png)
这里可以考虑比较暴力的方式,先遍历一趟原数组$nums$,将奇偶数分开存储,再先遍历奇数列表,再遍历偶数列表,就可以保证奇偶数都按照顺序存储了。
```java
class Solution {
    public int[] exchange(int[] nums) {
        int[] ans = new int[nums.length];
        List<Integer> oddList = new ArrayList<>();
        List<Integer> evenList = new ArrayList<>();
        for(var num : nums) {
            if(num % 2 == 1) {
                oddList.add(num);
            } else {
                evenList.add(num);
            }
        }
        int idx = 0;
        for(var num : oddList) {
            ans[idx++] = num;
        }
        for(var num : evenList) {
            ans[idx++] = num;
        }
        return ans;
    }
}
​```java
```
缺点是需要遍历多次，代码上并不是很简洁，并且使用了新空间，这里可以考虑使用双指针优化至O(n)的空间。

双指针方式就需要设置两个不同的指针分别去找到奇数和偶数，并且将其交换。

l指针设置在数组头部开始遍历,遇到奇数遍使左指针继续右移,遇到偶数停止,j指针设置在数组尾部开始遍历,遇到偶数使右指针继续左移,直到遇到奇数停止,此时，交换两个指针所指向的数值。

```java
class Solution {
    public int[] exchange(int[] nums) {
        int n = nums.length, l = 0, r = n - 1;
        while(l < r) {
            while(l < r && nums[l] % 2 == 1) {
                l++;
            }
            while(l < r && nums[r] % 2 == 0) {
                r--;
            }
            int temp = nums[l];
            nums[l] = nums[r];
            nums[r] = temp;
        }
        return nums;
    }
}
```