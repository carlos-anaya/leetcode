# 367. Valid Perfect Square (Easy)

Given a positive integer `num`, write a function which returns *True* if num is a perfect square.

**Note**: **Do not** use any built-in library function such as sqrt.

- Example 1:
> **Input**: 16 <br>
> **Output**: True
- Example 2:
> **Input**: 14 <br>
> **Output**: False

## Brute Force
Loop while *i² <= n*, from *i=0*. Stop when *i²>n*.

**Time: O(√n) <br> Space: O(1)**

## Aproach I - Math
"A square number is the sum of the previous odd numbers", e.g.
> 1 = 1 <br>
> 4 = 1 + 3 <br>
> 9 = 1 + 3 + 5 <br>
> 16 = 1 + 3 + 5 + 7 <br>
> 25 = 1 + 3 + 5 + 7 + 9

Then we can just have a loop from *i=1,i+=2*, adding the values of *i* to a *sum* and check if the
sum is equal to *num*. Stop when the sum exceeds *num*.

**Time: O(√n)** <br> 
**Space: O(1)**

```java
class Solution {
    public boolean isPerfectSquare(int num) {
        for(int i = 1, sum = 0; sum < num; i += 2) {
            sum += i;
            if(sum == num)
                return true;
        }
        return false;
    }
}
```

## Optimal
Use a *binary search* modification.

**Time: O(logn) <br> Space: O(1)**

### Go solution
```go
func isPerfectSquare(num int) bool {
    if num == 0 {
        return false
    }
    l, r := 0, num // we can start from num/2 since sqrt(n) < n/2
    for l <= r {
        m := (r-l)/2 + l
        if m * m == num {
            return true
        } else if (m *m < num) {
            l = m+1
        } else {
            r = m-1
        }
    }
    return false
}
```
### Java solution
```java
class Solution {
    public boolean isPerfectSquare(int num) {
        if(num == 0)
            return false;
        // use long to avoid overflowing when m*m
        long l = 0, r = num;
        while(l <= r) {
            long m = (r-l)/2 + l;
            if(m*m == num)
                return true;
            else if(m*m < num)
                l = m + 1;
            else
                r = m - 1;
        }
        return false;
    }
}
```