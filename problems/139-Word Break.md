# 139. Word Break (Medium)

Given a *non-empty string* `s` and a *dictionary* `wordDict` containing a list of non-empty words,
determine if `s` can be segmented into a space-separated sequence of one or more dictionary words.

**Note**:
- The same word in the dictionary may be reused multiple times in the segmentation.
- You may assume the dictionary does not contain duplicate words.

- Example 1:
> **Input**: s = "leetcode", wordDict = ["leet", "code"] <br>
> **Output**: true <br>
> **Explanation**: Return true because "leetcode" can be segmented as "leet code".
- Example 2:
> **Input**: s = "applepenapple", wordDict = ["apple", "pen"] <br>
> **Output**: true <br>
> **Explanation**: Return true because "applepenapple" can be segmented as "apple pen apple".
- Example 3:
> **Input**: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"] <br>
> **Output**: false

## Brute Force - Memorization
We use a `backtracking` function. We use an index to mark the *current char* and the *start char* of
the word to be processed and then at each level we check if the substring from *start* to *curr* is
in the dictionary. If so, we backtrack starting from the next char.

We use a **memo** array to keep track of already considered words and the result they yielded.

**Time: O(n²)** <br> **Space: O(n²)**

```java
class Solution {
    int[][] memo;
    Set<String> dict;
    
    public boolean wordBreak(String s, List<String> wordDict) {
        dict = new HashSet(wordDict);
        memo = new int[s.length()+1][s.length()+1];
        
        return backtrack(s, 0, 0);
    }
    
    private boolean backtrack(String s, int start, int end) {
        if(end == s.length()) {
            return dict.contains(s.substring(start, end));
        }
        end++;
        
        if(memo[start][end] != 0)
            return memo[start][end] == 2;
        
        memo[start][end] = 1;
        String word = s.substring(start, end);
        
        if(dict.contains(word)) {
            if(backtrack(s, end, end)) {
                memo[start][end] = 2;
                return true;
            }
        }
        if(backtrack(s, start, end)) {
            memo[start][end] = 2;
            return true;
        }
        return false;
    }
}
```
```go
var memo [][]int
var dict map[string]bool

func wordBreak(s string, wordDict []string) bool {
    dict = make(map[string]bool)
    for _, word := range wordDict {
        dict[word] = true
    }
    memo = make([][]int, len(s)+1)
    
    for i := 0; i < len(s); i++ {
        memo[i] = make([]int, len(s)+1)
    }
	
	return backtrack(s, 0, 0)
}

func backtrack(str string, start, curr int) bool {
	if curr == len(str) {
        if dict[str[start:]] {
            return true
        }
        return false
    }
    curr++
    word := str[start:curr]
    
    if memo[start][curr] == 1 {
        return false
    } else if memo[start][curr] == 2 {
        return true
    }
    memo[start][curr] = 1
    
    
    if _, found := dict[word]; found {
        if backtrack(str, curr, curr) {
            memo[start][curr] = 2
            return true
        }
	}
	if backtrack(str, start, curr) {
        memo[start][curr] = 2
        return true
    }
	return false
}

```
## Optimal - DP
This problem has `optimal substructure` property, so we can apply a `dynamic programming` approach.
We can generate substrings of the word in a nested loop and keep track if they yield a positive
outcome. 

We have *dp[i] = true* if the *str.substring(j, i)* is in the dictionary and if the *dp[j-1]* is
true. But **why?** *i* marks the end of the current word, and *j* marks the beginning of the word,
so, even if this substring exists in the *dict*, we need to check if the previous word (or words)
yielded are valid. 

Take *str=leecode* as an example, and *dict={leet, code}*. When *j=3, i=6* the substring *code* is
valid and is in the dictionary, but *lee* is not valid.

**Time: O(n²) <br> Space: O(n)**

### Go solution
```go
func wordBreak(s string, wordDict []string) bool {
    dict := make(map[string]bool)
    for _, word := range wordDict {
        dict[word] = true
    }
    lenS := len(s)
    dp := make([]bool, lenS)
    for i, _ := range s {
        for j := 0; j <=  i; j++ {
            word := s[j:i+1]
            if _, found := dict[word]; found && (j == 0 || dp[j-1]) {
                dp[i] = true
                break
            }
        }
    }
    return dp[lenS-1]
}
```
