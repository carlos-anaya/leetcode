# 463. Island Perimeter (Easy)

You are given a map in form of a two-dimensional integer grid where 1 represents land and 0
represents water.

Grid cells are connected horizontally/vertically (not diagonally). The grid is completely surrounded
by water, and there is exactly one island (i.e., one or more connected land cells).

The island doesn't have "lakes" (water inside that isn't connected to the water around the island).
One cell is a square with side length 1. The grid is rectangular, width and height don't exceed 100.
Determine the perimeter of the island.

- Example 1:
> **Input**: [ <br>
> [0,1,0,0], <br>
> [1,1,1,0], <br>
> [0,1,0,0], <br>
> [1,1,0,0]]
> **Output**: 16 <br>
> **Explanation**: The perimeter is marked with yellow lines in the following image:

![alt island](https://assets.leetcode.com/uploads/2018/10/12/island.png)

## Optimal
Loop over each cell and if the cell is part of the island (*grid[i][j]==1*) calculate get it's
perimeter and add it to the whole perimeter. To get the perimeter of a cell we check that their
neighbors are 0 or are off limit.

**Time: O(n\*m) <br> Space: O(1)**

### Go solution
```go
func islandPerimeter(grid [][]int) int {
    n, m, p := len(grid), len(grid[0]), 0
    for i := 0; i < n; i++ {
        for j := 0; j < m; j++ {
            if grid[i][j] == 1 {
                p += getPerimeter(grid, i, j, n, m)
            }
        }
    }
    return p
}

func getPerimeter(grid [][]int, i, j, n, m int) int {
    p := 0
    if i - 1 < 0 || grid[i-1][j] == 0 { p++ }
    if i + 1 >= n || grid[i+1][j] == 0 { p++ }
    if j - 1 < 0 || grid[i][j-1] == 0 { p++ }
    if j + 1 >= m || grid[i][j+1] == 0 { p++ }
    return p
}
```
### Java solution
```java
class Solution {
    public int islandPerimeter(int[][] grid) {
        int n = grid.length, m = grid[0].length;
        int perimeter = 0;
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < m; j++) {
                if(grid[i][j] == 1)
                    perimeter += getPerimeter(grid, i, j, m, n);
            }
        }
        return perimeter;
    }
    
    private int getPerimeter(int[][] grid, int i, int j, int m, int n) {
        int perimeter = 0;
        if(i <= 0 || grid[i-1][j] == 0)
            perimeter++;
        if(i >= n-1 || grid[i+1][j] == 0)
            perimeter++;
        if(j <= 0 || grid[i][j-1] == 0)
            perimeter++;
        if(j >= m-1 || grid[i][j+1] == 0)
            perimeter++;
        
        return perimeter;
    }
}
```