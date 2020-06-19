# 946. Validate Stack Sequences (Medium)

Given two sequences `pushed` and `popped` with **distinct values**, return true if and only if this
could have been the result of a sequence of push and pop operations on an initially empty stack.

- Example 1:
> **Input**: pushed = [1,2,3,4,5], popped = [4,5,3,2,1] <br>
> **Output**: true <br>
> **Explanation**: We might do the following sequence: <br>
> push(1), push(2), push(3), push(4), pop() -> 4, <br>
> push(5), pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1
- Example 2:
> **Input**: pushed = [1,2,3,4,5], popped = [4,3,5,1,2] <br>
> **Output**: false <br>
> **Explanation**: 1 cannot be popped before 2

## Optimal
Use a `stack` or a `linked-list`. We use a pointer `j` to keep track of the next item to be checked
from `popped` and keep push elements from `pushed` to the stack/list. In case the peek of the stack
is the same as `pop[j]`, we pop from the stack and advance `j`. If at the end of the process we end
up with an empty stack it means that the sequence is valid.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
type ListNode struct {
    Val int
    Next *ListNode
}

func validateStackSequences(pushed []int, popped []int) bool {
    var head *ListNode
    j := 0
    for _, num := range pushed {
        head = &ListNode{num, head}
        for head != nil && popped[j] == head.Val {
            head = head.Next 
            j++
        }
    }
    
    if head == nil{
        return true
    }
    
    return false
}
```
### Java solution
```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack();
        int j = 0;
        
        for(int i = 0; i < pushed.length; i++) {
            stack.push(pushed[i]);
            while(!stack.isEmpty() && popped[j] == stack.peek()) {
                stack.pop();
                j++;
            }
        }
        
        return stack.isEmpty();
    }
}
```