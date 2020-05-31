# 680. Valid Palindrome II (Easy)

Given a non-empty string `s`, you may delete **at most one** character. Judge whether you can make
`s` a palindrome.

- Example 1:
> **Input**: "aba" <br>
> **Output**: true <br>
> **Explanation**: string is already a palindrome
- Example 2:
> **Input**: "abca" <br>
> **Output**: true <br>
> **Explanation**: remove either b or c to make it a palindrome

## Optimal
Loop over the string using `two pointers`. Once there's a mismatch, check if removing one char from
the left or the right make a valid palindrome.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func validPalindrome(s string) bool {
    l, r := 0, len(s)-1
    for l < r {
        // if there's a mismatch, check if skipping a char
        // to the right or to the left yields an answer
        if s[l] != s[r] {
            return isRangePalindrome(s, l, r-1) || 
                   isRangePalindrome(s, l+1, r)
        }
        l++
        r--
    }
    return true
}

// check whether the string in range l..r is a palindrome
func isRangePalindrome(s string, l, r int) bool {
    for l < r {
        if s[l] != s[r] {
            return false
        }
        l++
        r--
    }
    return true
}
```
### Java solution
```java
class Solution {
    public boolean validPalindrome(String s) {
        int l = 0, r = s.length() - 1;
        while(l < r) {
            if(s.charAt(l) != s.charAt(r))
                return isPalindromeRange(s, l+1, r) ||
                        isPalindromeRange(s, l, r-1);
            l++;
            r--;
        }
        return true;
    }
    
    private boolean isPalindromeRange(String s, int l, int r) {
        while(l < r) {
            if(s.charAt(l) != s.charAt(r))
                return false;
            l++;
            r--;
        }
        return true;
    }
}
```