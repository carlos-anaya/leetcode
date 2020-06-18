# 45. Jump Game II

Given an array of non-negative integers, you are positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the *minimum number of jumps*. You can assume that you can 
*always* reach the last index.

- Example 1:
> **Input**: [2,3,1,1,4] <br>
> **Output**: 2 <br>
> **Explanation**: best path is 2->3->4

## Brute Force
Use a **jumps array** of size len(nums) to keep track of the best we can do at each *idx*. 
Initialize the array to the *int.max* and then iterate through the nums. Inside the loop update
jumps from *i+1 to i+nums[i]* (from current idx to the max jump we can do in from there).

```go
func jump(nums []int) int {
    if len(nums) < 2 {
        return 0
    }
    
    n := len(nums)
    jumps := make([]int, n)
    for i := range jumps {
        jumps[i] = math.MaxInt32 
    }
    jumps[0] = 0
    for i := 0; i < n; i++ {
        // update jumps from i+1 to i+nums[i] (e.g. all the jumps we can do from here)
        for j := 1; j <= nums[i] && i+j < n; j++ {
            jumps[i+j] = min(jumps[i+j], jumps[i] + 1)
        }
    }
    return jumps[n-1]
}
```
**Time: O(n*m) <br> Space: O(n)** (n -> size of nums, m -> max jump value)

## Optimal
We can simulate a window of size **curr max jump**. For example, starting from *idx=0* the 
**curr max jump** we can do is 2, so the next window is *from idx=1 to idx=2*. We move through that
window and we find the **next max jump** is 4 (from idx=1 we can do 3 jumps, so we can reach idx=4).

Everytime we finish a "window" we must add 1 to *jumps* since we took the *next max jump* we could
in the previous window.

idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7
--- | --- | --- | --- | --- | --- | --- | --- | ---
nums | 2 | 3 | 1 | 3 | 4 | 2 | 0 | 1
jumps | 0 | 1 | 1 | 2 | 2 | 3 | 3 | 3
curr max jump | 2 | 2 | 2 | 4 | 4 | 8 | 8 | 8
next max jump | 2 | 4 | 4 | 6 | 8 | 8 | 8 | 8

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func jump(nums []int) int {
    if len(nums) < 2 { // base case
        return 0
    }
    
    jumps, currMaxJump, nextMaxJump := 0, nums[0], nums[0]
    for i := 1; i < len(nums); {
        jumps++
        // Find next max jump in curr window
        for ; i < len(nums) && i <= currMaxJump; i++ {
            nextMaxJump = max(nextMaxJump, i + nums[i])
        }
        currMaxJump = nextMaxJump
    }
    
    return jumps
}
```
### Java solution
```java
class Solution {
    public int jump(int[] nums) {
        if(nums.length < 2)
            return 0;
        
        int jumps = 0, currMaxJump = nums[0], nextMaxJump = nums[0];
        
        for(int i = 1; i < nums.length;) {
            jumps++;
            while(i < nums.length && i <= currMaxJump) {
                nextMaxJump = Math.max(nextMaxJump, nums[i] + i);
                i++;
            }
            currMaxJump = nextMaxJump;
        }
        
        return jumps;
    }
}
```