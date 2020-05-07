# 221. Maximal Square

Given a 2D binary matrix filled with 0's and 1's, find the *largest square* containing only 1's and
return its area.

- Example:
> **Input**:
> 1 0 1 0 0 <br>
> 1 0 **1 1** 1 <br>
> 1 1 **1 1** 1 <br>
> 1 0 0 1 0 <br>
> **Output**: 4 <br>
> **Explanation**: The largest square has an area of 4 units

## Brute Force
Nested loop: traverse each cell, from left to right, top to bottom until a `1` is found. When found
at let's say position *i, j*, move to position *i+1, j+1* (diagonal) and if it's a `1`, the check
that row *(i+1, j)* and that column *(i, j+1)*. When a `0` is found, stop immediately and return
the previous area. If all were `1's` then move diagonally again (*i+2, j+2*) and inspect the rows
and cols again.

**Time: O((n\*m)²) <br> Space: O(1)**

## Optimal - DP
Consider the Matrix below. At position *i=1, j=1* we see that we can have a square of size 2\*2, but
at position *i=1, j=3* we don't have a square because the element at *i=0, j=3* is a *0*. So, what
do info do we need at position *i, j* so we know we can make a square? First, *matrix[i][j]* has to 
be a *1*, and then we need to ensure that elements at *<i-1, j>*, *<i-1, j-1>* and *<i, j-1>* are 
not *0's*. The maximum lenght of the square is equal to the minimum of the surrounding values plus 1

- if *matrix[i][j] == 1* then *dp[i][j] = min(dp[i-1][j], dp[i-1][j-1], dp[i][j-1]) + 1*

Original Matrix

0 | 1 | 2 | 3 | 4
--- | --- | --- | --- | ---
1 | 1 | 1 | 0 | 0
1 | 1 | 1 | 1 | 0
1 | 1 | 1 | 1 | 1
1 | 0 | 1 | 1 | 0

DP Matrix

0 | 1 | 2 | 3 | 4
--- | --- | --- | --- | ---
1 | 1 | 1 | 0 | 0
1 | 2 | 2 | 1 | 0
1 | 2 | 3 | 2 | 1
1 | 0 | 1 | 2 | 0

**Time: O(n\*m) <br> Space: O(m)**

### Go solution
```go
func maximalSquare(matrix [][]byte) int {
    if len(matrix) == 0 { // edge case empty matrix
        return 0
    }
    n, m := len(matrix), len(matrix[0])
    maxSq := 0
    prev := make([]int, m)
    
    for j := range matrix[0] {
        if matrix[0][j] == '1' {
            prev[j] = 1
            maxSq = 1 // edge case rows == 1
        }
    }
    
    for i := 1; i < n; i++ {
        curr := make([]int, m)
        if matrix[i][0] == '1' {
            curr[0] = 1
            maxSq = max(maxSq, 1) // edge case cols == 1
        }
        
        for j := 1; j < m; j++ {
            if matrix[i][j] == '1' {
                curr[j] = 1 + min(min(curr[j-1], prev[j-1]), prev[j])
                maxSq = max(maxSq, curr[j])
            }
        }
        prev = curr
    }
    
    return maxSq * maxSq
}
```
### Java solution
```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        int n = matrix.length;
        if(n == 0) {
            return 0;
        }
        int m = matrix[0].length;
        
        int max = 0;
        int[] curr = new int[m], prev = new int[m];
        for(int j = 0; j < m; j++) {
            if(matrix[0][j] == '1') {
                prev[j] = 1;
                max = 1;
            }
        }
        
        for(int i = 1; i < n; i++) {
            curr = new int[m];
            if(matrix[i][0] == '1') {
                curr[0] = 1;
                max = Math.max(max, 1);
            }
            for(int j = 1; j < m; j++) {
                if(matrix[i][j] == '1') {
                    curr[j] = Math.min(curr[j-1], Math.min(prev[j], prev[j-1])) + 1;
                    max = Math.max(max, curr[j]);
                }
            }
            prev = curr;
        }
        
        return max * max;
    }
}
```