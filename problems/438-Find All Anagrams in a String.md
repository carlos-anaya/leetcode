# 438. Find All Anagrams in a String (Medium)

Given a string `s` and a *non-empty* string `p`, find all the start indices of p's *anagrams* in s.

**Note**: Strings consists of *lowercase English letters* only and the length of both strings 
`s` and `p` will not be larger than 20,100. Additionally, the order of output does not matter.

- Example 1:
> **Input**: s: "cbaebabacd" p: "abc" <br>
> **Output**: [0, 6] <br>
> **Explanation**: The substring with start index = 0 is "cba", which is an anagram of "abc". <br>
> The substring with start index = 6 is "bac", which is an anagram of "abc".
- Example 2:
> **Input**: s: "abab" p: "ab" <br>
> **Output**: [0, 1, 2]

## Brute Force
Use a sliding window and check if the chars in the window are an anagram of the string `p`. 
How to know if two chars are anagrams? We could sort both strings and then compare them.

**Time: O(s\*p\*logp) <br> Space: O(1)**

## Sub-Optimal
Instead of sorting p and sort everytime the chars of the window we can have a **char frequency map**
or an array.

In this case, since we're just dealing with lowercase english characters we need an array of 
*size 26*. First we count the frequencies of string `p`. Then we loop over the string `s` and keep
an array of frequencies too. Once we have a window of size *len(p)* we check if both arrays are 
equivalent. Then we move the sliding window, adding a new char and removing the start of the window.

**Time: O(n+p) <br> Space: O(1)**

```go
func findAnagrams(s string, p string) []int {
    n, m := len(s), len(p)
    schars, pchars := make([]int, 26), make([]int, 26)
    
    // fill the array of char freqs of p
    for _, c := range p { pchars[c-'a']++ }
    
    ans := make([]int, 0, len(s))
    for i, c := range s {
        // skip until we have enough chars to compare
        if i < m {
            schars[c-'a']++
            continue
        }
        // check if both arrs have the same chars
        if sameChars(schars, pchars) {
            ans = append(ans, i-m)
        }
        // remove first char of the window and add the new one
        schars[s[i-m]-'a']--
        schars[c-'a']++
    }
    // check if the last substring is anagram
    if sameChars(schars, pchars) {
        ans = append(ans, n-m)
    }
    return ans
}

func sameChars(arr1, arr2 []int) bool {
    for i, c := range arr1 {
        if arr2[i] != c {
            return false
        }
    }
    return true
}
```

## Optimal
Instead of comparing both arrays for every move of the sliding window we can keep a count of the 
*chars needed to form an anagram*. I.e.
1. Let s: "decbaebac" p: "abc", then the freq array for `p` is as follows and *count=3*, i.e. 
  we need 3 more chars to match an anagram.

a-0 | b-1 | c-2 | d-3 | e-4| ... |z-25
--- | --- | --- | --- | --- | --- | ---
1 | 1 | 1 | 0 | 0 | ... | 0

2. We start the window of size 3 and decrement every occurence of `s` chars from the array, i.e. the
  window is "dec", then we have  *freqs['d']--*,  *freqs['e']--*,  *freqs['c']--*

a-0 | b-1 | c-2 | d-3 | e-4| ... |z-25
--- | --- | --- | --- | --- | --- | ---
1 | 1 | 0 | -1 | -1 | ... | 0

3. In the previous step we decremented a positive value (**c**), which means that char is in `p`, so
  we need to *count--*. If at any time *count==0*, then we have a match.

4. Next window move step we remove "d" and add "b", thus we have *freqs['d']++* and *freqs['b']--*.
  Since *freqs['b'] >= 0* we need to *count--* ("b" is in `p`).

a-0 | b-1 | c-2 | d-3 | e-4| ... |z-25
--- | --- | --- | --- | --- | --- | ---
1 | 0 | 0 | 0 | -1 | ... | 0

5. Next window move step we remove "e" and add "a", thus we have *freqs['e']++* and *freqs['a']--*.
  Since *freqs['a'] >= 0* we need to *count--* ("a" is in `p`). This time *count == 0*, so we have a
  match.

a-0 | b-1 | c-2 | d-3 | e-4| ... |z-25
--- | --- | --- | --- | --- | --- | ---
0 | 0 | 0 | 0 | 0 | ... | 0

6. Next window move step we remove "c" and add "e", thus we have *freqs['c']++* and *freqs['e']--*.
  Since now *freqs['c'] > 0* we need to *count++* ("c" is in `p` but we just removed it from the
  window). Then, *count == 1*.

a-0 | b-1 | c-2 | d-3 | e-4| ... |z-25
--- | --- | --- | --- | --- | --- | ---
0 | 0 | 1 | 0 | -1 | ... | 0

**Time: O(n+p) <br> Space: O(1)**

### Go solution
```go
func findAnagrams(s string, p string) []int {
    ans := make([]int, 0, len(s))
    lenS, lenP := len(s), len(p)
    // corner case, empty strings or p larger than s
    if lenS == 0 || lenP == 0 || lenP > lenS {
        return ans
    }
    
    pchars := make([]int, 26)
    // l, r for window; count is chars remaining to make anagram
    l, r, count := 0, 0, lenP
    
    // fill the array of char counts of p
    // all the p chars will have positive counts, others 0
    for _, c := range p {
        pchars[c-'a']++
    }
    
    // process the first window
    for ; r < lenP; r++ {
        curr := s[r]-'a'
        // curr is part of p, so we decrement count
        if pchars[curr] > 0 {
            count--
        }
        pchars[curr]--
    }
    // the first chars in s are an anagram
    if count == 0 { ans = append(ans, 0)}
    
    // slide window
    for r < lenS; r++ {
        // take the first char of the window for removing it
        curr := s[l]-'a'
        // if non-negative it means it was part of anagram, so increase count
        if pchars[curr] >= 0 {
            count++
        }
        pchars[curr]++
        l++
        
        // take new char and add it to the window
        curr = s[r]-'a'
        // new char is part of p, so we decrement count
        if pchars[curr] > 0 {
            count--
        }
        pchars[curr]--
        
        // the first chars in s are an anagram
        if count == 0 { ans = append(ans, l) }
    }
    
    return ans
}
```
### Java solution
```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        int sLen = s.length(), pLen = p.length();
        List<Integer> ans = new ArrayList();
        // validate input
        if(sLen == 0 || pLen == 0 || pLen > sLen)
            return ans;
        
        int l = 0, r = 0, diff = pLen;
        int[] pChars = new int[26];
        
        // get the "p" string char frequencies
        for(int i = 0; i < pLen; i++) {
            char c = p.charAt(i);
            pChars[c-'a']++;
        }
            
        // slide the window
        while(r < sLen) {
            // remove left chars from the window when it has enough chars
            if (r >= pLen) {
                // first remove the first char of the window
                char c = s.charAt(l);
                if(pChars[c-'a'] >= 0) {
                    diff++;
                }
                pChars[c-'a']++;
                l++;
            }
            
            // add the curr char to the window
            char c = s.charAt(r);
            if(pChars[c-'a'] > 0) {
                diff--;
            }
            pChars[c-'a']--;
            r++;
         
            // found a match in the current window
            if(diff == 0) { ans.add(l); }
        }
        
        return ans;
    }
}
```