# 476. Number Complement

Given a positive integer, output its complement number. The complement strategy is to flip the bits of its binary representation.

- Example 1:
> **Input**: 5 <br>
> **Output**: 2 <br>
> **Explanation**: The binary representation of 5 is 101 and its complement is 010 (2)
- Example 2:
> **Input**: 10 <br>
> **Output**: 5 <br>
> **Explanation**: The binary representation of 10 is 1010 and its complement is 0101 (5)
- Example 3:
> **Input**: 1 <br>
> **Output**: 0
- Example 4:
> **Input**: 0 <br>
> **Output**: 1

## Optimal
We see that the complement is the difference between the next **all set bits number** and 
**number**. For example, number 10 in binary is *(1010)* and the all-set-bits next number is 15
*(1111)*. The difference is the complement: *1111 - 1010 = 101*.

To find the all-set-bits next number, we can find the number of bits by shifting to the left until
the number becomes 0, keeping track of shifts. E.g. *1010 >> 4 == 0*. Next we shift 1 to the right:
*1 << 4 == 10000* and if we take 1 from it: *1 << 4 - 1 == 10000 - 1 = 1111*. Finally we take num:
*1111 - 1010 = 101*.

**Time: O(1) <br> Space: O(n)**

### Go solution
```go
func findComplement(num int) int {
    shifts, n := 0, num
    // find the number of shifts
    for n != 0 {
        n >>= 1
        shifts++
    }
    // 1 << shifts - 1 == all-bits-set number
    return 1 << shifts - 1 - num
}
```
### Java solution
```java
class Solution {
    public int findComplement(int num) {
        int shifts = 0, n = num;
        while(n != 0) {
            n >>= 1;
            shifts++;
        }
        return (1 << shifts) - 1 - num;
    }
}
```