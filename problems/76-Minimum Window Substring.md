# 76. Minimum Window Substring (Hard)

Given a string `S` and a string `T`, find the *minimum substring* in `S` which will contain **all**
the characters in `T`.

**Note**:

- If there is no such window in `S` that covers all characters in `T`, return an empty string **""**
- If there is such window, there will always be only one unique minimum window in `S`.

- Example:
> **Input**: S = "ADOBECODEBANC", T = "ABC" <br>
> **Output**: "BANC"

## Optimal
Use a `sliding window` approach. We first count all the chars in `T`. Then we increase the window to
the right until we have all the chars within the window. We have an answer of size *r-l+1*. Then, we
*shrink* the window until we "owe" a char again. Then, we increase the window again...

**Time: O(S+T) <br> Space: O(S+T)**

### Go solution
```go
func minWindow(s string, t string) string {
    tmap := make(map[rune]int)
    // fill frequencies map
    for _, c := range t {
        if _, found := tmap[c]; !found {
            tmap[c] = 0
        }
        tmap[c]++
    }
    
    lenT, diff := len(t), len(t)
    ans := ""
    
    l := 0
    for r, c := range s {
        // char is not in T, so we skip it
        if _, found := tmap[c]; !found {
            continue
        }
        
        // decrease the frequency of the char
        tmap[c]--
        // if not negative it reduces the diff
        // negative means the c char is repeated so we "owe" other chars
        if tmap[c] >= 0 {
            diff--
        }
        
        // found a window with all the needed chars
        for diff == 0 {
            // short-circuit if curr window lenght == lenght of t
            if r - l + 1 == lenT {
                return s[l:r+1]
            } else if len(ans) == 0 || r - l + 1 < len(ans) { // new min substring
                ans = s[l:r+1]
            }
            
            // shrink the window
            prev := rune(s[l])
            l++
            // if the removed char is in the map increace the frequency
            if _, found := tmap[prev]; found {
                tmap[prev]++
                // if frequency > 0 we "owe" chars, so increase diff
                // if freq <= 0 the char was repeated so it doesn't increase diff
                if tmap[prev] > 0 {
                    diff++
                }
            }
        }
    }
    
    return ans
}
```
### Java solution
```java
class Solution {
    public String minWindow(String s, String t) {
        // use int array as it is more performant than a map
        int[] charsT = new int[128];
        int lenT = t.length(), diff = t.length();
        int resIdx = 0, resLen = 0;
        
        // fill frequencies
        for(int i = 0; i < lenT; i++)
            charsT[t.charAt(i)]++;
            
        for(int l = 0, r = 0; r < s.length(); r++) {
            char c = s.charAt(r);
            // decrease diff if curr char is still needed in "t"
            if(charsT[c] > 0)
                diff--;
            charsT[c]--;
            
            while(diff == 0) {
                // update answer if window is smaller
                if((r - l + 1) < resLen || resLen == 0) {
                    resIdx = l;
                    resLen = r - l + 1;
                }
                // short circuit when window has same length as "t"
                if(resLen == lenT)
                    return s.substring(l, r + 1);
                
                // shrink the window
                char prev = s.charAt(l);
                l++;
                
                // return prev to its previous count
                charsT[prev]++;
                // if "prev" is positive we "owe" chars, so diff increases
                if(charsT[prev] > 0)
                    diff++;
            }
        }
        return resLen == 0 ? "" : s.substring(resIdx, resIdx+resLen);
    }
}
```