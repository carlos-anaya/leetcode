# 876. Middle of the Linked List

Given a non-empty, singly linked list with head node `head`, return the middle node of linked list.
If there are two middle nodes, return the **second middle node**.

- Example:
> **Input**: [1,2,3,4,5] <br>
> **Output**: 3 (the list node, not the value) <br>
> **Explanation**: 3 is the middle of the list

## Brute Force
Put every node in an array/list, then return arr[arr.length/2 + 1].

**Time: O(n) <br> Space: O(n)**

## Optimal
Use the `slow, fast` pointer technique. **fast** moves two nodes at a time, so it'll reach the end
when **slow** is at the middle.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func middleNode(head *ListNode) *ListNode {
    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
    }
    
    return slow
}
```
### Java solution
```java
class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode slow = head, fast = head;
        
        while(fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        return slow;
    }
}
```