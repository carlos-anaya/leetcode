# 155. Min Stack

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

- **push(x)** -- Push element x onto stack.
- **pop()** -- Removes the element on top of the stack.
- **top()** -- Get the top element.
- **getMin()** -- Retrieve the minimum element in the stack.

- Example 1:
> MinStack minStack = new MinStack(); <br>
> minStack.push(-2); <br>
> minStack.push(0); <br>
> minStack.push(-3); <br>
> minStack.getMin();   --> Returns -3. <br>
> minStack.pop(); <br>
> minStack.top();      --> Returns 0. <br>
> minStack.getMin();   --> Returns -2.

## Optimal
For the stack we can use the default stack (Java) or use *ListNodes* or in Go use an slice.
We could either: 
- have two stacks, one for the values and one for the mins 
- store tuples (val, min) in the stack

**Time: O(1) <br> Space: O(n)**

### Go solution
```go
/* USING PAIR SLICE */
type Pair struct {
    Val int
    Min int
}

type MinStack struct {
    pairs []*Pair
}

func (m *MinStack) Size() int {
    return len(m.pairs)
}

func (m *MinStack) Empty() bool {
    return len(m.pairs) == 0
}

func Constructor() MinStack {
    pairs := make([]*Pair, 0)
    return MinStack{pairs}
}

func (this *MinStack) Push(x int)  {
    var min int
    if this.Empty() || this.pairs[this.Size() - 1].Min > x {
        min = x
    } else {
        min = this.pairs[this.Size() - 1].Min
    }
    this.pairs = append(this.pairs, &Pair{x, min})
}

func (this *MinStack) Pop()  {
    if !this.Empty() {
        this.pairs = this.pairs[:this.Size() - 1]
    }
}

func (this *MinStack) Top() int {
    if !this.Empty() {
        return this.pairs[this.Size() - 1].Val
    }
    return -1
}

func (this *MinStack) GetMin() int {
    if !this.Empty() {
        return this.pairs[this.Size() - 1].Min
    }
    return -1
}


/* USING LIST NODE */
type ListNode struct {
    Val int
    Min int
    Next *ListNode
}

type MinStack struct {
    Head *ListNode
}

func Constructor() MinStack {
    return MinStack{nil}
}

func (this *MinStack) Push(x int)  {
    if this.Head == nil || this.Head.Min > x {
        this.Head = &ListNode{x, x, this.Head}
    } else {
        this.Head = &ListNode{x, this.Head.Min, this.Head}
    }
}

func (this *MinStack) Pop()  {
    if this.Head == nil {
        return
    }
    this.Head = this.Head.Next
}

func (this *MinStack) Top() int {
    if this.Head != nil {
        return this.Head.Val
    }
    return -1
}

func (this *MinStack) GetMin() int {
    if this.Head != nil {
        return this.Head.Min
    }
    return -1
}
```
### Java solution
```java
class MinStack {
    
    class ListNode {
        int val;
        int min;
        ListNode next;
        
        ListNode(int val, int min, ListNode next) {
            this.val = val;
            this.min = min;
            this.next = next;
        }
    }
    
    ListNode head;

    public MinStack() {
        head = null;
    }
    
    public void push(int x) {
        if(head == null || x < head.min) {
            head = new ListNode(x, x, head);
        } else {
            head = new ListNode(x, head.min, head);
        }
    }
    
    public void pop() {
        if(head == null) {
            return;
        }
        head = head.next;
    }
    
    public int top() {
        if(head == null) {
            return 0;
        }
        return head.val;
    }
    
    public int getMin() {
        if(head == null) {
            return 0;
        }
        return head.min;
    }
}
```