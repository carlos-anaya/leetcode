# 328. Odd Even Linked List (Medium)

Given a *singly linked list*, group all **odd** nodes together followed by the **eve**n nodes.
Please note here we are talking about the node number and not the value in the nodes.

You should do it **in place**. The program should run in *O(1) space* complexity and *O(nodes) time*
complexity.

- Example 1:
> **Input**: 1->2->3->4->5->NULL <br>
> **Output**: 1->3->5->2->4->NULL
- Example 2:
> **Input**: 2->1->3->5->6->4->7->NULL <br>
> **Output**: 2->3->6->7->1->5->4->NULL

## Optimal
Since the problem asks for a Space O(1) and Time O(n) complexities, we need to solve it in one pass.
We can keep two pointers `odd` and `even` nodes, then each node moves two nodes forward. At the end
we need to set the final odd node.next to the *even head*. 

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func oddEvenList(head *ListNode) *ListNode {
    if head == nil {
        return nil
    }
    odd, even, evenHead := head, head.Next, head.Next
    // odd and even move two nodes forward
    // have to ensure odd will not be null, so we can append even list at the end
    for even != nil && even.Next != nil {
        // even.Next is odd, the next odd
        odd.Next = even.Next
        odd = odd.Next
        // odd.Next is event, the next even
        even.Next = odd.Next
        even = even.Next
    }
    // last odd's next is the first even node
    odd.Next = evenHead
    return head
}
```
### Java solution
```java
class Solution {
    public ListNode oddEvenList(ListNode head) {
        if(head == null)
            return head;
        
        ListNode odd = head;
        ListNode even = head.next;
        ListNode evenHead = even;
        
        while(even != null && even.next != null) {
            odd.next = even.next;
            even.next = odd.next.next;
            
            odd = odd.next;
            even = even.next;
        }
        odd.next = evenHead;
        return head;
    }
}
```