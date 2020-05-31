# 1249. Minimum Remove to Make Valid Parentheses (Medium)

- Example 1:
> **Input**: [2,1,5] <br>
> **Output**: [5,5,0] <br>
> **Explanation**:
- Example 2:
> **Input**: [2,7,4,3,5] <br>
> **Output**: [7,0,5,5,0] <br>
> **Explanation**:

## Sub-Optimal
We use a `stack` to keep track of the invalid parentheses. We start from the end of the string and
move leftwards, so we don't need to reverse the final output.

- If we find a ')', we add the **index** to the stack.
- If we find a '(', we have two scenarios
  - If the peek of the stack corresponds to a ')', then we pop that index from the stack. I.e. this
    '(' closes last inserted ')', so this is valid.
  - Otherwise, we push the '(' to the stack and it will stay there since it's already invalid.
- Finally, we loop over the string again and check if the element exist in the stack or not. If it
  does, we pop it from the stack and skip it from the answer.

**Time: O(n) <br> Space: O(n)**

### Java solution
```java
class Solution {
    public String minRemoveToMakeValid(String s) {
        int n = s.length();
        Stack<Integer> stack = new Stack();
        
        // start from right to left so stack is in order at the end
        for(int i = n-1; i >= 0; i--) {
            char c = s.charAt(i);
            if(c == '(') {
                // if it's an opening parentheses and the peek is a close
                // this is balanced
                if(!stack.empty() && s.charAt(stack.peek()) == ')') {
                    stack.pop();
                } else { // unbalanced, no close parentheses
                    stack.push(i);
                }
            } else if (c == ')') {
                stack.push(i);
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for(int i = 0; i < s.length(); i++) {
            if(!stack.empty() && stack.peek() == i) {
                stack.pop();
            } else {
                sb.append(s.charAt(i));
            }
        }
        
        return sb.toString();
    }
}
```
## Optimal
We can remove the use of the `stack`, this will reduce the space complexity to *O(1)* (without
considering the output space). We first check the number of **close parentheses**. Then, we loop
again over the string keeping track of an **unmatched open parentheses** variable.

- If we have a "(" and the number of *unmatchedOpen* == *closeCount*, we skip this "(". I.e. the 
  open/close count is already balanced. Otherwise, we increase *unmatchedOpen* and append it to *sb*
- If we have a ")" we first reduce the *closeCount*. Then, if *unmatchedOpen* == 0, this ")" is
  invalid and we skip it. Otherwise, we reduce *unmatchedOpen* since this close parentheses "closes"
  an existing valid open parentheses.
- Any other char is appended to *sb*.

Why does this work? First, if there's a close parentheses and there's no open parentheses then it is
invalid and it has to be skipped. Likewise, once we've matched the number of open/close parentheses
any new open parentheses will be invalid and it should be skipped.

**input**: "c)o((d)e" **output**: "co(d)e"

input | c | ) | o | ( | ( | d | ) | e
--- | --- | --- | --- | --- | --- | --- | --- | ---
unmatched open | 0 | 0 | 0 | 0 | 1 | 1 | 1 | 0
close count | 2 | 2 | 1 | 1 | 1 | 1 | 1 | 0
append? | c | - | o | ( | - | d | ) | e


**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func minRemoveToMakeValid(s string) string {
    var sb strings.Builder
    unmatchedOpen, closeCount := 0, 0
    
    // count close parentheses
    for _, c := range s {
        if c == ')' { closeCount++ }
    }
    
    for _, c := range s {
        if c == '(' {
            // same number of open/close parentheses, so 
            // this new parentheses is invalid
            if unmatchedOpen == closeCount {
                continue
            }
            // an open parentheses is unmatched
            unmatchedOpen++
        } else if c == ')' {
            // reduce number of close parentheses
            closeCount--
            // no open parentheses available, so this close parentheses is invalid
            if unmatchedOpen == 0 {
                continue
            }
            // an open parentheses was matched, so we reduce the count
            unmatchedOpen--
        }
        sb.WriteRune(c)
    }
    
    return sb.String();
}
```
