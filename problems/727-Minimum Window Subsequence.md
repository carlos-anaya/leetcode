# 727. Minimum Window Subsequence (Hard)

Given strings `S` and `T`, find the **minimum** (contiguous) substring `W` of `S`, so that `T` is a
*subsequence* of `W`.

If there is no such window in `S` that covers all characters in T, return the empty string "". If
there are multiple such minimum-length windows, return the one with the left-most starting index.

- Example 1:
> **Input**: S = "abcdebdde", T = "bde" <br>
> **Output**: "bcde" <br>
> **Explanation**: "bcde" is the answer because it occurs before "bdde" which has the same length.
> "deb" is not a smaller window because the elements of T in the window must occur in order.

## Optimal
This problem is similar to [76-Minimum Window Substring](76-Minimum%20Window%20Substring.md), but in
this case we need a *subsequence*, not a *substring*, then the characters need to be in order.

We first create a *window* in `S` that contains all the chars in `T`, let's say from *0...r*. Then,
instead of *shrinking* the window, we create a new window starting from *r*, backwards (to the left)
that contains all the chars in `T`, let's say *l..r*, this way we ensure it's the minimum sequence
ending in *r*. Finally, we set *r = l + 1*, otherwise the chars between *l..r* will be ignored for
new potential windows.

For matching chars in `S` and `T` we use a *two pointer approach*, so we ensure the chars are in
order.

idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 0
--- | --- | --- | --- | --- | --- | --- | --- | --- | ---
string | a | b | b | c | d | e | b | d | e
first window | . | . | l | . | . | r | . | . | .
second window | . | . | . | . | . | . | l | . | r

**Time: O(S+T) <br> Space: O(1)**

### Java solution
```java
class Solution {
    public String minWindow(String S, String T) {
        int lenS = S.length(), lenT = T.length();
        if(lenS == 0 || lenT == 0)
            return "";
        
        int r = 0, tIdx = 0;
        int minIdx = 0, minSize = Integer.MAX_VALUE;
        // move the window 'r' until it has all the chars from T
        // then create another window from 'r' back to 'l' to find the min
        // window that has all chars from T
        while(r < lenS) {
            if(S.charAt(r) == T.charAt(tIdx))
                tIdx++;
            r++;
            // don't have all T chars in the window yet
            if(tIdx < lenT)
                continue;
            
            // find the smallest window that contains all chars of T
            // l moves backwards
            int l = r - 1;
            while(tIdx > 0) {
                if(S.charAt(l) == T.charAt(tIdx - 1))
                    tIdx--;
                if(tIdx == 0)
                    break;
                l--;
            }
            // new min found
            if(r - l < minSize) {
                minSize = r - l;
                minIdx = l;
            }
            // move 'r' back to after 'l' so we can find the chars again
            // tIdx is already 0, so no need to update it
            r = l + 1;
        }
        
        if(minSize != Integer.MAX_VALUE)
            return S.substring(minIdx, minIdx + minSize);
        
        return "";
    }
}
```
