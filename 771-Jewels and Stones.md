# 771. Jewels and Stones

You're given strings `J` representing the types of stones that are jewels, and `S` representing the
stones you have.  Each character in `S` is a type of stone you have.  You want to know how many of
the stones you have are also jewels.

The letters in `J` are guaranteed distinct, and all characters in `J` and `S` are letters. Letters
are case sensitive, so *"a"* is considered a different type of stone from *"A"*.

- Example 1:
> **Input**: J = "aA", S = "aAAbbbb" <br>
> **Output**: 3
- Example 2:
> **Input**: J = "z", S = "ZZ"
> **Output**: 0

## Brute Force
Nested loop: for each stone *'s'*, check the list of jewels *'j'*.

**Time: O(J*S) <br> Space: O(1)**

## Approach I - Hashing
Use a `hash set/map` to store the jewels. Loop over the stones anc check if they exist in the 
*set/map*.

```go
func numJewelsInStones(J string, S string) int {
    set := make(map[rune]bool)
    for _, j := range J {
        set[j] = true
    }
    count := 0
    for _, s := range S {
        if set[s] {
            count++
        }
    }
    return count
}
```

**Time: O(J+S) <br> Space: O(J)** (since the number of chars is finite, we can consider it constant)

## Approach II - Bool Array
Since the number of chars is Finite (26 uppercase, 26 lowercase), we can use a *boolean array* to
store what jewels we have, then we just need to loop over the stones and check if the stone is
marked as jewel.

### Go solution
```go
func numJewelsInStones(J string, S string) int {
    jewels := make([]bool,52)
    for _, j := range J {
        if j >= 'a' { // jewel is lowercase
            jewels[j-'a'+26] = true
        } else { // jewel is uppercase
            jewels[j-'A'] = true
        }
    }
    count := 0
    for _, s := range S {
        if s >= 'a' { // stone is lowercase
            if jewels[s-'a'+26] {
                count++
            }
        } else { // stone is uppercase
            if jewels[s-'A'] {
                count++
            }
        }
    }
    return count
}
```
### Java solution
```java
class Solution {
    public int numJewelsInStones(String J, String S) {
        Set<Character> jewels = new HashSet();
        for(int i = 0; i < J.length(); i++) {
            jewels.add(J.charAt(i));
        }
        
        int count = 0;
        for(int i = 0; i < S.length(); i++) {
            if(jewels.contains(S.charAt(i))) {
                count++;
            }
        }
        return count;
    }
}
```