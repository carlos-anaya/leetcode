# 249. Group Shifted Strings (Medium)

Given a string, we can "shift" each of its letter to its successive letter, for example:
`"abc" -> "bcd"`. We can keep "shifting" which forms the sequence:

> "abc" -> "bcd" -> ... -> "xyz"

Given a *list of strings* which contains only lowercase alphabets, group all strings that belong to
the same shifting sequence.

- Example 1:
> **Input**: ["abc", "bcd", "acef", "xyz", "az", "ba", "a", "z"] <br>
> **Output**: [ <br>
> ["abc","bcd","xyz"], <br>
> ["az","ba"], <br>
> ["acef"], <br>
> ["a","z"] <br>
> ]

## Optimal
What can we see from the words that are shifted? For example, take *"abc","lmn","xyz"*, what are
their numerical values? *{1,2,3}, {11,12,13}, {23,24,25}*... what do we note? 
**s1[1] - s1[0] == s2[1] - s2[0] == s3[1] - s3[0]** (2-1 == 12-11 == 25-24).

We can take advantage of that and generate a `hash` value of the strings, so, no matter if they're
shifted, they'll produce the same hash!. Let's build the hash as
**'#' + s[1] - s[0] + ... + '#' + s[n-1] - s[n-2]** and use a map to keep track of words with the
same hash.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func groupStrings(strings []string) [][]string {
    n := len(strings)
    ans := make([][]string, 0, n)
    maps := make(map[string][]string)
    // group the strings in a map given the hash value
    for _, str := range strings {
        hashed := hash(str)
        if _, found := maps[hashed]; found {
             maps[hashed] = append( maps[hashed], str)
        } else {
            list := make([]string, 0, n)
            list = append(list, str)
            maps[hashed] = list
        }
    }
    // create output array
    for _, v := range maps {
        ans = append(ans, v)
    }
    return ans
}

// hash the strigs based on 
// (str[1]-str[0]) + '#' + ... + '#' + (str[n-1]-str[n-2])
// e.g. 'ade', 'bef', 'jmn' -> '#3#1'
func hash(str string) string {
    if len(str) == 1 {
        return "#"
    }
    var sb strings.Builder
    for i := 1; i < len(str); i++ {
        diff := str[i] - str[i-1]
        diff = (diff + 26) % 26 // to avoid negatives
        sb.WriteRune('#')
        sb.WriteByte(diff)
    }
    return sb.String()
}
```
### Java solution
```java
class Solution {
    public List<List<String>> groupStrings(String[] strings) {
        Map<String,List<String>> map = new HashMap();
        for(int i = 0; i < strings.length; i++) {
            String word = strings[i];
            String hashed = hash(word.toCharArray());
            List<String> list = map.getOrDefault(hashed, new ArrayList());
            list.add(word);
            map.put(hashed, list);
        }
        List<List<String>> ans = new ArrayList();
        for(List<String> list : map.values())
            ans.add(list);
        return ans;
    }
    
    private String hash(char[] str) {
        if(str.length == 0)
            return "#";
        StringBuilder sb = new StringBuilder();
        for(int i = 1; i < str.length; i++) {
            int diff = (str[i] - str[i-1] + 26) % 26;
            sb.append('#');
            sb.append(diff + 'a');
        }
        
        return sb.toString();
    }
}
```