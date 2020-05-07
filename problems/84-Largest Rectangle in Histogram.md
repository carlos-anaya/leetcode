# 84. Largest Rectangle in Histogram

Given *n* non-negative integers representing the histogram's bar height where the width of each bar
is 1, find the area of largest rectangle in the histogram.

- Example 1:
> **Input**: [2,1,5,6,2,3] <br>
> **Output**: 10 <br>
> **Explanation**: the largest rectangle is between the 3rd and 4th elements (5 and 6) <br>
![alt histogram](https://assets.leetcode.com/uploads/2018/10/12/histogram_area.png)

## Brute Force
Nested loop: for each rectangle, find the first rectangle to the left that is greater or equal than
*current*, then keep ading the value to *currArea* until a rectangle smaller is found at the right.

Update *maxArea* if *currArea* is greater.

idx | 0 | 1 | 2 | 3 | 4 | 5
--- | --- | --- | --- | --- | --- | ---
nums | 2 | 1 | 5 | 6 | 2 | 3
max area | 2 | 6 | 10 | 6 | 8 | 3

**Time: O(n²) <br> Space: O(1)**

```go
func largestRectangleArea(heights []int) int {
  maxArea := 0
  for i, num := range heights {
    currArea := 0
    for j, h := range heights {
      if h >= num {
        // curr bar is greater or equal than curr rectangle's height
        currArea += num
      } else {
        if j > i { 
          // curr bar is smaller than curr rectangle's height
          // and it's after the curr rectangle
          break
        } else {
          // curr bar is smaller than curr rectangle's height
          // but it's before curr rectangle
          currArea = 0
        }
      }
    }
    maxArea = max(maxArea, currArea)
  }
  return maxArea
}
```

## Optimal
Use a `stack`. Keep pushing elements as long as they're higher than previous (*stack.peek()*). In
case the *curr rectangle (idx=i)* is smaller than a previous rectangle, we need to *pop* that
element and calculate it's area. The *height* of the area is the height of the *popped number* and
the *widht* of the area has *size = i - stack.peek() - 1*. Why that size? Because we just popped a
higher rectangle than current, then it's size is from the previous smaller rectangle (stack.peek()),
i.e. the width is the number of rectangles between two smaller rectangles (stack.peek() and curr).

- Example:

idx | 0 | 1 | 2 | 3
--- | --- | --- | --- | --- |
nums | 1 | 5 | 6 | 2

1. At idx = 0, push 0 (stack is empty)
2. At idx = 1, push 1 (rect=5 > rect=1(peek))
3. At idx = 2, push 2 (rect=6 > rect=5(peek))
4. At idx = 3, pop 2 (rect=2 < rect=6(peek)). <br>
  We have h=6 (popped rect), w=3-1-1 = 1 (i=3, peek=1) so currArea = 6*1 = 6. 
5. At idx = 3, pop 1 (rect=2 < rect=5(peek)).  <br>
  We have h=5 (popped rect), w=3-0-1 = 2 (i=3, peek=0) so currArea = 5*2 = 10. 
6. At idx = 3, push 3 (rect=2 > rect=1(peek))
7. At idx = 4, pop 3. <br>
  We have h=2 (popped rect), w=4-0-1 = 3 (i=4, peek=0) so currArea = 2*3 = 6. 
7. At idx = 4, pop 0.  <br>
  We have h=1 (popped rect), w=4 (i=4, stack empty) so currArea = 1*4 = 4.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func largestRectangleArea(heights []int) int {
    maxArea := 0
    stack := NewStack(len(heights))
    i := 0
    for i < len(heights) {
        currHeight := heights[i]
        for !stack.Empty() && heights[stack.Peek()] >= currHeight {
            prevRect := stack.Pop()
            prevHeight := heights[prevRect]
            width := i
            if !stack.Empty() {
                width = i - stack.Peek() - 1
            }
            maxArea = max(maxArea, prevHeight * width)
        }
        stack.Push(i)
        i++
    }
    
    // Pop remaining elements from stack
    for !stack.Empty() {
        prevRect := stack.Pop()
        prevHeight := heights[prevRect]
        width := i
        if !stack.Empty() {
            width = i - stack.Peek() - 1
        }
        maxArea = max(maxArea, prevHeight * width)
    }
    
    return maxArea
}
```
### Java solution
```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int maxArea = 0;
        Stack<Integer> stack = new Stack();
        
        int i = 0;
        for(; i<heights.length; i++) {
            int currH = heights[i];
            while(!stack.isEmpty() && heights[stack.peek()] >= currH) {
                int p = stack.pop();
                int h = heights[p];
                int w = stack.isEmpty() ? i : i - stack.peek() - 1;
                maxArea = Math.max(maxArea, h * w);
            }
            // current is greater than stack.peek()
            stack.push(i);
        }
    
        // Pop remaining elements
        while(!stack.isEmpty()) {
            int p = stack.pop();
            int h = heights[p];
            int w = stack.isEmpty() ? i : i - stack.peek() - 1;
            maxArea = Math.max(maxArea, h * w);
        }
        
        return maxArea;
    }
}
```