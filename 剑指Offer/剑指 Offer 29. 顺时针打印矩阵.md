[剑指 Offer 29. 顺时针打印矩阵](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/ "剑指 Offer 29. 顺时针打印矩阵")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220208003510142-203033477.png)

老面孔了，只要画图注意边界即可。
```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        int u = 0, d = matrix.length - 1;
        // 排除非矩阵的情况
        if(d < 0) {
            return new int[]{};
        }
        int l = 0, r = matrix[0].length - 1;
        int[] res = new int[matrix.length * matrix[0].length];
        int idx = 0;
        while(u <= d && l <= r) {
            // 上方的
            if(u <= d && l <= r) {
                for(int i = l; i <= r; i++) {
                    res[idx] = matrix[u][i];
                    idx++;
                }
            }
            u++;
            // 右侧的
            if(u <= d && l <= r) {
                for(int i = u; i <= d; i++) {
                    res[idx] = matrix[i][r];
                    idx++;
                }
            }
            r--;
            // 下方的
            if(u <= d && l <= r) {
                for(int i = r; i >= l; i--) {
                    res[idx] = matrix[d][i];
                    idx++;
                }
            }
            d--;
            // 左侧的
            if(u <= d && l <= r) {
                for(int i = d; i >= u; i--) {
                    res[idx] = matrix[i][l];
                    idx++;
                }
            }
            l++;
        }
        return res;
    }
}
```