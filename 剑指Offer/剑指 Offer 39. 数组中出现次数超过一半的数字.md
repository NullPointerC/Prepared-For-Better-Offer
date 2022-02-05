[剑指 Offer 39. 数组中出现次数超过一半的数字](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/ "剑指 Offer 39. 数组中出现次数超过一半的数字")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220206003127556-1842781389.png)
一个比较简单的办法就是哈希计数，统计次数大于$\frac{n}{2}$的数字。
```java
class Solution {
    public int majorityElement(int[] nums) {
        int res = -1, n = nums.length;
        Map<Integer, Integer> map = new HashMap<>();
        for(var num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        int mid = n >> 1;
        for(var entry : map.entrySet()) {
            if(map.get(entry.getKey()) > mid) {
                res = entry.getKey();
                break;
            }
        }
        return res;
    }
}
```
时间复杂度为$O(n)$，空间复杂度为$O(n)$。
另一个办法是注意到题目给定的条件中有这个数字出现的次数大于$\frac{n}{2}$，因此数字肯定不止出现在前半部分或后半部分，因此我们将数组进行排序，中间位置的就一定是要求的数字。
```java
class Solution {
    public int majorityElement(int[] nums) {
        int res = -1, n = nums.length;
        Arrays.sort(nums);
        res = nums[n >> 1];
        return res;
    }
}
```
时间复杂度为$O(nlogn)$，空间复杂度为$O(1)$，这个方法的效率比哈希表会更好是因为哈希表需要不断扩容。
还有一种办法就是摩尔投票法，也是求多数元素的一种比较简单实用的办法。
就是我们选定一个候选人，如果遍历时发现是候选人，那么计数加一，否则减一，当计数为0时，更换候选人，这样总能选出多数元素。
```java
class Solution {
    public int majorityElement(int[] nums) {
        // 初始时要将计数置为空
        int res = -1, cnt = 0;
        for(var num : nums) {
            // 更换候选人
            if(cnt == 0) {
                res = num;
                cnt++;
            } else {
                // 判断是否和候选人一样
                if(res == num) {
                    cnt++;
                } else {
                    cnt--;
                }
            }
        }
        return res;
    }
}
```
