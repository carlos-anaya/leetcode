# 678. Valid Parenthesis String

Given a string containing only three types of characters: `'(', ')' and '*'`, write a function to 
check whether this string is valid. A string is valid if it complies these rules:

1. Any left parenthesis `'('` must have a corresponding right parenthesis `')'`.
2. Any right parenthesis `')'` must have a corresponding left parenthesis `'('`.
3. Left parenthesis `'('` must go before the corresponding right parenthesis `')'`.
4. `'*'` could be treated as a single parenthesis `')'` or `'('` or `an empty string`.
5. An empty string is also valid.

- Example 1:
> **Input**: "()" <br>
> **Output**: True <br>
- Example 2:
> **Input**: "(*)" <br>
> **Output**: True <br>
- Example 3:
> **Input**: "(*))" <br>
> **Output**: True <br>
- Example 4:
> **Input**: "*)(" <br>
> **Output**: False <br>

## Brute Force
Recursive call. For each `'*'` we try the three possible scenarios: *empty string, '(' or ')'*.

**Time: O(N*3^N) <br> Space: O(N)**

```go
func checkValidString(s string) bool {
    return check(s, 0)
}

func check(s string, count int) bool {
    for i, c := range s {
        if c == '(' {
            count++
        } else if c == ')' {
            if count <= 0 {
                return false
            } else {
                count--
            }
        } else {
            return check(s[i + 1:], count) ||
                check(s[i + 1:], count - 1) ||
                check(s[i + 1:], count + 1)
        }
    }
    
    return count == 0
}
```

## Optimal
In a classical `'(' and ')'` problem we use a **count** variable to keep track if the string is
valid. For each `(` we **count++** and for each `)` we **count--**. When count becomes negative, we
return **false** and at the end the string is valid if **count == 0**.

In this case the `'*'` represents an *empty string or '(' or ')'*, so, count can increase, decrease
or remain the same. E.g.

| string | ( | ( | * | ) | ) |
| --- | --- | --- | --- | --- | --- |
| count | 1 | 2 | 1, 2 or 3 | 0, 1 or 2 | 0 or 1 |

Then, we can use two vars, say `hi` and `lo`, to keep track of the limits set by the `*'s`. For each
`(` we *hi++ and lo++* (like a regular count variable) and for each `)` we *hi-- and lo--*. But when
we have a `*`, we *hi++ and lo--*.

| string | ( | ( | * | ) | ) |
| --- | --- | --- | --- | --- | --- |
| hi | 1 | 2 | 3 | 2 | 1 |
| lo | 1 | 2 | 1 | 0 | 0 |

If any time **hi < 0** (no opening brackets available), we return false. In case **lo < 0**, we
reset it to 0, since a negative will put the counts in a invalid state. At the end we return
*lo == 0*.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func checkValidString(s string) bool {
    hi, lo := 0, 0
    for _, c := range s {
        if c == '(' {
            hi++
            lo++
        } else if c == ')' {
            lo--
            hi--
        } else {
            hi++
            lo--
        }
        
        if hi < 0 {
            return false
        }
        if lo < 0 {
            lo = 0
        }
    }
    return lo == 0
}
```
### Java solution
```java
class Solution {
    public boolean checkValidString(String s) {
        int hi = 0, lo = 0;
        for(int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if(c == '(') { 
                hi++; 
                lo++; 
            } else if(c == ')') { 
                hi--;
                lo--; 
            } else { 
                hi++; 
                lo--;
            }
            
            if(hi < 0) 
                return false;
            if(lo < 0) 
                lo = 0;
        }
        
        return lo == 0;
    }
}
```