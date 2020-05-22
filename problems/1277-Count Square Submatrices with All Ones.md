# 1277. Count Square Submatrices with All Ones (Medium)

Given a `m * n` matrix of ones and zeros, return how many **square** submatrices have all ones.

- Example 1:
> **Input**: matrix = [ <br>
> [0,1,1,1], <br>
> [1,1,1,1], <br>
> [0,1,1,1]] <br>
> **Output**: 15 <br>
> **Explanation**: There are **10** squares of side **1**. <br>
> There are **4** squares of side **2**. <br>
> There is **1** square of side **3**. <br>
> Total number of squares = 10 + 4 + 1 = 15.

## Optimal
Based on the solution in [221-Maximal Square](221-Maximal%20Square.md). We can keep a *dp matrix* to
keep track of the squares we can make. In case theres a *1* at position *i,j*, we check if its
neighbors are *1* too.

- if *matrix[i][j] == 1* then *dp[i][j] = min(dp[i-1][j], dp[i-1][j-1], dp[i][j-1]) + 1*

**Time: O(n\*m) <br> Space: O(1)**

### Go solution
```go
func countSquares(matrix [][]int) int {
    ans := 0
    for i := 0; i < len(matrix); i++ {
        for j := 0; j < len(matrix[0]); j++ {
            if i > 0 && j > 0 && matrix[i][j] == 1{
                matrix[i][j] = min(matrix[i-1][j-1], 
                                   min(matrix[i-1][j], matrix[i][j-1])) + 1
            }
            ans += matrix[i][j]
        }
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public int countSquares(int[][] matrix) {
        int ans = 0;
        for(int i = 0; i < matrix.length; i++) {
            for(int j = 0; j < matrix[0].length; j++) {
                if(i > 0 && j > 0 && matrix[i][j] == 1) {
                    matrix[i][j] = 1 + Math.min(
                        Math.min(matrix[i-1][j], matrix[i][j-1]), matrix[i-1][j-1]);
                }
                ans += matrix[i][j];
            }
        }
        return ans;
    }
}
```