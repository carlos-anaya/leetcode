# 174. Dungeon Game

The demons had captured the princess (P) and imprisoned her in the bottom-right corner of a dungeon.
The dungeon consists of **M x N** rooms laid out in a 2D grid. Our valiant knight (K) was initially
positioned in the top-left room and must fight his way through the dungeon to rescue the princess.

The knight has an initial health point represented by a positive integer. If at any point his health
point drops to 0 or below, he dies immediately.

Some of the rooms are guarded by demons, so the knight loses health (negative integers) upon 
entering these rooms; other rooms are either empty (0's) or contain magic orbs that increase the
knight's health (positive integers).

In order to reach the princess as quickly as possible, the knight decides to move only rightward or
downward in each step.

**Write a function to determine the knight's minimum initial health so that he is able to rescue the
princess.**

**Notes**
1. The knight's health has no upper bound.
2. Any room can contain threats or power-ups, even the first room the knight enters and the 
   bottom-right room where the princess is imprisoned.

Example:

**Input**:

. | . | .
--- | --- | ---
-2 (K) | -3 | 3
-5 | -10 | 1
10 | 30 | -5 (P)


> **Output**: 7 <br>
> **Explanation**: the King moves R -> R -> D -> D. He takes a total damage of 6, so he needs at
> least 7 health points at start. Note that the King could move D -> D -> R -> R and arrive with
> 28 points, however he would have initally needed 8 points.

## DP - Destroying the input
The approach to solve this is somehow similar to the one in 
[64-Minimum Path Sum.md](64-Minimum%20Path%20Sum.md), but instead of finding the min path, we need
to find the path with the *minimum initial cost*, thus we start from *N,M* and move back to *0,0*.

- If in the *princess cell* (n, m) there's an elixir (positive number) the king needs 
  *just 1 health point* to arrive safely there. If there's a demon (negative number) the king needs
  that *number of points plus one* he can survive the demon's attack.
- In the cells *above* the princess cell, if there's a demon, the king needs those points plus the
  points he needed before. Otherwise, he needs the *points from before minus current cell points*.
  I.e. before reaching the cell below, there's an elixir that adds points, so he needs fewer points
  to survice in the cell below. (if the balance is positive, i.e. elixir 10 and he needed 5, then
  we can safely use just 1).
- The same logic applies to the cell's to the left of the princess cell. These ideas can be
  formalized as:
  - `dungeon[i][m] = min(dungeon[i+1][m] - dungeon[i][m], 1), for i < n`
  - `dungeon[n][j] = min(dungeon[n][j+1] - dungeon[n][j], 1), for j < m`
- Finally, for cells "in-between", we first need to check which of the previous cells to the right
  or bottom requires the fewer points, then we check again if there's a demon or an elixir in the
  current cell and update the cell accordingly. I.e.
  - `dungeon[i][j] = max(min(dungeon[i+1][j], dungeon[i][j+1]) - dungeon[i][j], 1)`

**Time: O(N\*M) <br> Space: O(1)**

## DP - Preserving the input
Same approach as above, but instead of destroying the input, we can allocate two arrays of size M,
and update accordingly since we're moving one level at a time.

**Time: O(N\*M) <br> Space: O(M)**

### Go solution - Destroying the input
```go
func calculateMinimumHP(dungeon [][]int) int {
    n, m := len(dungeon)-1, len(dungeon[0])-1
    
    if dungeon[n][m] < 0 { 
        // if last is negative, we need to arrive with abs(last) + 1 point
        dungeon[n][m] *= -1
        dungeon[n][m] += 1
    } else { 
        // if last is positive, we need to arrive with just 1 point 
        dungeon[n][m] = 1
    }
    
    for j := m - 1; j >= 0; j-- {
        // if new cell is higher/equal to prev, we need to arrive with just 1 point
        // else, we need the prev points minus the curr cell points
        dungeon[n][j] = max(dungeon[n][j+1] - dungeon[n][j], 1)
    }
    
    for i := n - 1; i >= 0; i-- {
        // if new cell is higher/equal to prev, we need to arrive with just 1 point
        // else, we need the prev points minus the curr cell points
        dungeon[i][m] = max(dungeon[i+1][m] - dungeon[i][m], 1)
        for j := m - 1; j >= 0; j-- {
            // in i, j we need to find the min of i+1,j and i,j+1
            min := min(dungeon[i+1][j], dungeon[i][j+1])
            // and then, to arrive to the current cell we need those min points
            // minus the current cell value. 
            dungeon[i][j] = max(min - dungeon[i][j], 1)
        }
    }
    
    return dungeon[0][0]
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}

func min(a, b int) int {
    if a > b {
        return b
    }
    return a
}
```
### Java solution - Without destroying the input
```java
class Solution {
    public int calculateMinimumHP(int[][] dungeon) {
        int n = dungeon.length - 1, m = dungeon[0].length - 1;
        int[] prevRow = new int[m + 1];
        prevRow[m] = 1;
        
        // if princess cell is negative, we need at least those points + 1
        if(dungeon[n][m] < 0) {
            prevRow[m] = dungeon[n][m] * -1 + 1;
        }
        
        // initialize prevRow with the last row of dungeon
        for(int j = m - 1; j >= 0; j--) {
            prevRow[j] = Math.max(prevRow[j+1] - dungeon[n][j], 1);
        }
        
        for(int i = n - 1; i >= 0; i--) {
            int[] currRow = new int[m + 1];
            currRow[m] = Math.max(prevRow[m] - dungeon[i][m], 1);
            for(int j = m - 1; j >= 0; j--) {
                int min = Math.min(currRow[j+1], prevRow[j]);
                currRow[j] = Math.max(min - dungeon[i][j], 1);
            }
            prevRow = currRow;
        }
        
        return prevRow[0];
    }
}
```