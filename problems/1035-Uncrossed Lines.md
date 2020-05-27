# 1035. Uncrossed Lines (Medium)

We write the integers of `A` and `B` (in the order they are given) on two separate horizontal lines.

Now, we may draw connecting lines: a straight line connecting two numbers `A[i]` and `B[j]` such
that: **A[i] == B[j]**.

The line we draw does not intersect any other connecting (non-horizontal) line. Note that a
connecting lines cannot intersect even at the endpoints: each number can only belong to one
connecting line.

Return the maximum number of connecting lines we can draw in this way.

- Example 1:
> **Input**: A = [1,4,2], B = [1,2,4] <br>
> **Output**: 2 <br>
> **Explanation**: We can draw 2 uncrossed lines as in the diagram below:

![diagram](https://assets.leetcode.com/uploads/2019/04/26/142.png)

- Example 2:
> **Input**: A = [2,5,1,2,5], B = [10,5,2,1,5,2] <br>
> **Output**: 3
- Example 3:
> **Input**: A = [1,3,7,1,7,5], B = [1,9,2,5,1] <br>
> **Output**: 2

## Brute Force - Recursion
If there's a match we add one to the result of skipping the num in both arrays. If no match we take
the max of skipping the number in either array.

```go
func maxUncrossedLines(A []int, B []int) int {
    return rec(A, B, 0,0)
}

func rec(arr1, arr2 []int, i1, i2 int) int {
    // base case, either arr is over, so no possible match
    if i >= len(arr1) || j >= len(arr2) {
        return 0
    }
    // match is 1 + plus result skipping the num in each arr
    if arr1[i] == arr2[j] {
        return 1 + rec(arr1, arr2, i+1, j+1)
    }
    // no match, return best of skipping a num in either arr
    return max(rec(arr1, arr2, i+1, j), rec(arr1, arr2, i, j+1))
}
```

**Time: O(2^n)** (n = min(arr1.length, arr2.length)) <br> **Space: O(n)**

## Optimal
This problem can be solved with a *dynamic programming* approach. In fact, the idea and code is very
similar to [583-Delete Operation for Two Strings](583-Delete%20Operation%20for%20Two%20Strings.md) 
and it's the same as [1143-Longest Common Subsequence](1143-Longest%20Common%20Subsequence.md).

Instead of the *min edit distance* we're looking for the *max match ie. longest common subsequence*,
so for each match we add one to the previous solution, this is **dp[i][j] = 1 + dp[i-1][j-1]**. If
there's no match, the optimal solution is the max of skipping the element in either array, this is 
**dp[i][j] = max(dp[i-1][j], dp[i][j-1])**.

**Time: O(n\*m) <br> Space: O(min(n, m))**

### Go solution
```go
func maxUncrossedLines(A []int, B []int) int {
    lenA, lenB := len(A), len(B)
    prev := make([]int, lenB + 1)
    // loop over chars in A and B
    for i := 1; i <= lenA; i++ {
        curr := make([]int, lenB + 1)
        for j := 1; j <= lenB; j++ {
            // a match adds one to the prev val
            if A[i-1] == B[j-1] {
                curr[j] = 1 + prev[j-1]
            } else {
                // if no match take the best we can do without
                // considering any of the numbers
                curr[j] = max(prev[j], curr[j-1])
            }
        }
        prev = curr
    }            
    return prev[lenB]
}
```
### Java solution
```java
class Solution {
    public int maxUncrossedLines(int[] A, int[] B) {
        int[] prev = new int[B.length+1];
        for(int i = 1; i <= A.length; i++) {
            int[] curr = new int[B.length+1];
            for(int j = 1; j <= B.length; j++) {
                if(A[i-1] == B[j-1])
                    curr[j] = 1 + prev[j-1];
                else
                    curr[j] = Math.max(curr[j-1], prev[j]);
            }
            prev = curr;
        }
        return prev[B.length];
    }
}
```