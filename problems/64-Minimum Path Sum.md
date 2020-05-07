# 64. Minimum Path Sum

Given a `m x n` grid filled with *non-negative numbers*, find a path from top left to bottom right
which minimizes the sum of all numbers along its path.

**Note**: You can only move either down or right at any point in time.

- Example 1:
> **Input**: <br>
> [[1,2,1,3] <br>
>  [2,4,2,1] <br>
>  [3,2,1,5] <br>
>  [1,2,1,3]] <br>
> **Output**: 11 <br>
> **Explanation**: path is 1 -> 2 -> 1 -> 2 -> 1 -> 1 -> 3

## Brute force
We can do a **DFS** recursive call, exploring the element right or down. The call stack will be of
size *N\*M*.

## DP - Destroying the input
If destroying the input is ok, we can update the matrix directly. Elements in the first row can be
calculated as **grid[0][j] = grid[0][j] + grid[0][j-1]**, for *j > 0*. Likewise, elements in the
first colum are **grid[i][0] = grid[i][0] + grid[i-1][0]** for *i > 0*.

Finally, for elements where *i > 0 && j > 0*, we have two choices, the path from above or the path
from the left, thus the relation is **grid[i][j] = min(grid[i-1][j], grid[i][j-1]) + grid[i][j]**.
The solution is in grid[n-1][m-1]. Considering the input from above, the grid becomes:
> [1 3 4 7] <br>
> [3 7 6 7] <br>
> [6 8 7 12] <br>
> [7 9 8 11]

**Time: O(M\*N) <br> Space: O(1)**

## DP - Preserving the input
If destroying the input is not allowed, instead of allocating a matrix of size *M\*N* to calculate
an answer similar as above, we see that we only care of the *previous row* and the previous value to
the left.

**Time: O(M\*N) <br> Space: O(M)**

### Go solution
```go
func minPathSum(grid [][]int) int {
    m := len(grid[0])
    prevRow := make([]int, m)
    prevRow[0] = grid[0][0]
    
    for j := 1; j < m; j++ {
        prevRow[j] = grid[0][j] + prevRow[j - 1]
    }
    for i := 1; i < len(grid); i++ {
        prevRow[0] += grid[i][0]
        for j := 1; j < m; j++ {
            prevRow[j] = min(prevRow[j - 1], prevRow[j]) + grid[i][j]
        }
    }
    
    return prevRow[m - 1]
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}
```
### Java solution - If destroying input is ok 
```java
class Solution {
    public int minPathSum(int[][] grid) {
        int n = grid.length, m = grid[0].length;
        for(int j = 1; j < m; j++){
            grid[0][j] += grid[0][j-1];
        }
        
        for(int i = 1; i < n; i++) {
            grid[i][0] += grid[i-1][0];
            for(int j = 1; j < m; j++) {
                grid[i][j] += Math.min(grid[i-1][j], grid[i][j - 1]);
            }
        }
        
        return grid[n-1][m-1];
    }
}
```