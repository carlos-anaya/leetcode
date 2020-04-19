# 695. Max Area of Island

Given a non-empty 2D array grid of `0's` and `1's`, an **island** is a group of 1's (representing
land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid
are surrounded by water.

Find the max area of an island in the given 2D array. (If there is no island, the maximum area is 0)

- Example
> **Input**:<br>
> 10011 <br>
> 10000 <br>
> 10110 <br>
> 00110 <br>
> **Output**: 4

## Optimal

This is similar to [200. Number of Islands](200-Number%20of%20Islands.md), but instead of just 
finding the number of islands, we count the number of `1's` (size of the island), keeping track of
the **max area** so far. The `exploreIsland` function now returns the size of the island (and marks
the island as visited).

**Time: O(n\*m)** (nested loop, and the DFS could potentially visit all the cells) <br> 
**Space: O(n\*m)** (in it's wost case -grid filled with 1's- the call stack will be n\*m)

**Note**: we can add a *short-circuit* condition, checking if the *remaining area* is smaller than
the current *max area*.

### Go solution
```go
func maxAreaOfIsland(grid [][]int) int {
    ans := 0
    for i, row := range grid {
        for j, val := range row {
            if val == 1 {
                if area := exploreIsland(i, j, &grid); area > ans {
                    ans = area
                }
            }
        }
    }
    return ans
}

func exploreIsland(i, j int, grid *[][]int) int {
    if !isValid(i, j, grid) {
        return 0
    }
    
    (*grid)[i][j] = 0
    
    return  exploreIsland(i - 1, j, grid) +
            exploreIsland(i + 1, j, grid) +
            exploreIsland(i, j - 1, grid) +
            exploreIsland(i, j + 1, grid) + 1
}

func isValid(i, j int, grid *[][]int) bool {
    if  i < 0 || i >= len(*grid) || 
        j < 0 || j >= len((*grid)[0]) ||
        (*grid)[i][j] == 0 {
        return false
    }
    return true
}
```
### Java solution
```java
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        int maxArea = 0;
        int n = grid.length, m = grid[0].length;
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < m; j++) {
                // Short-circuit when remaining area is smaller than max area
                if (n * m - (i + 1)*(j + 1) < maxArea) {
                    return maxArea;
                }
                if(grid[i][j] == 1) {
                    maxArea = Math.max(maxArea, exploreIsland(i, j, grid, n, m));
                }
            }
        }
        
        return maxArea;
    }
    
    private int exploreIsland(int i, int j, int[][] grid, int n, int m) {
        // calculating  n and m increments runtime, so better to pass as args;
        if(i < 0 || i >= n || j < 0 || j >= m || grid[i][j] == 0) {
            return 0;
        }
        
        grid[i][j] = 0;
        return exploreIsland(i - 1, j, grid, n, m) +
                exploreIsland(i + 1, j, grid, n, m) +
                exploreIsland(i, j - 1, grid, n, m) +
                exploreIsland(i, j + 1, grid, n, m) + 1;
    }
}
```