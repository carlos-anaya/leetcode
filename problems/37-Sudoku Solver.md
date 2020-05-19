# 37. Sudoku Solver (Hard)

Write a program to solve a `Sudoku` puzzle by filling the empty cells.

A sudoku solution must satisfy **all of the following rules**:

1. Each of the digits 1-9 must occur exactly once in each row.
2. Each of the digits 1-9 must occur exactly once in each column.
3. Each of the the digits 1-9 must occur exactly once in each of the 9 3x3 sub-boxes of the grid.

**Note**:

- The given board contain only digits 1-9 and the character '.'.
- You may assume that the given Sudoku puzzle will have a single unique solution.
- The given board size is always 9x9.
- Empty cells are indicated by the character '.'.

**Example**:

![initial board](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)

![solved board](https://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Sudoku-by-L2G-20050714_solution.svg/250px-Sudoku-by-L2G-20050714_solution.svg.png)

## Optimal - Backtracking
Loop over the whole board, when we find a `.` we loop from *1 to 9* for backtracking. We have to
validate each *candidate* for backtracking. The *candidate* validation checks if the number is valid
within the *row, column and block*.

### Go solution
```go
func solveSudoku(board [][]byte)  {
    solve(board)
}

func solve(board [][]byte) bool {
    for i := 0; i < 9; i++ {
        for j := 0; j < 9; j++ {
            // board already has a number
            if board[i][j] != '.' {
                continue
            }
            // the cell is empty, try all the numbers
            for c := byte('1'); c <= byte('9'); c++ {
                if isValidNumber(board, i, j, c) {
                    board[i][j] = c
                    if solve(board) {
                        return true
                    }
                    // candidate didn't work
                    board[i][j] = '.'
                }
            }
            // none of the candidates worked
            return false
        }
    }
    return true
}

// validate the candidate number is valid in this cell
func isValidNumber(board [][]byte, row, col int, num byte) bool {
    for i := 0; i < 9; i++ {
        // num1 moves through the row
        num1 := board[row][i]
        // num2 moves through the column
        num2 := board[i][col]
        // num3 moves through the block
        blockRow := (row/3)*3
        blockCol := (col/3)*3
        num3 := board[blockRow + i/3][blockCol + i%3]

        if num1 == num || num2 == num || num3 == num {
            return false
        }
    }
    return true;
}
```
### Java solution
```java
class Solution {
    public void solveSudoku(char[][] board) {
        solve(board, 0, 0);
    }
    
    // use row/col to keep track of the last filled cell
    private boolean solve(char[][] board, int row, int col) {
        for(int i = row; i < 9; i++) {
            for(int j = col; j < 9; j++) {
                // cell already has a number
                if(board[i][j] != '.')
                    continue;
                // explore all the numbers as candidates
                for(char c = '1'; c <= '9'; c++) {
                    // num is valid in that cell
                    if(isNumValid(board, i, j, c)) {
                        // backtracking
                        board[i][j] = c;
                        if(solve(board, i, j))
                            return true; // found a solution
                        board[i][j] = '.';
                    }
                }
                return false;
            }
            col = 0;
        }
        return true;
    }
    
    // check the number candidate is valid in row, column and block
    private boolean isNumValid(char[][] board, int row, int col, char c) {
        int rowIdx = row/3*3, colIdx = col/3*3;
        for(int i = 0; i < 9; i++) {
            if(board[row][i] == c || board[i][col] == c ||
              board[rowIdx + i/3][colIdx + i%3] == c) {
                return false;
            }
        }
        return true;
    }
}
```