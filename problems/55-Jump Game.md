# 55. Jump Game

Given an array of non-negative integers, you are positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

- Example 1:
> **Input**: [2,3,1,1,4] <br>
> **Output**: True <br>
> **Explanation**: we can do 2->3->4 or 2->1->1->4
- Example 2:
> **Input**: [3,2,1,0,4] <br>
> **Output**: False <br>
> **Explanation**: all possible paths end in 0, so we can't reach 4

## Brute Force
The brute-force approach would be a simple **recursive function**. We have to explore each index
from 0 to nums[i]. If none of those idx's reached the end, return false:

```go
func canJump(nums []int) bool {
    return helper(nums, 0)
}

func helper(nums []int, idx int) {
    if idx >= len(nums) - 1 {
        return true
    }
    
    for i := nums[idx]; i >= 0; i-- {
        if helper(nums, idx + i) {
            return true
        }
    }
    
    return false
}
```

**NOTE**: this code will cause time-limit-exceeded exceptions for large numbers, e.g. 
*nums=[777, 157, ..., 221, 1, 0]*

**Time: O(2^n) <br> Space: O(n)**

## DP top-down

Similar to the code above, but we keep a **memo** array to keep track of visited idx's and marking
as *-1* when they don't help to reach the last index and *1* in case they do.

**Time: O(n²) <br> Space: O(1)**

## Optimal - Greedy (DP bottom-up)
Let's consider the *element at position n-2*, if its **value** is 1 (or greater), then we can reach
the last element, so this becomes the new element to reach. If it's value is 0, then the
*element at position n-3* has to be 2 (or greater) so we can jump to *n-1*... we can formalize this
as **if num[i] + i >= n, then n = i, for i > n**. At the end, if *n == 0* then it's possible to 
reach the last element, so we return *true*.

- Example:

idx | 0 | 1 | 2 | 3 | 4 | 5
--- | --- | --- | --- | --- | --- | ---
nums | 1 | 2 | 0 | 3 | 0 | 1
n | 0 | 1 | 3 | 3 | 5 | 5

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func canJump(nums []int) bool {
    // Base case, we're already at last position
    if len(nums) < 2 {
        return true
    }
    
    n := len(nums) - 1
    for i := n-1; i >= 0; i-- {
        // If we can reach position n from i
        // then use i as the new index to reach
        if nums[i] + i >= n {
            n = i
        }
    }
    
    // Check if we reached the beginning of the array
    return n == 0
}
```
### Java solution
```java
class Solution {
    public boolean canJump(int[] nums) {
        if(nums.length < 2)
            return true;
        
        int n = nums.length - 1;
        for(int i = n - 1; i >= 0; i--) {
            if (nums[i] + i >= n)
                n = i;
        }
        
        return n == 0;
    }
}
```