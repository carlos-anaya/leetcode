# 953. Verifying an Alien Dictionary (Easy)

In an alien language, surprisingly they also use english lowercase letters, but possibly in a
different `order`. The order of the alphabet is some *permutation of lowercase letters*.

Given a sequence of `words` written in the alien language, and the order of the alphabet, return 
true if and only if the given words are *sorted lexicographicaly* in this alien language.

- Example 1:
> **Input**: words = ["hello","leetcode"], order = "hlabcdefgijkmnopqrstuvwxyz" <br>
> **Output**: true <br>
> **Explanation**: As 'h' comes before 'l' in this language, then the sequence is sorted.
- Example 2:
> **Input**: words = ["word","world","row"], order = "worldabcefghijkmnpqstuvxyz" <br>
> **Output**: false <br>
> **Explanation**: As 'd' comes after 'l' in this language, then words[0] > words[1], hence the
> sequence is unsorted.
- Example 3:
> **Input**: words = words = ["apple","app"], order = "abcdefghijklmnopqrstuvwxyz" <br>
> **Output**: false <br>
> **Explanation**: "app" is a prefix of "apple", so "app" should come first. Then, the sequence is
> unsorted.

## Optimal
Let's first consider a regular english language. We know that "app" should come before "apple" since
it is a preffix. Same way, "cam" should come befor "car" (they share the prefix "ca" and then the
mistmatch "m" and "r" follow the latin order: a, b, c, ...).

For this alien language we do the same. We need to compare contiguous words and then the chars of
those words. When we find a mismatch we check if the chars follow the alphabet order. Addidionally,
we add a validation to check if there is a prefix, then the prefix should come before the other
word.

**Time: O(C)** (C -> number of total chars (in all words)) <br> 
**Space: O(1)** (array for order is constant)

### Go solution
```go
func isAlienSorted(words []string, order string) bool {
    alphabet := make([]int, 26)
    // store the order of the chars in an array
    for i, c := range order {
        alphabet[c-'a'] = i
    }
    
    // evaluate pair of words
    for i := 1; i < len(words); i++ {
        w1, w2 := words[i-1], words[i]
        isPrefix := true
        // compare char by char of each word
        for j := 0; j < len(w1) && j < len(w2); j++ {
            if w1[j] == w2[j] {  // same char so skip it
                continue
            }
            if alphabet[w1[j]-'a'] > alphabet[w2[j]-'a'] {
                return false
            }
            isPrefix = false // there was a mismatch, so no prefix
            break // if this is in order, we move to the next word
        }
        // if w2 is a prefix of w1, the order of words is incorrect
        // the prefix should be before
        if isPrefix && len(w1) > len(w2) {
            return false
        }
    }
    return true
}
```
### Java solution
```java
class Solution {
    public boolean isAlienSorted(String[] words, String order) {
        byte[] alphabet = new byte[26]; // use byte since alphabet is small
        // fill the order of the alien alphabet
        for(byte i = 0; i < 26; i++)
            alphabet[order.charAt(i)-'a'] = i;
        
        for(int i = 1; i < words.length; i++) {
            String w1 = words[i-1], w2 = words[i];
            boolean isPrefix = true;
            for(int j = 0; j < w1.length() && j < w2.length(); j++) {
                // skip same chars
                if(w1.charAt(j) == w2.charAt(j))
                    continue;
                
                // charAt(j) of word1 is in higher order than charAt(j) of word2
                if(alphabet[w1.charAt(j)-'a'] > alphabet[w2.charAt(j) - 'a'])
                    return false;
                
                // mismatch means a word is not a prefix of the other
                isPrefix = false;
                // the order of w1 and w2 is ok, so skip to the next word
                break;
            }
            // if w2 is a prefix of w1, the order is incorrect
            if(isPrefix && w1.length() > w2.length()) {
                return false;
            }
        }
        return true;
    }
}
```
