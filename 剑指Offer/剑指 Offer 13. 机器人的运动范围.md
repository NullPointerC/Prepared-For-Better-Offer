[剑指 Offer 13. 机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220129234835083-971185211.png)
这题需要注意它和前一题的不同之处。前一题是要求搜索问题，已经搜索到的单词的字母，如'A'，在这一轮的搜索中没有被用上，但是可能从'A'的上一步的其他方向可能又走到'A'，所以要恢复现场。
但是这里要统计的不是是否可以达，而是要统计可达的格子数，格子如果已经走了这里，就不能在尝试从其他方向走过来了，所以不需要恢复现场。
```java
class Solution {
    private static final int[] dx = new int[]{-1, 0, 1, 0};
    private static final int[] dy = new int[]{0, -1, 0, 1};
    int ans = 0;
    public int movingCount(int m, int n, int k) {
        boolean[][] visted = new boolean[m][n];
        backtrack(m, n, k, 0, 0, visted);
        return ans;
    }


    private void backtrack(int m, int n, int k, int x, int y, boolean[][] visted) {
        if(getDigitSum(x, y) <= k) {
            ans++;
        } else {
            return ;
        }
        visted[x][y] = true;
        for(int i = 0; i < 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            if(nx < 0 || nx >= m || ny < 0 || ny >= n || visted[nx][ny]) {
                continue;
            } else {
                backtrack(m, n, k, nx, ny, visted);
            }
        }
        // visted[x][y] = false;
    }

    private int getDigitSum(int x, int y) {
        return x / 10 + x % 10 + y / 10 + y % 10;
    }
}
```