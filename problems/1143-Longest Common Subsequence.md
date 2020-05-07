# 1143. Longest Common Subsequence

Given two strings **text1** and **text2**, return the length of their longest common subsequence.

A *subsequence* of a string is a new string generated from the original string with some characters
(can be none) deleted without changing the relative order of the remaining characters. (eg, "ace" is
a subsequence of "abcde" while "aec" is not). A common subsequence of two strings is a subsequence 
that is common to both strings.

If there is no common subsequence, return 0.

- Example 1:
> **Input**: text1 = "abcde", text2 = "ace" <br>
> **Output**: 3 <br>
> **Explanation**: The longest common subsequence is "ace" and its length is 3.
- Example 2:
> **Input**: text1 = "abc", text2 = "def" <br>
> **Output**: 0 <br>
> **Explanation**:

## Brute Force
The easiest way is to use a *plain recursive function*. We start from the end of both strings and
check if the current chars are equal, if so, recurse and advance 1 char for each string. Else,
we have to explore the recursion for each string advancing one char each and return the max:

```go
func longestCommonSubsequence(text1 string, text2 string) int {
    return helper(text1, text2, len(text1) - 1, len(text2) - 1)
}

func helper(text1, text2 string, i, j int) int {
    if i < 0 || j < 0 {
        return 0
    }
    
    if text1[i] == text2[j] {
        return 1 + helper(text1, text2, i-1, j-1)
    }
    
    return max(helper(text1, text2, i-1, j),
               helper(text1, text2, i, j-1))
}
```

**Time: O(n²) <br> Space: O(1)**

## Optimal - DP (bottom-up)
This problem is an ideal candidate for a dynamic programming approach. Consider the table below.
We can check character by character by character. 

- If we have a match (text1[j] == text2[i]) then we have to add 1 to our **answer**, but what is
the *prev answer*? It's the best we did when we compared *text1[j-1] and text2[i-1]*. But why?
because the current char is a match for both strings.
- If there's no match, then the best we can do is the max when *text1[j-1] and text2[i]* or
*text1[j] and text2[i-1]*, i.e. what's the best we can do ignoring the current char (no match) in
any of the strings.

. | "" | b | c | a | a | a | b
--- | --- | --- | --- | --- | --- | --- | --- 
"" | 0 | 0 | 0 | 0 | 0 | 0 | 0
a | 0 | 0 | 0 | 1 | 1 | 1 | 1
a | 0 | 0 | 0 | 1 | 2 | 2 | 2
b | 0 | 1 | 1 | 1 | 2 | 2 | 3

**Time: O(n*m) <br> Space: O(min(n,m))**

**NOTE**: we don't need to create the whole matrix, we just care of the previous state (row) and
the current row's prev value (since we're processing the current row). We're using *min(n,m)* since
at the beginning of the process we can check which string is shorter and use it as base for the
*dp helper array*.

### Go solution
```go
func longestCommonSubsequence(text1 string, text2 string) int {
    m, n := len(text1), len(text2)
    dpPrev := make([]int, m + 1)
    
    for i := 1; i <= n; i++ {
        dpCurr := make([]int, m + 1)
        for j := 1; j <= m; j++ {
            dpCurr[j] = max(dpCurr[j-1], dpPrev[j])
            if text2[i-1] == text1[j-1] {
                dpCurr[j] = dpPrev[j-1] + 1
            }
        }
        dpPrev = dpCurr
    }
    return dpPrev[m]
}
```
### Java solution
```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        // ensure text1 the shortest string
        if(text1.length() > text2.length()) {
            String tmp = text2;
            text2 = text1;
            text1 = tmp;
        }
        
        int n = text1.length(), m = text2.length();
        int[] prev = new int[n+1];
        
        for(int i = 1; i <= m; i++) {
            int[] curr = new int[n+1];
            for(int j = 1; j <= n; j++) {
                if(text1.charAt(j-1) == text2.charAt(i-1)) {
                    curr[j] = 1 + prev[j-1];
                } else {
                    curr[j] = Math.max(curr[j-1], prev[j]);
                }
            }
            prev = curr;
        }
        return prev[n];
    }
}
```