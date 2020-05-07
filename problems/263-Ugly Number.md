# 263. Ugly Number
Write a program to check whether a given number is an **ugly number**.
Ugly numbers are positive numbers whose prime factors only include `2, 3, 5`.

- Example 1:
> **Input**: 6 <br>
> **Output**: true <br>
> **Explanation**: 6 = 2 * 3 <br>
- Example 2:
> **Input**: 50 <br>
> **Output**: true <br>
> **Explanation**: 50 = 2 * 5 *5 <br>
- Example 3:
> **Input**: 7 <br>
> **Output**: false <br>

## Optimal
Divide as much as possible *num* by 2, 3 or 5. At the end return `num == 1`.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func isUgly(num int) bool {
    if num < 1 {
        return false
    }
    num = divide(num, 2)
    num = divide(num, 3)
    num = divide(num, 5)
    
    return num == 1
}

func divide(a, b int) int {
    for a % b == 0 {
        a /= b
    }
    return a
}
```
### Java solution
```java
class Solution {
    public boolean isUgly(int num) {
        if(num < 1) {
            return false;
        }
        
        num = divide(num, 2);
        num = divide(num, 3);
        num = divide(num, 5);
        
        return num == 1;
    }
    
    private int divide(int a, int b) {
        while(a % b == 0) {
            a /= b;
        }
        return a;
    }
}
```