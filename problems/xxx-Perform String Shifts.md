# xxx. Perform String Shifts

You are given a string **s** containing lowercase English letters, and a matrix **shift**, where 
`shift[i] = [direction, amount]`:

- `direction` can be 0 (for left shift) or 1 (for right shift). 
- `amount` is the amount by which string s is to be shifted.
- A left shift by 1 means remove the first character of s and append it to the end.
- Similarly, a right shift by 1 means remove the last character of s and add it to the beginning.

Return the final string after all operations.

- Example 1:
> **Input**: s = "abc", shift = [[0,1],[1,2]] <br>
> **Output**: "cab" <br>
> **Explanation**: <br>
> [0,1] means shift to left by 1. "abc" -> "bca" <br>
> [1,2] means shift to right by 2. "bca" -> "cab"
- Example 2:
> **Input**: s = "abcdefg", shift = [[1,1],[1,1],[0,2],[1,3]] <br>
> **Output**: "efgabcd" <br>
> **Explanation**: <br>
> [1,1] means shift to right by 1. "abcdefg" -> "gabcdef" <br>
> [1,1] means shift to right by 1. "gabcdef" -> "fgabcde" <br>
> [0,2] means shift to left by 2. "fgabcde" -> "abcdefg" <br>
> [1,3] means shift to right by 3. "abcdefg" -> "efgabcd"

## Optimal
- Use a variable to count the number of shifts to the **right (increase)** and **left (decrease)**
- Set the count variable to **mod** len(s) to avoid full shifts
- Given that *n right shifts* = *n - len(s) left shifts*, we can just do one type of shift

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func stringShift(s string, shift [][]int) string {
    total := 0
    for _, s := range shift {
        if s[0] == 0 {
            total -= s[1]
        } else {
            total += s[1]
        }
    }
    // use module, so complete shifts are ignored
    total = total % len(s)
    // if total is negative, adding len(s) is equivalent to rigth shifts
    if total < 0 {
        total += len(s)
    }
    // in right shifts the start of the string is len(s) - shifts
    total = len(s) - total
    
    return s[total:] + s[:total]
}
```
### Java solution
```java
class Solution {
    public String stringShift(String s, int[][] shift) {
        int count = 0;
        for(int[] sh : shift) {
            if(sh[0] == 0) {
                count -= sh[1];
            } else {
                count += sh[1];
            }
        }
        // use module, so complete shifts are ignored
        count = count % s.length();
        // if count is negative, add len(s) to make it equivalent in rigth shifts
        if(count < 0) {
            count += s.length();
        }
        // in right shifts the start of the string is len(s) - shifts
        count = s.length() - count;
        return s.substring(count, s.length()) + s.substring(0, count);
    }
}
```