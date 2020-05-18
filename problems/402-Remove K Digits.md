# 402. Remove K Digits (Medium)

Given a non-negative integer `num` represented as a string, remove `k` digits from the number so
that the new number is the smallest possible.

**Note**: The length of num is less than 10002 and will be ≥ k and the given num doesn't contain any
leading zero.

- Example 1:
> **Input**: num = "1432219", k = 3 <br>
> **Output**: 1219 <br>
> **Explanation**: Remove the digits 4, 3, and 2 to form the new number 1219 which is the smallest.
- Example 2:
> **Input**: num = "10200", k = 1 <br>
> **Output**: "200" <br>
> **Explanation**: Remove the leading 1. The output must not contain leading zeroes.
- Example 2:
> **Input**: num = "10", k = 2 <br>
> **Output**: "0" <br>
> **Explanation**: Remove all the digits from the number and it is left with nothing which is 0.

## Brute Force - Recursion
We can do a recursive call to return the *min value* of two paths: discarding the current element
and reducing k *(fn(arr[1:], k-1))* or keeping the current element and moving to the next element
*(arr[0] + fn(arr[1:], k))*. The base case is when *k==0*.

**Time: O(n²) <br> Space: O(1)**

## Optimal
We need to remove the *biggest number* in the **first increasing sequence**. In other words, we 
need to remove numbers from the left that are the biggest, i.e. if we have *str="12742"* we need to
remove *"7"* first, then *"4"*. Similarly, if we have *str="3001"*, we remove the *"3"* first and
then we can remove the leading 0's for "free".

We can use a `stack` to push *increasing numbers*. When we find a decreasing "number", then we have
a candidate for removal: *stack.peek()*. We keep popping elements from the *stack* as long as
*stack.peek() > num[i]* and *k > 0*. If at the end of the string *k > 0* we pop the remaining *k*
nums from the end of the string (i.e. the sequence was always increasing or same value, e.g. "12345"
or "2222").

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func removeKdigits(num string, k int) string {
    stack := NewStack(len(num))
    
    for _, c := range num {
        // if pop elements from the stack as long as they're
        // higher than current char
        for k > 0 && !stack.Empty() && stack.Peek() > c {
            stack.Pop()
            k--
        }
        stack.Push(c)
    }
    // pop elements from stack into an ans array
    // start from the end of the arrayreverted
    i := stack.Size() - 1
    ans := make([]rune, i+1)
    for !stack.Empty() {
        c := stack.Pop()
        // remove elements if not enough elements were popped previously
        if k > 0 {
            k--
            continue
        }
        ans[i] = c
        i--
    }
    // skip the leading zeroes
    for i, c := range ans {
        if c != 0 && c != '0' {
            return string(ans[i:])
        }
    }
    return "0"
}

type Stack struct {
    chars []rune
    size int
}

func NewStack(capacity int) Stack {
    chars := make([]rune, 0, capacity)
    return Stack{chars, 0}
}

func (s *Stack) Empty() bool {
    return s.size == 0
}

func (s *Stack) Size() int {
    return s.size
}

func (s *Stack) Push(c rune) {
    s.chars = append(s.chars, c)
    s.size++
}

func (s *Stack) Peek() rune {
    return s.chars[s.size-1]
}

func (s *Stack) Pop() rune {
    c := s.chars[s.size - 1]
    s.chars = s.chars[:s.size-1]
    s.size--
    
    return c
}
```
### Java solution
```java
class Solution {
    public String removeKdigits(String num, int k) {
        Stack<Character> stack = new Stack();
        stack.push(num.charAt(0));
        for(int i = 1; i < num.length(); i++) {
            char digit = num.charAt(i);
            while(k > 0 && !stack.empty() && stack.peek() > digit) {
                stack.pop();
                k--;
            }
            stack.push(digit);
        }
        
        StringBuilder sb = new StringBuilder();
        while(!stack.empty()) {
            char c = stack.pop();
            if(k > 0) {
                k--;
                continue;
            }
            sb.append(c);
        }
        
        return removeLeadingZeroes(sb.reverse().toString());
    }
    
    private String removeLeadingZeroes(String s) {
        for(int i = 0, z = 0; i < s.length(); i++, z++) {
            if(s.charAt(i) != '0')
                return s.substring(z);
        }
        return "0";
    }
}
```