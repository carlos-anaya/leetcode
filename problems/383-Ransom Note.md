# 383. Ransom Note

Given an arbitrary ransom note string and another string containing letters from all the magazines,
write a function that will return true if the ransom note can be constructed from the magazines;
otherwise, it will return false.

Each letter in the magazine string can only be used once in your ransom note.

**Note:** You may assume that both strings contain only lowercase letters.

- Example:
> canConstruct("a", "b") -> false <br>
> canConstruct("aa", "ab") -> false <br>
> canConstruct("aa", "aab") -> true <br>

## Optimal
Since we're guaranteed that the magazine contains only lowercase letters, we can use an array of
counts per character in the magazine. Then we can loop over the ransomNote characters and check if
we have enough chars. Return false when a character is not available. Return true if all the chars
of the ransom note were validated.

**Time: O(n+m) <br> Space: O(m)** (m -> chars in Magazine, n -> chars in ransom Note)

### Go solution
```go
func canConstruct(ransomNote string, magazine string) bool {
    var mag [26]int
    for _, m := range magazine {
        mag[m-'a']++
    }
    
    for _, n := range ransomNote {
        if mag[n-'a'] <= 0 {
            return false
        }
        mag[n-'a']--
    }
    
    return true
}
```
### Java solution
```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] mag = new int[26];
        for(char m : magazine.toCharArray())
            mag[m-'a']++;
        
        for(char r : ransomNote.toCharArray()){
            if(mag[r-'a'] <= 0)
                return false;
            mag[r-'a']--;
        }
        
        return true;
    }
}
```