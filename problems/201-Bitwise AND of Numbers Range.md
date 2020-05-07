# 201. Bitwise AND of Numbers Range

Given a range [m, n] where 0 <= m <= n <= 2147483647, return the bitwise AND of all numbers in this
range, inclusive.

- Example 1:
> **Input**: [0, 1] <br>
> **Output**: 0 <br>
> **Explanation**: 0 AND 1 = 0
- Example 2:
> **Input**: [5, 6, 7] <br>
> **Output**: 4 <br>
> **Explanation**: 101 AND 110 AND 111 = 100

## Brute Force
Loop from m to n, use a variable to keep track of the answer

```go
func rangeBitwiseAnd(m int, n int) int {
    ans := m
    for m = m + 1; m <= n; m++ {
        ans &= m
    }
    return ans
}
```

**Time: O(n) <br> Space: O(1)**

## Optimal
Assume we have 12(1100) and 15(1111), we see that they share the two most significant bits, so we
can do right shifts until they are the same, i.e. 3(11) after 2 shifts. Finally, we do 2 left shifts
of 3 so it becomes 12 (1100).

Why do we do this? Because, in the range from *m* to *n*, there will be *0's* that will make the
final *AND result* 0, so we simply skip those and use the most significant bits (that will be *1's*)
in the final *AND'ed* result.

**Time: O(1) <br> Space: O(1)**

### Go solution
```go
func rangeBitwiseAnd(m int, n int) int {
    shifts := 0
    for m != n {
        m >>= 1
        n >>= 1
        shifts++
    }
    return n << shifts
}
```
### Java solution
```java
class Solution {
    public int rangeBitwiseAnd(int m, int n) {
        int shifts = 0;
        while(m != n) {
            n >>= 1;
            m >>= 1;
            shifts++;
        }
        return n << shifts;
    }
}
```