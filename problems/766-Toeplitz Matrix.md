# 766. Toeplitz Matrix (Easy)

A matrix is `Toeplitz` if **every diagonal** from top-left to bottom-right has the same element.

Now given an `M x N` matrix, return *true* if and only if the matrix is *Toeplitz*.

- Example 1:
> **Input**: matrix = [ <br>
>   [1,2,3,4], <br>
>   [5,1,2,3], <br>
>   [9,5,1,2]] <br>
> **Output**: True
- Example 2:
> **Input**: matrix = [ <br>
>   [1,2,3,4], <br>
>   [5,1,2,8], <br>
>   [9,2,1,2]] <br>
> **Output**: False

**Follow up**:

- **What if the matrix is stored on disk, and the memory is limited such that you can only load**
  **at most one row of the matrix into the memory at once?**
  - We use a *buffer* (can be a linked-list) to store the first row. When a new row comes we compare
    the *stream[j]* with *buffer[j-1]*. At the end we discard the *tail* of the buffer and add the
    *stream[0]* as *head*.
  - Similarly, we can *hash* the buffer from *0 to n-1*, then when the *stream* comes, we can hash
    *stream[1] ... stream[n]* and compare the hashes. 
- **What if the matrix is so large that you can only load a partial row into the memory at once?**
  - Split the matrix vertically, with an overlapping column in each sub-matrix, then repeat the
    previous process for each sub-matrix:

>    [1 2 3 4 5 6 7 8 9 0]     -->      [1 2 3 4]       +      [4 5 6 7]       +      [7 8 9 0] <br>
>    [0 1 2 3 4 5 6 7 8 9]     -->      [0 1 2 3]       +      [3 4 5 6]       +      [6 7 8 9] <br>
>    [1 0 1 2 3 4 5 6 7 8]     -->      [1 0 1 2]       +      [2 3 4 5]       +      [5 6 7 8] <br>
>    [2 1 0 1 2 3 4 5 6 7]     -->      [2 1 0 1]       +      [1 2 3 4]       +      [4 5 6 7] <br>
>    [3 2 1 0 1 2 3 4 5 6]     -->      [3 2 1 0]       +      [0 1 2 3]       +      [3 4 5 6]

## Approach I
Nested loop. First move from left to right, picking the *start number* and validate the numbers in
the **diagonal** match the *start number*. Then, repeat the nested loops but this time from top to
bottom.

**Time: O(n\*m) <br> Space: O(1)**

### Java solution
```java
class Solution {
    public boolean isToeplitzMatrix(int[][] matrix) {
        if(matrix.length < 2 || matrix[0].length < 2)
            return true;
        int rows = matrix.length;
        int cols = matrix[0].length;
        
        // validate by increasing cols (left to right)
        for(int i = 0; i < cols; i++) {
            int num = matrix[0][i];
            int x = i+1, y = 1;
            while(x < cols && y < rows) {
                if(matrix[y][x] != num)
                    return false;
                x++;
                y++;
            }
        }
        // validate by increasing rows (top to bottom)
        for(int i = 1; i < rows; i++) {
            int num = matrix[i][0];
            int x = 1, y = i + 1;
            while(x < cols && y < rows) {
                if(matrix[y][x] != num)
                    return false;
                x++;
                y++;
            }
        }
        return true;
    }
}
```

## Approach II
If we see carefully, the *num[0][0]* has to be the same as *num[1][1]* and *num[i][i]*. Similarly,
the *num[0][2]* has to be the same as *num[1][3]* and *num[i-2][i]*.

Then, we have that for every `num[i][j] == num[i-1][j-1]`. I.e. the current number has to be the
same as the element in the row above and column before.

**Time: O(n\*m) <br> Space: O(1)**

### Go solution
```go
func isToeplitzMatrix(matrix [][]int) bool {
    for i:= 1; i < len(matrix); i++ {
        for j := 1; j < len(matrix[0]); j++ {
            if matrix[i][j] != matrix[i-1][j-1] {
                return false
            }
        }
    }
    return true
}
```
