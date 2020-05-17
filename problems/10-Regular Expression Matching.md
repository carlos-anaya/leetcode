# 10. Regular Expression Matching (Hard)

Given an input string `s` and a pattern `p`, implement a *regular expression matching* with support
for `.` and `*`. The char `.` matches any single character and the `*` char matches zero or more of
the preceding element.

Note:
- The matching should cover the **entire** input string (not partial).
- `s` could be empty and contains only lowercase letters a-z.
- `p` could be empty and contains only lowercase letters a-z, and characters like `.` or `*`.

- Example 1:
> **Input**: s = "aa" p = "a" <br>
> **Output**: false
- Example 2:
> **Input**: s = "aa" p = "a\*" <br>
> **Output**: true
- Example 3:
> **Input**: s = "ab" p = ".\*" <br>
> **Output**: true
- Example 4:
> **Input**: s = "aab" p = "c\*a\*b" <br>
> **Output**: true
- Example 5:
> **Input**: s = "mississippi" p = "mis\*is\*p\*." <br>
> **Output**: false

## Brute Force - Recursion
This can be solved easily with a recursive call, having two pointers one for the string and other
for the pattern and then, evaluating char by char.

The only concern is when the pattern has a `\*`. For this case we check if the pattern next char is
a star (*if p[i+1] == '\*'*) so we know if we have to match muliple chars in *s* or 0 chars so we
skip the pattern.

The base case of the recursion is as follows:
- End of string and end of pattern, return true
- End of string but pattern not complete:
  - If pattern has a `.` or a char and a `\*` we skip the pattern
  - Pattern doesn't have `\*`, thus return false
- End of pattern but chars in string missing, return false

**Time: O((S+P)2^(S+P)) <br> Space: O(S²+P²)**

### Go solution
```go
// use a struct with the string, pattern and length to make the recursive call more readable
type Data struct {
    str []rune
    pat []rune
    lenS int
    lenP int
}

func isMatch(s string, p string) bool {
    data := Data{[]rune(s), []rune(p), len(s), len(p)}
    return helper(&data, 0, 0)
}

func helper(d *Data, s, p int) bool {
    // check if there's a star in the pattern
    hasStar := p + 1 < d.lenP && d.pat[p+1] == '*'
    // base case, end of string
    if s >= d.lenS {
        // end of pattern
        if p >= d.lenP {
            return true
        }
        // pattern not completed but having star
        if hasStar {
            return helper(d, s, p+2)
        }
        // pattern not completed
        return false
    }
    // end of pattern but string chars left
    if p >= d.lenP && s < d.lenS {
        return false
    }
    
    if hasStar {
        // if any char or chars match, the we check if two cases:
        // skipping the pattern or consuming a char
        if d.pat[p] == '.' || d.str[s] == d.pat[p] {
            return helper(d, s, p+2) ||
                helper(d, s+1, p)
        }
        // char didn't match, so we skip the pattern
        return helper(d, s, p+2)
    }
    
    // no star, so we move to the next char and pattern if
    // there is a '.' or the chars match
    if d.pat[p] == '.' || d.str[s] == d.pat[p] {
        return helper(d, s+1, p+1) 
    } 
    
    return false
}
```

## Optimal - DP Bottom-up
We can do a *bottom-up* algorithm by using a **dp** boolean matrix. We check char by char if the
*pattern* and *string* match. The steps are as follows.

- We start with *empty strings* for both *pattern* and *string* and that is a match, so we have 
  **dp[0][0]=true**. The *dp matrix* is of size *(lenS+1)\*(lenP+1)*. 
- Loop over *pattern (i)* and *string (j)*. If *currP* is `.` or a char and it matches *currS* then
  **dp[i][j]=dp[i-1][j-1]**. I.e. if there's a match, we're expanding the result of *pattern* and 
  *string* before adding *currP* and *currS*.
- If *currP* is a `\*`, we have two options:
  - If *prevP* matches *currS* or it is a `.`, then **dp[i][j]=dp[i][j-1]** because we're expanding
    the result of *string* before adding a repeated char.
  - If *prevP* doesn't match *currS*, then **dp[i][j]=dp[i-2][j]** because we're expanding
    the result of *pattern* skipping the *star* and the *prevP*.
- **Note** an *edge-case* when *s=""* and *p=a\** there's a match. Thus, we have to initialize the
  first column in case there's a `*`: **dp[i][0] = dp[i-2][0];**

. | "" | a | a | b | x
--- | --- | --- | --- | --- | ---
"" | 1 | 0 | 0 | 0 | 0
c | 0 | 0 | 0 | 0 | 0
\* | 1 | 0 | 0 | 0 | 0
a | 0 | 1 | 0 | 0 | 0
\* | 1 | 1 | 1 | 0 | 0
b | 0 | 0 | 0 | 1 | 0
. | 0 | 0 | 0 | 0 | 1

**Time: O(S\*P) <br> Space: O(S\*P)**


### Java solution
```java
class Solution {
    final char STAR = '*';
    final char DOT = '.';
    
    public boolean isMatch(String s, String p) {
        int lenS = s.length(), lenP = p.length();
        boolean[][] dp = new boolean[lenP+1][lenS+1];
        // empty strings are a match
        dp[0][0] = true;
        
        for(int i = 1; i <= lenP; i++) {
            char currP = p.charAt(i - 1);
            
            // initialize col 0, there might be a char or dot 
            // followed by a star that is a match for an empty string
            if(currP == STAR) {
                dp[i][0] = dp[i-2][0];
            }
            
            for (int j = 1; j <= lenS; j++) {
                char currS = s.charAt(j - 1);
                if(currP == STAR) {
                    char prevP = p.charAt(i - 2);
                    boolean prevMatch = prevP == DOT || prevP == currS;
                    // have a star and the prev char is dot or matches char in s
                    dp[i][j] = prevMatch && dp[i][j-1];
                    // have a star but didn't match, then we take the value as if
                    // we skipped the pattern
                    dp[i][j] = dp[i][j] || dp[i-2][j];
                } else {
                    // no star and pattern matches a dot or char in s
                    if(currP == DOT || currP == currS) {
                        dp[i][j] = dp[i-1][j-1];
                    }
                }
            }
        }
        
        return dp[lenP][lenS];
    }
}
```