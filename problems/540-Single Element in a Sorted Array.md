# 540. Single Element in a Sorted Array (Medium)

You are given a sorted array consisting of only integers where every element appears exactly twice,
except for one element which appears exactly once. Find this single element that appears only once.

- Example 1:
> **Input**: [1,1,2,3,3,4,4,8,8] <br>
> **Output**: 2
- Example 2:
> **Input**: [3,3,7,7,10,11,11] <br>
> **Output**: 10

## Brute Force
We can do a single loop starting from *i=0 to n-2; i+=2* and check if *nums[i] == nums[i+1]*.
Return *nums[i]* when the condition is not met.

**Time: O(n) <br> Space: O(1)**

## Optimal
We can take advantage of the *sorted array* and use a binary search. When we compute *mid*, we can
check if the idx is *even* or *odd*, and then we can check whether the *num[mid] = num[mid-1]*.
This will let us know what subarray has the single number. 
E.g. if *m is odd && num[mid] = num[mid-1]*, then the left subarray is odd, thus the *single number*
should be there.

idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8
--- | --- | --- | --- | --- | --- | --- | --- | --- | ---
nums| 1 | 1 | 3 | 3 | 4 | 4 | 7 | 8 | 8
loop 1 | l | | | | m | | | | r
loop 2 | | | | | l | | m | | r
loop 3 | | | | | | | l | m | r

- The first loop *m* is even and no duplicate before, thus the left sub-array is even
- Second loop is same as above. Note that *l = m* because *nums[m]* might be the single num
- Third loop *m* is odd and no duplicate before, thus right sub-array is even. *r=m-1*, thus *l=r=8*

**Time: O(logn) <br> Space: O(n)**

### Go solution
```go
func singleNonDuplicate(nums []int) int {
    l, r := 0, len(nums) - 1
    for l < r {
        m := (r-l)/2 + l
    
        // if m % 2 == 0, we're in the middle of l & r
        // i.e. both subarrays l..m-1 and m+1..r are even
        if m % 2 == 0 {
            // if nums[m-1] is same as nums[m] the left subarray becomes odd
            // so the single number is there, so we skip nums[m] and nums[m-1]
            if nums[m] == nums[m-1] { // current's duplicate is before
                r = m - 2
            } else { // single number is in right subarray
                l = m // we can't discard nums[m] since it might be the single num
            }
        } else {
            // m is odd, so considering nums[m] we have an even subarray.
            // if nums[m] == nums[m-1], the left subarray is even, so
            // single is in right subarray, then skip nums[m]
            if nums[m] == nums[m-1] {
                l = m + 1
            } else { // right subarray is even considering nums[m], so single 
                // has to be in left side
                r = m - 1
            }
        }
    }
    return nums[l]
}
```
### Java solution
```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int l = 0, r = nums.length - 1, m = 0;
        while(r > l) {
            m = (r-l)/2 + l;
            if(m % 2 == 0) {
                if(nums[m-1] == nums[m])
                    r = m - 2;
                else
                    l = m;
            } else {
                if(nums[m-1] == nums[m])
                    l = m + 1;
                else
                    r = m - 1;
            }
        }
        return nums[l];
    }
}
```