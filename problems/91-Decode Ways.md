# 91. Decode Ways (Medium)

- Example 1:
> **Input**: [2,1,5] <br>
> **Output**: [5,5,0] <br>
> **Explanation**:
- Example 2:
> **Input**: [2,7,4,3,5] <br>
> **Output**: [7,0,5,5,0] <br>
> **Explanation**:

## Brute Force - Recursion
Use a plain recursion. Keep track of the index of the char to be processed, in case it is a '0' then
there's no answer. Then, recurse over the next char, and addidionally, if the current char and the
next char make a *'10-19'* or *'20-26'*, we have more ways to decode it, so add them to the answer.

**Time: O(n²) <br> Space: O(1)**

```go
func numDecodings(s string) int {
    return decode(s, 0)
}

func decode(str string, idx int) int {
    if idx == len(str) {
        return 1
    }
    if str[idx] == '0' {
        return 0
    }
    ans := decode(str, idx + 1)
    
    if idx + 1 < len(str) {
        if str[idx] == '1' || ( str[idx] == '2' && str[idx+1] <= '6') {
            ans += decode(str, idx + 2)
        }
    }
    
    return ans
}
```

## Brute Force - Memoization
Same approach as before, but add a memo so we don't re-calculate same values.

```go
func numDecodings(s string) int {
    memo := make([]int, len(s)+1)
    return decode(s, 0, memo)
}

func decode(str string, idx int, memo []int) int {
    if idx == len(str) {
        return 1
    }
    if str[idx] == '0' {
        return 0
    }
    if memo[idx] != 0 {
        return memo[idx]
    }
    ans := decode(str, idx + 1, memo)
    
    if idx + 1 < len(str) {
        if str[idx] == '1' || ( str[idx] == '2' && str[idx+1] <= '6') {
            ans += decode(str, idx + 2, memo)
        }
    }
    memo[idx] = ans
    
    return ans
}
```

## Optimal I - DP
We can maintain a **dp** solutions array, and for each char index we evaluate if it produces a valid
decoding.
- If the curr char is not '0', then it extends the previous solution: *dp[i] = dp[i-1]*.
- If the curr and prev chars are 10-26, then it extends the prev's prev solution: *dp[i] = dp[i-2]*.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func numDecodings(s string) int {
    n := len(s)
    // make dp of size n + 1
    dp := make([]int, n + 1)
    // initialize so in case of 10-26, dp[2] += dp[0] is valid
    dp[0] = 1
    // if the first char is not '0', there's one way to decode it
    if s[0] != '0' {
        dp[1] = 1
    }
    
    for i := 2; i <= n; i++ {
        curr, prev := s[i-1], s[i-2]
        // if not 0, the ways do decode are same as the previous one
        if curr != '0' {
            dp[i] = dp[i-1]
        }
        // if is 10-19 or 20-26, add the prev's prev value
        if prev == '1' || (prev  == '2' && curr <= '6') {
            dp[i] += dp[i-2]
        }
    }
    
    return dp[n]
}
```

## Optimal - DP
If we closely observe, we just care about the last two values of the **dp** array, so we can use
two variables instead, so we reduce the space complexity.

**Time: O(n) <br> Space: O(1)**

### Java solution
```java
class Solution {
    public int numDecodings(String s) {
        int n = s.length();
        if(n == 0)
            return 0;
        
        int prevPrev = 1;
        int prev = s.charAt(0) != '0' ? 1 : 0;
        
        for(int i = 1; i < n; i++) {
            char currC = s.charAt(i);
            char prevC = s.charAt(i-1);
            int temp = 0;
            // curr char is valid, so it extends prev solution
            if(currC != '0')
                temp = prev;
            // last two digits are valid, so extend prev's prev solution
            if(prevC == '1' || (prevC == '2' && currC <= '6'))
                temp += prevPrev;
            
            prevPrev = prev;
            prev = temp;
        }
        return prev;
    }
}
```