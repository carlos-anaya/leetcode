# 200. Number of Islands

Given a 2d grid map of `'1's` (land) and `'0's` (water), count the number of `islands`. An *island*
is surrounded by water and is formed by connecting adjacent lands *horizontally* or *vertically*. 
You may assume all four edges of the grid are all surrounded by water.

- Example 1:
> **Input**:<br>
> 11110 <br>
> 11010 <br>
> 11000 <br>
> 00000 <br>
> **Output**: 1
- Example 2:
> **Input**:<br>
> 11000 <br>
> 11010 <br>
> 00100 <br>
> 00100 <br>
> **Output**: 3

## Optimal
Use *nested loops* to check char by char. When we find a `1` then we *"explore"* the island. Use a
`DFS` approach to *explore* the island, marking visited `1` and visiting the neighbor cells.
- If destroying the input is ok, then we can set the `1's` to `0's`, otherwise, use another char 
  and at the end, revert to ones.
- Validate that the indexes **i, j** are within the range.

**Time: O(n\*m)** (nested loop, and the DFS could potentially visit all the cells) <br> 
**Space: O(n\*m)** (in it's wost case -grid filled with 1's- the call stack will be n\*m)

### Go solution
```go
func numIslands(grid [][]byte) int {
    count := 0
    
    for i, row := range grid {
        for j, val := range row {
            if val == '1' {
                visitIsland(i, j, &grid)
                count++
            }
        }
    }
    
    return count
}

func visitIsland(i, j int, grid *[][]byte) {
    if !isValid(i, j, grid) {
        return
    }
    
    (*grid)[i][j] = '0'
    visitIsland(i - 1, j, grid)
    visitIsland(i + 1, j, grid)
    visitIsland(i, j - 1, grid)
    visitIsland(i, j + 1, grid)
}

func isValid(i, j int, grid *[][]byte) bool {
    m, n := len(*grid), len((*grid)[0])
    if i < 0 || i >= m || j < 0 || j >= n || (*grid)[i][j] == '0' {
        return false
    }
    
    return  true
}
```
### Java solution
```java
class Solution {
    public int numIslands(char[][] grid) {
        int count = 0;
        for(int i = 0; i < grid.length; i++) {
            for(int j = 0; j < grid[i].length; j++) {
                if(grid[i][j] == '1') {
                    exploreIsland(i, j, grid);
                    count++;
                }
            }
        }
        return count;
    }
    
    private void exploreIsland(int i, int j, char[][] grid) {
        if(!isValid(i, j, grid))
            return;
            
        grid[i][j] = '0';
        exploreIsland(i - 1, j, grid);
        exploreIsland(i + 1, j, grid);
        exploreIsland(i, j - 1, grid);
        exploreIsland(i, j + 1, grid);
    }
    
    private boolean isValid(int i, int j, char[][] grid) {
        if(i < 0 || i >= grid.length || j < 0 || j >= grid[0].length ||
          grid[i][j] == '0') {
            return false;
        } 
        return true;
    }
}
```