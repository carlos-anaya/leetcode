# 36. Valid Sudoku (Medium)

Determine if a 9x9 `Sudoku` board is valid. Only the filled cells need to be validated according to 
the following **rules**:
- Each row must contain the digits `1-9` without repetition.
- Each column must contain the digits `1-9` without repetition.
- Each of the 9 `3x3` *sub-boxes* of the grid must contain the digits `1-9` without repetition.

![sudoku board](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)

**Note**:

- A Sudoku board (partially filled) could be valid but is not necessarily solvable.
- Only the filled cells need to be validated according to the mentioned rules.
- The given board contain only digits 1-9 and the character `'.'`.
- The given board size is always `9x9`.

## Optimal
We just need to validate that the numbers in each *row, column and block* don't repeat.
We have two loops from *0 to 9*, the outer loop is used to move on each *row, column and block* and
the second one loops over the numbers. We keep track of the *seen* numbers and in case we find it
again we return false. At the end we return true.

**Time: O(1) <br> Space: O(1)**

### Go solution
```go
func isValidSudoku(board [][]byte) bool {
    for i := 0; i < 9; i++ {
        // 3-dimensional to keep track of nums by row, col and block
        var seen [9][3]bool
        for j := 0; j < 9; j++ {
            // num1 moves through the row
            num1 := board[i][j]
            // num2 moves through the column
            num2 := board[j][i]
            // num3 moves through the block
            // Use '/' for vertical traversal. Because '/' increments by 1 after every 3 i
            blockRow := i/3*3
            // Use '%' for horizontal traversal. Because '%' increments by 1 for each i and resets
            blockCol := i%3*3
            num3 := board[blockRow + j/3][blockCol + j%3]
            
            if num1 != '.' {
                if seen[num1-'1'][0] { 
                    return false
                }
                seen[num1-'1'][0] = true
            }
            if num2 != '.' {
                if seen[num2-'0'][1] { 
                    return false
                }
                seen[num2-'0'][1] = true
            }
            if num3 != '.' {
                if seen[num3-'0'][2] { 
                    return false
                }
                seen[num3-'0'][2] = true
            }
        }
    }
    
    return true;
}
```
### Java solution
```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        for(int i = 0; i < 9; i++) {
            boolean[][] seen = new boolean[9][3];
            for(int j = 0; j < 9; j++) {
                char num1 = board[i][j];
                char num2 = board[j][i];
                char num3 = board[i/3*3 + j/3][i%3*3 + j%3];
                
                if(num1 != '.'){
                    if(seen[num1-'1'][0])
                        return false;
                    seen[num1-'1'][0] = true;
                }
                if(num2 != '.'){
                    if(seen[num2-'1'][1])
                        return false;
                    seen[num2-'1'][1] = true;
                }
                if(num3 != '.'){
                    if(seen[num3-'1'][2])
                        return false;
                    seen[num3-'1'][2] = true;
                }
            }
        }
        return true;
    }
}
```