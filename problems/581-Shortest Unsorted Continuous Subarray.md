# 581. Shortest Unsorted Continuous Subarray (Easy)

Given an integer array, you need to find one continuous subarray that if you only sort this subarray in ascending order, then the whole array will be sorted in ascending order, too.

You need to find the shortest such subarray and output its length.

- Example 1:
> **Input**: [2,6,4,8,10,9,15] <br>
> **Output**: 5 <br>
> **Explanation**: You need to sort [6,4,8,10,9] in ascending order to make the whole array sorted.
- Example 2:
> **Input**: [2,6,4,12,8,3,10,9,15] <br>
> **Output**: 7 
- Example 3:
> **Input**: [1,2,3] <br>
> **Output**: 0 

## Brute Force
We can create a copy of the array and sort it, then we can compare both arrays to find the first
mistmatch from left to right and from right to left. Those are the indices of the subarray that
needs to be sorted.

> [2,6,4,8,10,9,15] (nums) <br>
> [2,**4**,6,8,9,**10**,15] (sorted nums: 4 is the left mismatch and 10 is the right mismatch)

**Time: O(n\*logn) <br> Space: O(n)**

## Optimal
We first find the min number that is a mismatch to the left and the max number that is a mismatch
to the right. *But how?* If we consider a sorted array, we have an **increasing monotonic sequence**
from left to right. Then, we can find the elements that break that *monotonic inc sequence* to the 
left the *monotonic dec sequence* to the right. We are interested in the min and max value 
respectively. Let's consider this:

- [2,6,4,12,8,3,10,9,15]
> 2,6,4 - 4 breaks the inc sequence, then this is a candidate <br>
> 2,6,4,12,8 - 8 breaks the inc sequence, but it's greater than 4, so it's not a candidate <br>
> 2,6,4,12,8,3 - 3 breaks the inc sequence and is smaller than 4, so it's the new candidate <br>

Now from right to left we find elements that break the **decreasing monotonic sequence**, and we
pick the max. In this case we have *12*.

We have effectively found the left (min) and right (max) elements that comprise the *unsorted array*
and now we can just find the proper position of those elements. I.e. for *min* nums[l=5] = 3, we
find the correct position (l=1) and the same for *max* nums[r=3] = 12 and it's ideal position (r=7).

**Time: O(1) <br> Space: O(0)**

### Java solution
```java
class Solution {
    public int findUnsortedSubarray(int[] nums) {
        int n = nums.length, l = 0, r = 0;
        // loop over the array to find the min/max values & idxs
        // that are not in order
        for(int i = 1; i < n; i++) {
            // decreasing subsequence to the right and new min
            if(nums[i] < nums[i-1] && (l == 0 || nums[i] < nums[l])) {
                l = i;
            }
            int j = n - i - 1;
            // increasing subsequence to the left and new max
            if(nums[j] > nums[j+1] && (r == 0 || nums[j] > nums[r])) {
                r = j;
            }
        }
        // array already sorted (i.e. all is monotonic inc/dec)
        if(l == 0 && r == 0)
            return 0;
        
        // find the correct idx of where the min/max should be in the array
        int min = nums[l];
        int max = nums[r];
        while(l > 0 && nums[l - 1] > min)
            l--;
        while(r < n - 1 && nums[r + 1] < max)
            r++;
        return r - l + 1;
    }
}
```