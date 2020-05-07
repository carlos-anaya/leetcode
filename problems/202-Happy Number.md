# 202. Happy Number

Write an algorithm to determine if a number is *"happy"*.

A happy number is a number defined by the following process: Starting with any positive integer,
replace the number by the sum of the squares of its digits, and repeat the process until the number
equals 1 (where it will stay), or it loops endlessly in a cycle which does not include 1. Those 
numbers for which this process ends in 1 are happy numbers.

- Example 1:
> **Input**: 19 <br>
> **Output**: true <br>
> **Explanation**: <br>
> 1² + 9² = 82 <br>
> 8² + 2² = 68 <br>
> 6² + 8² = 100 <br>
> 1² + 0² + 0² = 1
- Example 2:
> **Input**: 2 <br>
> **Output**: false <br>
> **Explanation**: <br>
> 2² = 4 <br>
> 4² = 16 <br>
> 1² + 6² = 37 <br>
> 3² + 7² = 58 <br>
> 5² + 8² = 89 <br>
> 8² + 9² = 145 <br>
> 1² + 4² + 5² = 42 <br>
> 4² + 2² = 20 <br>
> 2² + 0² = 4 <br>
> 4² = 16 <br>

## Brute Force
From the example above, we see that after some tries a cycle appears. We can have a map/set to keep
track of the values. Return true if val == 1 or return false if we have seen that value before.

**Time: O(1) <br> Space: O(n)**

## Optimal
How do wedetect a **cycle** in a LinkedList? By using `two pointers (slow/fast)`. We can follow the
same approach here, and when the pointers meet, if they met in 1 then *n* is happy number.

**Time: O(1) <br> Space: O(1)**

### Go solution
```go
func isHappy(n int) bool {
    slow, fast := n, getSumOfDigits(n)
    for slow != fast {
        slow = getSumOfDigits(slow)
        fast = getSumOfDigits(getSumOfDigits(fast))
    }
    
    return slow == 1
}

func getSumOfDigits(n int) int {
    ans := 0
    for n > 0 {
        md := n % 10
        ans += md * md
        n /= 10
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public boolean isHappy(int n) {
        int slow = n;
        int fast = getDigitSum(n);
        
        while(slow != fast) {
            slow = getDigitSum(slow);
            fast = getDigitSum(getDigitSum(fast));
        }
        
        return slow == 1;
    }
    
    private int getDigitSum(int n) {
        int ans = 0;
        while(n > 0) {
            int mod = n % 10;
            ans += mod*mod;
            n /= 10;
        }
        return ans;
    }
}
```