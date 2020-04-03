# 974. Subarray Sums Divisible by K

- Example 1:
>**Input**: A = [4,5,0,-2,-3,1], K = 5 <br>
>**Output**: 7 <br>
>**Explanation**: There are 7 subarrays with a sum divisible by K = 5:<br>[4, 5, 0, -2, -3, 1], [5], [5, 0], [5, 0, -2, -3], [0], [0, -2, -3], [-2, -3]


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