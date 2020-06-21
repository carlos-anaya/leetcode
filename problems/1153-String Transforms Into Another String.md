# 1153. String Transforms Into Another String (Hard)

Given two strings `str1` and `str2` of the same length, determine whether you can transform `str1`
into `str2` by doing **zero or more** *conversions*.

In one conversion you can convert *all* occurrences of one character in `str1` to **any** other
lowercase English character.

Return `true` if and only if you can transform `str1` into `str2`.

- Example 1:
> **Input**: str1 = "aabcc", str2 = "ccdee" <br>
> **Output**: true <br>
> **Explanation**: Convert 'c' to 'e' then 'b' to 'd' then 'a' to 'c'. Note that the order of
> conversions matter.
- Example 2:
> **Input**: str1 = "leetcode", str2 = "codeleet" <br>
> **Output**: false <br>
> **Explanation**: There is no way to transform str1 to str2.

## Optimal
We can think of this problem as a `graph` problem. Let's consider the strings for example 1: 
*str1 = "aabcc", str2 = "ccdee"*, then we have these mappings:

  a -> c <br>
  b -> d <br>
  c -> e

Then, the transformation is valid. Now, let's change the last 'c' in str1 and check the mappings
*str1 = "aabca", str2 = "ccdee"*

  a -> c <br>
  b -> d <br>
  c -> e <br>
  a -> e (conflict)

In this case we clearly see that there's a conflict since a character in *str1* cannot be
transformed to 2 different chars. Now let's analyse this example: *str1 = "abcca", str2 = "edeee"*,
then we have these mappings:

  a -> e <br>
  b -> d <br>
  c -> e (repeaded but it's fine)

We see that many characters in *str1* letter can map to the same character in *str2*. Let us see
one example more:  *str1 = "abcca", str2 = "cdaac"*

  a -> c <br>
  b -> d <br>
  c -> a (cycle, but it's fine)

We see that 'a' maps to 'c' and then 'c' maps to 'a', which is valid. In this case 'a' could be
transformed to any other *temp* character, then 'c' can be transformed to 'a' and finally, the
*temp* can be transformed to 'c'. In this case, we have to have at least one character not mapped in
*str2* so it can be used as *temp*!!!

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func canConvert(str1 string, str2 string) bool {
    if str1 == str2 {
        return true
    }
    if len(str1) != len(str2) {
        return false
    }
    
    transforms := make(map[byte]byte)
    usedChars := make([]bool, 26)
    usedCount := 0
    
    for i := 0; i < len(str1); i++ {
        c1, c2 := str1[i], str2[i]
        if val, found := transforms[c1]; !found {
            transforms[c1] = c2
        } else if val != c2 { // character already has a mapping to a different char
            return false
        }
        // keep track of used chars...
        // this is needed for tracking if we can use a tmp char
        if !usedChars[c2-'a'] {
            usedChars[c2-'a'] = true
            usedCount++
        }
    }
    
    // a character can be used as temp
    if usedCount < 26 {
        return true
    }
    
    return false
}
```
