# 130. Surrounded Regions (Medium)

Given a 2D board containing `X` and `O` (the letter O), capture all regions surrounded by `X`. A
region is captured by flipping all `O`'s into `X`'s in that surrounded region.

**Note**: Surrounded regions shouldn’t be on the border, which means that any `O` on the border of
the board are not flipped to `X`. Any `O` that is not on the border and it is not connected to an
`O` on the border will be flipped to `X`. Two cells are connected if they are adjacent cells 
connected horizontally or vertically.

- Example 1:
> **Input**: <br>
> X X X O <br>
> X O O X <br>
> X X O X <br>
> O O X X<br>
> **Output**: <br>
> X X X O <br>
> X X X X <br>
> X X X X <br>
> O O X X

## Optimal
1. Mark regions that won't be captured, i.e. do a `DFS` or `BFS` traversal from the border cells
  that are `O`. Use another char for that, e.g. `U`.
2. Capture the inner `O` regions, i.e. flip them to `X`.
3. Return the *un-captured* regions back to `O`.

**Time: O(n) <br> Space: O(n)**

### Java solution
```java
class Solution {
    private static final char X = 'X', O = 'O', U = 'U';
    private static final int[][] dirs = new int[][]{{-1,0},{1,0},{0,-1},{0,1}};
    int n, m;
    
    public void solve(char[][] board) {
        if(board.length ==0 || board[0].length == 0)
            return;
        
        n = board.length;
        m = board[0].length;
        // mark uncaptured cells in border
        for(int i = 0; i < n; i++) {
            dfs(board, i, 0);
            dfs(board, i, m-1);
        }
        for(int j = 0; j < m; j++) {
            dfs(board, 0, j);
            dfs(board, n-1, j);
        }
        // capture inner regions
        for(int i = 1; i < n; i++) {
            for(int j = 1; j < m; j++) {
                if(board[i][j] == O)
                    board[i][j] = X;
            }
        }
        // flip back non-surronded regions
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < m; j++) {
                if(board[i][j] == U)
                    board[i][j] = O;
            }
        }
    }
    
    private void dfs(char[][] board, int i, int j) {
        if(i < 0 || i >= n || j < 0 || j >= m || board[i][j] != O)
            return;
        board[i][j] = U;
        
        for(int[] dir : dirs)
            dfs(board, i + dir[0], j + dir[1]);
    }
}
```