# 1209. Remove All Adjacent Duplicates in String II (Medium)

Given a string `s`, a *k duplicate removal* consists of choosing `k` adjacent and equal letters from
`s` and removing them causing the left and the right side of the deleted substring to concatenate
together.

We repeatedly make `k` duplicate removals on `s` until we no longer can.

Return the final string after all such duplicate removals have been made.

It is guaranteed that the answer is unique.

- Example 1:
> **Input**: s = "abcd", k = 2 <br>
> **Output**: "abcd" <br>
> **Explanation**: None of the chars repeats
- Example 2:
> **Input**: s = "deeedbbcccbdaa", k = 3 <br>
> **Output**: "aa" <br>
> **Explanation**: First delete "eee" and "ccc", get "ddbbbdaa" <br>
> Then delete "bbb", get "dddaa" <br>
> Finally delete "ddd", get "aa"

## Optimal
Use a **stack** or a **linked list** with a tuple `<char, count>` and loop over the string from
*end to start* to avoid reversing the final string (i.e. pop the elements in order).
1. If the stack is *empty* or *peek() != curr_char*, add a new tuple for *curr_char* and *count = 1*
2. If *peek() == curr_char* there are two choices, if *peek().count - 1 < k* then we just need to
  increment the *peek().count*, otherwise we need to pop the tuple (it has k repeated elements).

Finally, pop from the stack to build the output string. Add *count* times each *char*.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
type ListNode struct {
    Val rune
    Count int
    Next *ListNode
}

func removeDuplicates(s string, k int) string {
    if len(s) < k {
        return s
    }
    
    var head *ListNode
    count := 0 // use to keep track of the total number of chars
    for _, c := range s {
        if head == nil || head.Val != c { // different char
            head = &ListNode{c, 1, head}
            count++
        } else {
            if head.Count + 1 < k {
                head.Count = head.Count + 1
                count++
            } else {
                head = head.Next // pop element
                count = count - k + 1
            }
        }
    }
    
    // pop chars into a rune array - for efficiency
    ans := make([]rune, count)
    for head != nil {
        for i := 0; i < head.Count; i++ {
            ans[count - 1] = head.Val
            count--
        }
        head = head.Next
    }
    
    return string(ans)
}
```
### Java solution
```java
class Solution {
    class Tuple {
        char val;
        int count;
        Tuple(char val, int count) {
            this.val = val;
            this.count = count;
        }
    }
    
    public String removeDuplicates(String s, int k) {
        int n = s.length();
        if(n < k) {
            return s;
        }
        
        Stack<Tuple> stack = new Stack();
        for(int i = n - 1; i >= 0; i--) {
            char c = s.charAt(i);
            if(stack.isEmpty() || stack.peek().val != c) {
                stack.push(new Tuple(c, 1));
            } else {
                Tuple t = stack.peek();
                if(t.count + 1 < k) {
                    t.count++;
                } else {
                    stack.pop();
                }
            }
        }
        
        StringBuilder sb = new StringBuilder();
        while(!stack.isEmpty()) {
            Tuple t = stack.pop();
            for(int i = 0; i < t.count; i++) {
                sb.append(t.val);
            }
        }
        
        return sb.toString();
    }
}
```