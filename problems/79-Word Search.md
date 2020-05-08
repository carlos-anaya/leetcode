# 79. Word Search (Medium)

Given a 2D board and a word, find if the word exists in the grid.

The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are
those horizontal or vertical neighbors. The same letter cell may not be used more than once.

- Example:
> **Input**: board: <br>
> ['A','B','C','E'] <br>
> ['S','F','C','S'] <br>
> ['A','D','E','E'] <br>
> Given word = *"ABCCED"*, return **true** <br>
> Given word = *"SEE"*, return **true** <br>
> Given word = *"ABCB"*, return **false**

## Optimal - Backtracking
We need to loop over the board and then do a DFS search trying to match characters by backtracking
paths already checked. Since we can't use a previously used char, we need to mark it and if it did
not work, then restore it. For every match we need to check the 4 neighbors.

**Time: O(M\*N\*4^L)** (L -> lenght of word; 4^L because we check 4 paths of at most L chars) <br>
**Space: O(L)**

### Go solution
```go
func exist(board [][]byte, word string) bool {
    for i := 0; i < len(board); i++ {
        for j := 0; j < len(board[0]); j++ {
            if(checkBoard(&board, &word, i, j, 0)) {
                return true
            }
        }
    }
    return false
}

func checkBoard(board *[][]byte, word *string, i, j, w int) bool {
    // already matched all the word chars
    if w >= len(*word) {
        return true
    }
    // check next char is within the board and it matches the curr word char
    if (i < 0 || i >= len(*board) || j < 0 || j >= len((*board)[0]) ||
        (*board)[i][j] != (*word)[w]) {
        return false
    }
    
    c := (*board)[i][j];
    (*board)[i][j] = '.' // same letter can only be used once, so we mark it
    
    ans := checkBoard(board, word, i-1, j, w+1) ||
            checkBoard(board, word, i+1, j, w+1) ||
            checkBoard(board, word, i, j-1, w+1) ||
            checkBoard(board, word, i, j+1, w+1)
    
    (*board)[i][j] = c // restore the board value
    return ans
}
```
### Java solution
```java
class Solution {
    public boolean exist(char[][] board, String word) {
        for(int i = 0; i < board.length; i++) {
            for(int j = 0; j < board[0].length; j++) {
                if(exist(board, word, i, j, 0))
                    return true;
            }
        }
        return false;
    }
    
    private boolean exist(char[][] board, String word, int i, int j, int w) {
        if(w >= word.length())
            return true;
        
        if(i < 0 || i >= board.length || j < 0 || j >= board[0].length ||
           board[i][j] != word.charAt(w)) {
            return false;
        }
        
        w++;
        char c = board[i][j];
        board[i][j] = '.';
        boolean ans = exist(board, word, i-1, j, w) ||
            exist(board, word, i+1, j, w) ||
            exist(board, word, i, j-1, w) ||
            exist(board, word, i, j+1, w);
        
        board[i][j] = c;
        return ans;
    }
}
```