[剑指 Offer 56 - II. 数组中数字出现的次数 II](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220205013014604-1025981519.png)
最容易想到的自然还是map计数
```java
class Solution {
    public int singleNumber(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        for(var num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        for(var num : map.keySet()) {
            if(map.get(num) == 1) {
                return num;
            }
        }
        return 0;
    }
}
```
时间复杂度为$O(n)$，空间复杂度为$O(n)$，其中时间复杂度可能要超过$O(n)$，其中包括哈希表的扩容操作。
这里的运用位运算的方法我属实是想不太来，这里用了一个32位的数组来计算每一个二进制位出现的次数，如果一个数字出现了3次，那么它的每一位肯定也是出现了3次的，所以我们枚举每一个数字，将每个数字的二进制位出现的次数更新，再遍历$map$数组，如果$map[i]$出现的次数不是3的倍数，那么这一位肯定就是单独的那个数字中的，我们将每一位的位权相加即可。
```java
class Solution {
    public int singleNumber(int[] nums) {
        int[] map = new int[32];
        for(var num : nums) {
            for(int i = 0; i < 32; i++) {
                map[i] += (num & 1);
                num >>= 1;
            }
        }
        int res = 0;
        for(int i = 31; i >= 0; i--) {
            res = res << 1;
            if(map[i] % 3 == 1) {
                res |= 1;
            }
        }
        return res;
    }
}
```