# 1019. Next Greater Node In Linked List (Medium)

- Example 1:
> Input: [2,1,5] <br>
> Output: [5,5,0]
- Example 2:
> Input: [2,7,4,3,5] <br>
> Output: [7,0,5,5,0]
- Example 3:
> Input: [1,7,5,1,9,2,5,1] <br>
> Output: [7,9,9,9,0,5,0,0]


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
```go
/**
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */

type PairNode struct {
    Val int
    Idx int
    Next *PairNode
}

func nextLargerNodes(head *ListNode) []int {
    n := countNodes(head)
    ans := make([]int, n)
    var stack *PairNode
    
    i := 0
    for head != nil {
        for stack != nil && stack.Val < head.Val {
            temp := stack
            ans[temp.Idx] = head.Val
            stack = stack.Next
        }
        stack = &PairNode{head.Val, i, stack}
        head = head.Next
        i++
    }
    
    return ans
}

func countNodes(h *ListNode) (count int) {
    for h != nil {
        count++
        h = h.Next
    }
    return
}
```
### Java solution
```java
/**
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    class Tuple {
        int val;
        int idx;
        
        Tuple(int val, int idx) {
            this.val = val;
            this.idx = idx;
        }
    }
    
    public int[] nextLargerNodes(ListNode head) {
        Stack<Tuple> stack = new Stack();
        int count = countNodes(head);
        int[] ans = new int[count];
        
        for(int i = 0; head != null; i++) {
            while(!stack.empty() && stack.peek().val < head.val) {
                Tuple temp = stack.pop();
                ans[temp.idx] = head.val;
            }
            stack.push(new Tuple(head.val, i));
            head = head.next;
        }
        
        return ans;
    }
    
    private int countNodes(ListNode head) {
        int count = 0;
        for(; head != null; count++) {
            head = head.next;
        }
        return count;
    }
}
```