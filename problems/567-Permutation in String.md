# 567. Permutation in String (Medium)

Given two strings `s1` and `s2`, write a function to return true if s2 contains the **permutation**
of s1. In other words, one of the first string's permutations is the substring of the second string.

**Note**:
1. The input strings only contain lower case letters.
2. The length of both given strings is in range [1, 10,000].

- Example 1:
> **Input**: s1 = "ab" s2 = "eidbaooo" <br>
> **Output**: true <br>
> **Explanation**: s2 contains one permutation of s1 ("ba").
- Example 2:
> **Input**: s1= "ab" s2 = "eidboaoo" <br>
> **Output**: False

## Brute Force
We can use a `sliding window` to sort the chars in the window, then compare the *sorted substring*
with *s1*. If there's no match, we slide the window.

**Time: O(s2\*s1\*log s1) <br> Space: O(1)**

## Sub-Optimal
We can use a `sliding window` and **frequencies arrays** to check if all the *chars* in the window
are a permutation of *s1*. For every window we check if both *arrays* are the same and if not we
slide the window.

**Time: O(s1+s2) <br> Space: O(1)**

## Optimal
We can use a `sliding window` and a **frequencies array** to check if all the *chars* in the window
are a permutation of *s1*. The approach is very similar to
[438-Find All Anagrams in a String](438-Find%20All%20Anagrams%20in%20a%20String.md), but we just
return false/true instead of the list of indexes. We keep a *diff* variable for counting the chars
already matched and when *diff==true* we have a full match.

**Time: O(s1+s2) <br> Space: O(1)**

### Go solution
```go
func checkInclusion(s1 string, s2 string) bool {
    freqs := make([]int, 26)
    lenS1, lenS2 := len(s1), len(s2)
    
    if lenS1 == 0 || lenS2 == 0 || lenS1 > lenS2 {
        return false
    }
    
    // fill S1 frequencies array
    for _, c := range s1 { freqs[c-'a']++ }
    
    diff := lenS1
    for l, r := 0, 0; r < lenS2; r++  {
        // remove from start of window when we have enough chars
        if r >= lenS1 {
            c := s2[l]-'a'
            if freqs[c] >= 0 {
                diff++
            }
            freqs[c]++
            l++
        }
        // add the new char to the window
        c := s2[r]-'a'
        if freqs[c] > 0 {
            diff--
        }
        freqs[c]--
        
        // we have all the s1 chars in the window
        if diff == 0 { return true }
    }
    return false
}
```
### Java solution
```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        int lenS1 = s1.length(), lenS2 = s2.length();
        if(lenS1 == 0 || lenS2 == 0 || lenS1 > lenS2)
            return false;
        
        int[] freqs = new int[26];
        int diff = lenS1;
        // fill S1 freqs map
        for(int i = 0; i < lenS1; i++) 
            freqs[s1.charAt(i) - 'a']++;
        
        for(int l = 0, r = 0; r < lenS2; r++) {
            // remove chars from window start when window has size lenS1
            if(r >= lenS1) {
                int c = s2.charAt(l) - 'a';
                if(freqs[c] >= 0)
                    diff++;
                freqs[c]++;
                l++;
            }
            // add new char to the window
            int c = s2.charAt(r) - 'a';
            if(freqs[c] > 0)
                diff--;
            freqs[c]--;
            
            if(diff == 0)
                return true;
        }
        return false;
    }
}
```