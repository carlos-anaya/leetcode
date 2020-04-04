# 53. Maximum Subarray
Given an integer array `nums`, find the contiguous subarray (containing at least one number) which 
has the largest sum and return its sum.
- Example 1:
> **Input**: [-2,1,-3,4,-1,2,1,-5,4] <br>
> **Output**: 6 <br>
> **Explanation**: the subarray with the largest sum is [4,-1,2,1]

## Brute Force
Nested loop

**Time: O(n²) <br> Space: O(1)**

## Optimal
Use `Kadane's Algorithm`. Basically we just need to check if number in index **i** increases the 
array or we should start counting from **i**
- Initialize **sum** and **max** with *nums[0]*
- Loop through `nums`, starting from 1.
- Let **curr**, be the current number. Check which is larger: adding **curr** to **sum** or starting 
  from **curr**
- Update **max** accordingly

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func maxSubArray(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    
    ans, sum := nums[0], nums[0]
    for i := 1; i < len(nums); i++ {
        sum = max(sum + nums[i], nums[i])
        ans = max(ans, sum)
    }
    
    return ans
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
### Java solution
```java
class Solution {
    public int maxSubArray(int[] nums) {
        if(nums.length == 0)
            return 0;
        
        int sum = nums[0];
        int max = sum;
        
        for(int i = 1; i < nums.length; i++) {
            sum = Math.max(sum + nums[i], nums[i]);
            max = Math.max(max, sum);
        }
        
        return max;
    }
}
```