# Leftmost Column with at Least a One

A **binary matrix** means that all elements are `0` or `1`. For each individual row of the matrix, 
this row is sorted in *non-decreasing order*.

Given a row-sorted binary matrix, return *leftmost column index* with at least a `1` in it.
If such index doesn't exist, return -1.

You can't access the Binary Matrix directly. You may only access the matrix using a **BinaryMatrix**
interface:

- `BinaryMatrix.get(x, y)` returns the element of the matrix at index (x, y) (0-indexed).
- `BinaryMatrix.dimensions()` returns a list of 2 elements [m, n], which means the matrix is m * n.


- Example 1:
> **Input**: <br>
> [[0,0] <br>
> ,[0,0]] <br>
> **Output**: -1 
- Example 2:
> **Input**: [<br>
> [[0,0] <br>
> ,[0,1]] <br>
> **Output**: 1
- Example 3:
> **Input**: <br>
> [[0,0] <br>
> ,[1,1]] <br>
> **Output**: 0
- Example 4:
> **Input**: <br> 
> [[0,0,1,1] <br>
> ,[0,0,0,1] <br>
> ,[0,1,1,1]] <br>
> **Output**: 1

## Brute Force
Nested loop: start from **i = 0 to n, j = 0 to m**, keep track of the **left-most j** when a 
**1** is found.

**Time: O(n²) <br> Space: O(1)**

## Sub-optimal
Do a **binary search** in each row, finding the *first one*. When found, go to the row below and do
again a binary search, using the index of the *first one* as the right index for the binary search.

**Time: O(n\*logm) <br> Space: O(1)**

## Optimal
Start at the top-left and move left or down depending on the value of *binaryMatrix.get(i, j)*:
- If the value is 0, it means there are no ones to the left, so we go down.
- If the value is 1, we update our curr *answer* and go left, looking for a better *answer*. (no 
point on going right, since this is alreay an answer)

This approach is like following stair, taking advantage of the sorted rows.

**Time: O(n + m) <br> Space: O(n)**

### Go solution
```go
func leftMostColumnWithOne(binaryMatrix BinaryMatrix) int {
    n, m := binaryMatrix.Dimensions()[0], binaryMatrix.Dimensions()[1]
    ans, i, j := -1, 0, m - 1
    
    for i < n && j >= 0 {
        if binaryMatrix.Get(i, j) == 0 {
            i++
        } else {
            ans = j
            j--
        }
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public int leftMostColumnWithOne(BinaryMatrix binaryMatrix) {
        int m = binaryMatrix.dimensions().get(1);
        int n = binaryMatrix.dimensions().get(0);
        int i = 0, j = m - 1, ans = -1;
        
        while(i < n && j >= 0) {
            if(binaryMatrix.get(i, j) == 1) {
                ans = j;
                j--;
            } else {
                i++;
            }
        }
        return ans;
    }
}
```