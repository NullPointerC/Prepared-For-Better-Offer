[剑指 Offer 56 - I. 数组中数字出现的次数](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220205010739771-1245184609.png)
最容易想到的办法自然是哈希计数，但是我们发现题目的范围给到了$1e5$，不断的给哈希表扩容比较花时间，也需要$O(n)$的遍历时间，还需要开哈希表的$O(n)$空间。
```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int[] res = new int[2];
        Map<Integer, Integer> map = new HashMap<>();
        for(var num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        int idx = 0;
        for(var key : map.keySet()) {
            if(map.get(key) == 1) {
                res[idx++] = key;
            }
        }
        return res;
    }
}
```
再接着考虑原地空间的方式，这时就需要注意到其他数字都出现了2次，由异或的知识可以知道$n \text{^} n = 0$且$0 \text{^} n = n$。
因此我们先枚举一趟$nums$，从$0$开始异或每一个$num$，如果出现过两次的就会相互消去，只出现过1次的那两个数字a,b得到了它们异或的结果$n = a \text{^} b$。
此时我们还是无法确定$a$和$b$的，需要再想到我们求得$n$的最右边那位1设这位索引位置为$i$，得到此时的值$n = n \text{&} (-n)$。
可以比较容易的得到$a_2$的第$i$位和$b_2$的第$i$位显然一个为1，一个为0，因此总能得到a和b。

```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int n = 0;
        for(var num : nums) {
            n ^= num;
        }
        n &= -n;
        int[] res = new int[2];
        for(var num : nums) {
            if((num & n) != 0) {
                res[0] ^= num;
            } else {
                res[1] ^= num;
            }
        }
        return res;
    }
}
```