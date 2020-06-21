# 340. Longest Substring with At Most K Distinct Characters (Hard)

Given a string `s`, find the length of the *longest substring T* that contains at most `k` distinct
characters.

- Example 1:
> **Input**: s = "eceba", k = 2 <br>
> **Output**: 3 <br>
> **Explanation**: T is "ece" which its length is 3.
- Example 2:
> **Input**: s = "aa", k = 1 <br>
> **Output**: 2
- Example 3:
> **Input**: s = "a", k = 3 <br>
> **Output**: 1

## Optimal
We can use a `sliding window` and keep on moving to the right, adding chars. We keep a `map` with
the characters and their counts. When we have *k+1* distinct values in the window, we have to 
*shrink the window* until a char is removed (i.e., *map[char[l]] == 0*).

**Time: O(k\*s) <br> Space: O(s)**

### Java solution
```java
class Solution {
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        int n = s.length();
        if(n == 0 || k == 0)
            return 0;
        
        // can use an integer array
        Map<Character, Integer> map = new HashMap();
        int max = Integer.MIN_VALUE, diff = 0;
        
        // sliding window with two pointers
        for(int l = 0, r = 0; r < n; r++) {
            char c = s.charAt(r);
            // new distinct character
            if(!map.containsKey(c) || map.get(c) == 0)
                diff++;
            map.put(c, map.getOrDefault(c, 0) + 1);
            
            // move left pointer until a distinct char has been removed
            // can use map.size() instead of diff, but we'd need to remove a key from the
            // map when count == 0; remove operation is usually expensive.
            while(diff > k) {
                char prev = s.charAt(l);
                map.put(prev, map.get(prev) - 1);
                // distinct character has been "removed" from the window
                if(map.get(prev) == 0)
                    diff--;
                l++;
            }
            max = Math.max(max, r - l + 1);
        }
        
        return max;
    }
}
```
