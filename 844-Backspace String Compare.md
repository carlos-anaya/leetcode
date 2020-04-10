# 844. Backspace String Compare

Given two strings `S` and `T`, return if they are equal when both are typed into empty text editors.
`#` means a backspace character.

- Example 1:
> **Input**: S = "ab#c", T = "ad#c" <br>
> **Output**: True <br>
> **Explanation**: S and T both become "ac"
- Example 2:
> **Input**: S = "ab##", T = "c#d#" <br>
> **Output**: True <br>
> **Explanation**: S and T both become ""
- Example 3:
> **Input**: S = "##a###c", T = "a#c" <br>
> **Output**: True <br>
> **Explanation**:  S and T both become ""
- Example 4:
> **Input**: S = "a#c", T = "b" <br>
> **Output**: False <br>
> **Explanation**: S becomes "c" while T becomes "b"

## Brute Force
Use a **stack** or a **linked list** to store the elements. When a *char* is found push it to the 
stack or linked list, if a *#* is found, pop from stack (move to list.next) as long as there are 
elements. Do the process above for each string and then compare both stacks/lists.

**Time: O(n) <br> Space: O(n)**

## Optimal
We could reduce the Space complexity by using `pointers` starting from the last elements and a 
couple of variables to track how many characters to *skip*. Use a inner loop for each string as 
long as there are *#* or there are chars to skip. Once we get out of those loops, if the
characters are different or if one string is depleted and the other is not then return false.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func backspaceCompare(S string, T string) bool {
    skipS, skipT := 0, 0
    i, j := len(S) - 1, len(T) - 1
    
    // process both strings, one could be done but the other not yet
    for i >= 0 || j >=0 {
        // loop S while there are '#' or we need to skip chars
        for i >= 0 {
            if S[i] == '#' {
                skipS++
                i--
            } else if skipS > 0 {
                skipS--
                i--
            } else { // curr is a character and nothing to skip
                break
            }
        }
        // loop T while there are '#' or we need to skip chars
        for j >= 0 {
            if T[j] == '#' {
                skipT++
                j--
            } else if skipT > 0 {
                skipT--
                j--
            } else { // curr is a character and nothing to skip
                break 
            }
        }
        
        // chars are different
        if i >= 0 && j >= 0 && S[i] != T[j] {
            return false
        }
        // one string is done but the other has characters left
        if (i >= 0 && j < 0) || (i < 0 && j >= 0) { 
            return false
        }
        
        i--
        j--
    }
    
    return true
}
```
### Java solution
```java
class Solution {  
    public boolean backspaceCompare(String S, String T) {
        int i = S.length() - 1, j = T.length() - 1;
        int skipS = 0, skipT = 0;
        
        while(i >= 0 || j >= 0) {
            while(i >= 0) {
                if(S.charAt(i) == '#') {
                    skipS++;
                    i--;
                } else if (skipS > 0) {
                    skipS--;
                    i--;
                } else {
                    break;
                }
            }
            while(j >= 0) {
                if(T.charAt(j) == '#') {
                    skipT++;
                    j--;
                } else if (skipT > 0) {
                    skipT--;
                    j--;
                } else {
                    break;
                }
            }
            if(i >= 0 && j >=0 && S.charAt(i) != T.charAt(j))
                return false;
            if((i >= 0 && j < 0) || (j >= 0 && i < 0))
                return false;
            
            i--;
            j--;
        }
        
        return true;
    }
}
```