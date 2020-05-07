# 387. First Unique Character in a String

Given a string, find the *first non-repeating character* in it and return it's index. If it doesn't
exist, return -1.

- Example 1:
> **Input**: "leetcode" <br>
> **Output**: 0 <br>
> **Explanation**: l is the first non-repeating char
- Example 2:
> **Input**: "loveleetcode" <br>
> **Output**: 2 <br>
> **Explanation**: v is the first non-repeating char

## Brute Force
Nested loop: for each *char* check if it's repeated in the remaining string.

**Time: O(n²) <br> Space: O(1)**

## Approach I - two scans of string
Use a map with a pair `<char, count>` or a plain array of 26 int elements, each one representing the
count of a character. First loop over the string and create the map/array. Then loop again over
each char and check if *count == 1*, returning the char index. Return -1 if no unique char exists.

**Time: O(n) <br> Space: O(1)** (the array/map has fixed size, so it's constant)

```go
func firstUniqChar(s string) int {
    charCount := [26]int{}
    for _, c := range s {
        charCount[c-'a']++ 
    }
    
    for i, c := range s {
        if charCount[c-'a'] == 1 {
            return i
        }
    }
    
    return -1
}
```

## Approach II - one scan of string
Instead of scanning the string for a second time, we can do it just once. Besides the *char count*,
we keep track of the *char idx*. Then, we can just iterate through the *char count array*, keeping
track of the *min index* of *chars with count == 1*.

This could be useful if the sting is large, but in terms of *Big O* the limits are the same.

**Time: O(n) <br> Space: O(1)** (the array/map has fixed size, so it's constant)

### Go solution
```go
func firstUniqChar(s string) int {
    charCount := [26]int{}
    charIdx := [26]int{}
    for i, c := range s {
        charCount[c-'a']++
        charIdx[c-'a'] = i
    }
    
    minUniqueIdx := math.MaxInt32
    for i, count := range charCount {
        if count == 1 && charIdx[i] < minUniqueIdx {
            minUniqueIdx = charIdx[i]
        }
    }
    
    if minUniqueIdx != math.MaxInt32 {
        return minUniqueIdx
    }
    return -1
}
```
### Java solution
```java
class Solution {
    public int firstUniqChar(String s) {
        int[] charCount = new int[26];
        int[] charIdx = new int[26];
        
        for(int i = 0; i < s.length(); i++) {
            int c = s.charAt(i)-'a';
            charCount[c]++;
            charIdx[c] = i;
        }
        
        int idx = Integer.MAX_VALUE;
        for(int i = 0; i < s.length(); i++) {
            int c = s.charAt(i)-'a';
            if(charCount[c] == 1)
                idx = Math.min(idx, charIdx[c]);
        }
        return idx == Integer.MAX_VALUE ? -1 : idx;
    }
}
```