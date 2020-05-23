# 503. Next Greater Element II (Medium)

Given a **circular array**, print the *Next Greater Number* for every element. The 
*Next Greater Number* of a number x is the first greater number to its traversing-order next in the
array, which means you could search circularly to find its next greater number. If it doesn't exist,
output -1 for this number.

- Example 1:
> **Input**: [1,2,1] <br>
> **Output**: [2,-1,2] <br>
> **Explanation**: The first 1's next greater number is 2; <br>
> The number 2 can't find next greater number; <br>
> The second 1's next greater number needs to search circularly, which is also 2.
- Example 2:
> **Input**: [3,1,5,4,2,1] <br>
> **Output**: [5,5,-1,5,3,3]

## Brute Force
Nested loop: for each number loop until a greater value is found. 

**Time: O(n²) <br> Space: O(1)**

## Optimal
Similar to [496-Next Greater Element I](496-Next%20Greater%20Element%20I.md) we use a 
`monotonic stack`. The only concern is that we should consider a **circular array**. How do we
usually deal with *circular arrays*? By doing *two passes* over the input. The first run will do
find the majority element for the array, then, the second run will find greater elements for the 
existing elements in the stack, i.e. the elements that didn't get a majority element.

![circular array](https://gblobscdn.gitbook.com/assets%2F-M1hB-LnPpOmZGsmxY7T%2F-M1hBADdxsYKAvjevalu%2F-M1hBB0_iyWlcxtdlTdd%2F2.png?alt=media)

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func nextGreaterElements(nums []int) []int {
    n := len(nums)
    ans := make([]int, n)
    stack := make([]int, 0, n*2)
    
    // fill answer array with -1
    for i := 0; i < n; i++ {
        ans[i] = -1
    }
    
    // loop to n*2 to simulate the circular array
    for i := 0; i < n * 2; i++ {
        num := nums[i%n]
        for len(stack) > 0 && nums[stack[len(stack)-1]] < num {
            // ans[stack.peek()] has a greater element num
            peek := stack[len(stack)-1]
            ans[peek] = num
            // pop the element from the stack
            stack = stack[:len(stack)-1]
        }
        stack = append(stack, i%n)
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        Stack<Integer> stack = new Stack();
        int n = nums.length;
        int[] ans = new int[n];
        Arrays.fill(ans, -1);
        // loop until n*2 to simulate circular array
        for(int i = 0; i < n * 2; i++) {
            int num = nums[i%n];
            // pop elements from the stack if they're smaller than num
            while(!stack.empty() && nums[stack.peek()] < num) {
                // the stack.peek() has a greater element: num
                ans[stack.pop()] = num;
            }
            // push the index of the num
            stack.push(i%n);
        }
        return ans;
    }
}
```