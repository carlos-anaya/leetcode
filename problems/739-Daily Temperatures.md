# 730. Daily Temperatures (Medium)
Given a list of daily temperatures `T`, return a list such that, for each day in the input, 
tells you how many days you would have to wait until a warmer temperature. If there is no future 
day for which this is possible, put 0 instead. For example: 
> T = [73, 74, 75, 71, 69, 72, 76, 73] <br>
> ans = [1, 1, 4, 2, 1, 1, 0, 0]

## Brute Force
Nested loop: for each node traverse until a greater value is found

**Time: O(n²) <br> Space: O(1)**

## Optimal
Use a stack with a pair `<val, idx>`. 
1. Count the nodes and create an *answer* array
2. Add elements to the stack as long as those are smaller or equal to *stack.peek()* 
3. When a node value is greater than *stack.peek()*, pop elements as long as 
  `curr.val > stack.peek()`. Set `answer[popped.idx] = curr.head`.
  *I.e. the current element is bigger than x elements in the stack*

**Time: O(n) <br> Space: O(n)**

### Go solution
```Go
type ListNode struct {
    Val int
    Next *ListNode
}

func dailyTemperatures(T []int) []int {
    n := len(T)
    if n == 0 {
        return T
    }
    
    head := &ListNode{n-1, nil}
    ans := make([]int, n)
    
    for i := n - 1; i >= 0; i-- {
        for head != nil && T[i] >= T[head.Val] {
            head = head.Next
        }
        if head != nil {
            ans[i] = head.Val - i
        }
        head = &ListNode{i, head}
    }
    
    return ans
}
```
### Java solution
```Java
class Solution
    public int[] dailyTemperatures(int[] T) {
        int n = T.length;
        int[] ans = new int[n];
        if(n == 0) { return ans; }
        
        Stack<Integer> stack = new Stack();
        
        for(int i = n - 1; i >= 0; i--) {
            while(!stack.empty() && T[stack.peek()] <= T[i]) {
                stack.pop();
            }
            
            if(!stack.empty()) {
                ans[i] = stack.peek() - i;
            }
            stack.push(i);
        }
        
        return ans;
    }
}
```