# 583. Delete Operation for Two Strings (Medium)

Given two words `word1` and `word2`, find the *minimum number of steps* required to make `word1` and
`word2` the **same**, where in each step you can *delete one character* in either string.

- Example 1:
> **Input**: "sea", "eat" <br>
> **Output**: 2 <br>
> **Explanation**: You need one step to make "sea" to "ea" and another step to make "eat" to "ea".

## Brute Force
We can do a simple *recursion*. When there's a match we skip the character in both strings. If there
is no match, we add return the min result of skipping one char in each word plus one.

**Time: O(2^n)** (n = min(w1.length, w2.length)) <br> **Space: O(n)**

```go
func minDistance(word1 string, word2 string) int {
    return minDist(word1, word2, 0, 0)
}

func minDist(word1, word2 string, w1, w2 int) int {
    // base case, end of word1 and chars in w2 remaining
    if w1 >= len(word1) {
        return len(word2) - w2
    }
    // base case, end of word2 and chars in w1 remaining
    if w2 >= len(word2) {
        return len(word1) - w1
    }
    
    // match, check next char in each word
    if word1[w1] == word2[w2] {
        return minDist(word1, word2, w1+1, w2+1)
    }
    // no match, 1 plus the best of deleting a char in either word
    return 1 + min(minDist(word1, word2, w1, w2+1),
                   minDist(word1, word2, w1+1, w2))
}
```

## Optimal
Similar to [72-Edit Distance](72-Edit%20Distance.md) but without considering replacements.
We can use a *dp array*. We see that if there's a match the result is the same as before adding the
curr char, i.e. **dp[i][j] = dp[i-1][j-1]**. Otherwise, we should add one to the best option we can
do by skipping one char in each word, i.e. **dp[i][j] = 1 + min(dp[i][j-1], dp[i-1][j])**.

**Time: O(w1.length\*w2.length) <br> Space: O(min(w1.length, w2.length))**

### Go solution
```go
func minDistance(word1 string, word2 string) int {
    prev := make([]int, len(word2) + 1)
    // initialize first row
    for j := 1; j <= len(word2); j++ {
        prev[j] = j
    }
    for i := 1; i <= len(word1); i++ {
        curr := make([]int, len(word2) + 1)
        curr[0] = i // initialize each col
        for j := 1; j <= len(word2); j++ { 
            if word1[i-1] == word2[j-1] {
                curr[j] = prev[j-1] // match, so no cost
            } else {
                curr[j] = 1 + min(prev[j], curr[j-1]) // cost is 1 + prev best
            }
        }
        prev = curr
    }
    return prev[len(word2)]
}
```
### Java solution
```java
class Solution {
    public int minDistance(String word1, String word2) {
        // use just one dp array instead of prev/curr
        int[] dp = new int[word2.length() + 1];
        // initialize first row
        for(int j = 1; j <= word2.length(); j++)
            dp[j] = j;
        // use prev/curr to avoid creating a new dp array
        // each time we update dp[j-1] with prev, and curr becomes prev
        for(int i = 1; i <= word1.length(); i++) {
            int prev = i;
            for(int j = 1; j <= word2.length(); j++) {
                int curr;
                if(word1.charAt(i-1) == word2.charAt(j-1)) //match
                    curr = dp[j-1];
                else // no match is 1 + prev best
                    curr = 1 + Math.min(dp[j], prev);
                dp[j-1] = prev; // dp[j-1] will not be used more, so it's safe to override
                prev = curr;
            }
            dp[word2.length()] = prev; // update last result
        }
        
        return dp[word2.length()];
    }
}
```