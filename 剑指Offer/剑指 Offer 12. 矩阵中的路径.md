[剑指 Offer 12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220129224702034-1303747605.png)
dfs+剪枝问题。
这里由于是需要对所有的相邻节点尝试并且如果行不通需要重试，所以还需要回溯，回溯的过程中也有需要剪枝的地方，如走过的地方就不能再走，并且不能走出图外去。
这里我们用`isContains`表示这一轮的搜索是否搜到了要搜的字母，如果搜索到了，就继续往相邻的位置继续搜，如果没有搜到，要记得清除搜索痕迹并且重试。
```java
class Solution {
    private static final int[] dx = new int[]{-1, 0, 1, 0};
    private static final int[] dy = new int[]{0, -1, 0, 1};
    public boolean exist(char[][] board, String word) {
        if(null == word || word.equals("")) {
            return true;
        }
        int m = board.length;
        int n = board[0].length;
        boolean[][] visted = new boolean[m][n];
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(board[i][j] == word.charAt(0)) {
                    boolean isContains = backtrack(board, word, visted, 0, i, j);
                    if(isContains) return true;
                }
            }
        }
        return false;
    }

    private boolean backtrack(char[][] board, String word, boolean[][] visted, int position, int x, int y) {
        visted[x][y] = true;
        if(position == word.length() - 1) {
            return true;
        }
        boolean isContains = false;
        for(int i = 0; i < 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            int m = board.length;
            int n = board[0].length;
            if(nx < 0 || nx >= m || ny < 0 || ny >= n || visted[nx][ny] || board[nx][ny] != word.charAt(position + 1)) {
                continue;
            } else {
                visted[nx][ny] = true;
                isContains = backtrack(board, word, visted, position + 1, nx, ny);
                if(isContains) return true;
                visted[nx][ny] = false;
            }
        }
        if(!isContains) visted[x][y] = false;
        return isContains;
    }
}
```