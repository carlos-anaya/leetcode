# 283. Move Zeroes

Given an array `nums`, write a function to move all 0's to the end of it while maintaining the 
relative order of the non-zero elements. Do it **in place** and minimize the number of operations.

- Example 1:
> **Input**: [0,1,0,3,12] <br>
> **Output**: [1,3,12,0,0]
- Example 2:
> **Input**: [0,0,0,0,5] <br>
> **Output**: [5,0,0,0,0]

## Brute Force
Nested loop: for each idx that has a *zero* traverse until a *non-zero* value is found and swap.

**Time: O(n²) <br> Space: O(1)**

## Optimal
Use a **two-pointer** algorithm. Use `curr` to move through the array and `zeroIdx` to point to
the left-most index that is a 0. When we found a *non-zero* num, we swap it with `num[zeroIdx]` and
increment `zeroIdx`. As we progress, all the values to the left of `zeroIdx` are *non-zero*.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func moveZeroes(nums []int)  {
    for curr, zeroIdx := 0, 0; curr < len(nums); curr++ {
        if nums[curr] != 0 {
            swap(nums, curr, zeroIdx)
            zeroIdx++
        } 
    }
}

func swap(nums []int, p1, p2 int) {
    nums[p1], nums[p2] = nums[p2], nums[p1]
}
```
### Java solution
```java
class Solution {
    public void moveZeroes(int[] nums) {
        for(int curr = 0, zeroIdx = 0; curr < nums.length; curr++) {
            if(nums[curr] != 0) {
                swap(nums, curr, zeroIdx);
                zeroIdx++;
            }
        }
    }
    private void swap(int[]nums, int p1, int p2) {
        int tmp = nums[p1];
        nums[p1] = nums[p2];
        nums[p2] = tmp;
    }
}
```