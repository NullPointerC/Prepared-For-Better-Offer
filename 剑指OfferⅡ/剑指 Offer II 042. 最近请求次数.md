[剑指 Offer II 042. 最近请求次数](https://leetcode-cn.com/problems/H8086Q/)

![image-20220318232853504](http://static.codenote.xyz/img/20220318232853.png)

由于t是单调递增的，所以我们可以依次将t存入数组中，然后二分地去找到t-3000的左边界，找到了根据目前的size相减即可。

```java
class RecentCounter {
    private static final int N = 10010;
    private int[] q;
    int size;
    public RecentCounter() {
        q = new int[N];
        size = 0;
    }
    
    public int ping(int t) {
        q[size++] = t;
        int i = 0, j = size - 1, mid = 0, target = t - 3000;
        while(i < j) {
            mid = (i + j) / 2;
            if(q[mid] >= target) {
                j = mid;
            } else {
                i = mid + 1;
            }
        }
        return size - i;
    }
}

/**
 * Your RecentCounter object will be instantiated and called as such:
 * RecentCounter obj = new RecentCounter();
 * int param_1 = obj.ping(t);
 */
```

