# 72. Edit Distance (Hard)

Given two words `word1` and `word2`, find the *minimum number of operations* required to convert
`word1` to `word2`.

You have the following *3 operations* permitted on a word:

- Insert a character
- Delete a character
- Replace a character

- Example 1:
> **Input**: word1 = "horse", word2 = "ros" <br>
> **Output**: 3 <br>
> **Explanation**: <br>
> horse -> rorse (replace 'h' with 'r')  <br>
> rorse -> rose (remove 'r')  <br>
> rose -> ros (remove 'e')
- Example 2:
> **Input**: word1 = "intention", word2 = "execution" <br>
> **Output**: 5 <br>
> **Explanation**: <br>
> intention -> inention (remove 't') <br>
> inention -> enention (replace 'i' with 'e') <br>
> enention -> exention (replace 'n' with 'x') <br>
> exention -> exection (replace 'n' with 'c') <br>
> exection -> execution (insert 'u')

## Brute Force - Recursion
Simple recursion, if there's a match skip the char in both words and recurse again. If no match,
consider the three scenarios and return 1 + min(insert, delete, replace). Base case is when we reach
the end of a string, so we return the number of remaining chars in the other string.

**Time: O(3^n)** (n = min(w1.length, w2.length)) <br> 
**Space: O(n)**

```go
func minDistance(word1 string, word2 string) int {
    return rec(word1, word2, 0, 0)
}

func rec(word1, word2 string, w1, w2 int) int {
    // end of word1, return count of remaining chars of word2
    if w1 >= len(word1) { return len(word2) - w2 }
    // end of word2, return count of remaining chars of word1
    if w2 >= len(word2) { return len(word1) - w1 }
    
    // match of curr chars, no cost to edit this char so skip it
    if word1[w1] == word2[w2] {
        return rec(word1, word2, w1 + 1, w2 + 1)
    }
    
    // char didn't match so consider three scenarios, insert, delete or replace
    // this is min of skipping a char in either word or in both + 1 (because of mismatch)
    dist1 := rec(word1, word2, w1 + 1, w2)
    dist2 := rec(word1, word2, w1, w2 + 1)
    dist3 := rec(word1, word2, w1 + 1, w2 + 1)
    
    return 1 + min(min(dist1, dist2), dist3)
}
```

## Optimal - DP
This problem has the `optimal substructure property` so we can use a **bottom-up dp** approach:

- When there's a match, the edit distance is the same as the edit distance before adding the curr
  char, this is **dp[i][j] = dp[i-1][j-1]**.
- If there's no match, the edit distance is **1** plus the best we could do with an *insertion*,
  *deletion* or *replacement*, this is **dp[i][j] = 1 + min(dp[i-1][j-1], dp[i][j-1], dp[i-1][j])**.
- We use a *dp* array/matrix of size *min(W1, W2)* for considering the base cases of empty strings.
  This is, we initialize the first row and col comparing the word vs an empty string

See the table below:

. | "" | h | o | r | s | e
--- | --- | --- | --- | --- | --- | ---
"" | **0** | 1 | 2 | 3 | 4 | 5
r | 1 | 1 | 2 | **2** | 3 | 4
o | 2 | 2 | **1** | 2 | 3 | 4
s | 3 | 3 | 2 | 2 | **2** | 3
e | 4 | 4 | 3 | 3 | 3 | **2**

**Time: O(W1 \* W2)** (W1 = w1.length, W2 = w2.length) <br> **Space: O(min(W1, W2))**

### Go solution
```go
func minDistance(word1 string, word2 string) int {
    len1, len2 := len(word1), len(word2)
    prev := make([]int, len2 + 1)
    
    // initialize first row. word2 vs an "empty" string
    for j := 1; j <= len2; j++ {
        prev[j] = j
    }
    
    for i := 1; i <= len1; i++ {
        curr := make([]int, len2 + 1)
        curr[0] = i // initialize first col. word1 vs an "empty" string
        for j := 1; j <= len2; j++ {
            if word1[i-1] == word2[j-1] {
                curr[j] = prev[j-1]
            } else {
                curr[j] = 1 + min(min(prev[j], prev[j-1]), curr[j-1])
            }
        }
        prev = curr
    }
    return prev[len2]
}
```
### Java solution
```java
class Solution {
    public int minDistance(String word1, String word2) {
        int len1 = word1.length(), len2 = word2.length();
        int[] prev = new int[len2+1];
        // initialize first row
        for(int j = 1; j <= len2; j++)
            prev[j] = j;
        
        for(int i = 1; i <= len1; i++) {
            int[] curr = new int[len2+1];
            curr[0] = i; // initialize first col
            for(int j = 1; j <= len2; j++) {
                if(word1.charAt(i-1) == word2.charAt(j-1)) // match
                    curr[j] = prev[j-1];
                else // mismatch
                    curr[j] = 1 + Math.min(Math.min(prev[j], prev[j-1]), curr[j-1]);
            }
            prev = curr;
        }
        return prev[len2];
    }
}
```