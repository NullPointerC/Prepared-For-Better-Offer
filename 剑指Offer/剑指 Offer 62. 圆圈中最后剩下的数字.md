[剑指 Offer 62. 圆圈中最后剩下的数字](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/ "剑指 Offer 62. 圆圈中最后剩下的数字")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220208001304728-1759962393.png)
这里没有想到什么更好的办法，只好模拟了，每一次要删除的位置idx可以从上一次删除的位置idx模拟得到。
若上一次要删除的位置为$idx$，那么再下一次的删除位置就需要加m，由于这里说的是第$m$个，并且对于后面的数字来说，就相当于往前移动了1为，所以换成索引就需要-1。并且与$n$取模。
```java
class Solution {
    public int lastRemaining(int n, int m) {
        List<Integer> list = new ArrayList<>();
        for(int i = 0; i < n; i++) {
            list.add(i);
        }
        int idx = 0;
        while(n > 1) {
            idx = (idx + m - 1) % n;
            list.remove(idx);
            n--;
        }
        return list.get(0);
    }
}
```
数学方法属实是没有看太懂，据说是约瑟夫环问题😂。
```java
class Solution {
    public int lastRemaining(int n, int m) {
        int x = 0;
        for (int i = 2; i <= n; i++) {
            x = (x + m) % i;
        }
        return x;
    }
}
```
