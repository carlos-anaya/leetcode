# 49. Group Anagrams

Given an array of strings, group anagrams together (any order is ok).

- Example 1:
> **Input**: ["eat", "tea", "tan", "ate", "nat", "bat"] <br>
> **Output**: [["ate","eat","tea"],["nat","tan"],["bat"]]

## Brute Force
Use a **map** to store an anagram as *key* and a list/array of strings as *value*.
For *key* we can sort the anagram's characters, so any anagram will produce the same ordered string. 

**Time: O(NKlogK) <br> Space: O(NK)**<br>
*N -> Number of words; K -> Maximum length of string in words*

## Optimal
Instead of *sorting* characters (NlogN time) we can implement a custom `hash function`. Any anagram
should produce the same result in N time. 
- We use an **int array of size 26**, so each block represents a character and the value represents
  a count.
- We serialize the array to a string, separating every element with **#** or any other separator, 
  e.g. **abbddd -> [1,2,0,3,0,...,0] -> #1#2#0#3#0....#0**

**Time: O(NK) <br> Space: O(NK)**

### Go solution
```go
func groupAnagrams(strs []string) [][]string {
    strMap := make(map[string][]string)
    for _, str := range strs {
        hash := hash(str)
        if _, found := strMap[hash]; !found {
            strMap[hash] = make([]string, 0)
        } 
        strMap[hash] = append(strMap[hash], str)
    }
    
    ans := make([][]string, len(strMap))
    i := 0
    for _, val := range strMap {
        ans[i] = val
        i++
    }
    
    return ans
}

func hash(str string) string {
  var arr [26]int
  for _, r := range str {
    arr[r - 'a']++
  }
  var sb strings.Builder
  for _, val := range arr {
    sb.WriteRune('#')
    sb.WriteRune(rune(val))
  }
  return sb.String()
}

```
### Java solution
```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap();
        for(String str : strs) {
            String hash = hash(str);
            if(!map.containsKey(hash)) {
                map.put(hash, new ArrayList());
            }
            map.get(hash).add(str);
        }
        
        List<List<String>> ans = new ArrayList();
        for(String key : map.keySet()) {
            ans.add(map.get(key));
        }
        
        return ans;
    }
    
    private String hash(String str) {
        int[] arr = new int[26];
        for(int i = 0; i < str.length(); i++) {
            arr[str.charAt(i)-'a']++;
        }
        StringBuilder sb = new StringBuilder();
        for(int i : arr) {
            sb.append('#').append(i);
        }
        
        return sb.toString();
    }
}
```