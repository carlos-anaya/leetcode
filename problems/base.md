# 1111. Name

- Example 1:
> **Input**: [2,1,5] <br>
> **Output**: [5,5,0] <br>
> **Explanation**:
- Example 2:
> **Input**: [2,7,4,3,5] <br>
> **Output**: [7,0,5,5,0] <br>
> **Explanation**:

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

```
### Java solution
```java

```