# 33. Search in Rotated Sorted Array

Suppose an array sorted in ascending order is *rotated* at some *pivot unknown* to you beforehand.

(i.e., *[0,1,2,4,5,6,7]* might become *[4,5,6,7,0,1,2]*).

You are given a *target* value to search. If found, return its index, otherwise return -1.

You may assume *no duplicate* exists in the array.

Your algorithm's runtime complexity must be in the order of **O(log n)**.

- Example 1:
> **Input**: nums = [4,5,6,7,0,1,2], target = 0 <br>
> **Output**: 4
- Example 2:
> **Input**: nums = [4,5,6,7,0,1,2], target = 3 <br>
> **Output**: -1

## Optimal
We know that the array is sorted so we can use a `binary search`. The caveat is that the array is
*rotated*. In a sorted array we know that *arr[i-1] < arr[i]*; in a rotated array there shoud be
one element which doesn't follow that rule, thus *arr[i-1] > arr[i]* where *arr[i]* is refered as
`pivot`.

We can do a *modified binary search* to find the pivot, and once found, we can search the *target*
in the proper sub-array, i.e. the elements before pivot, and elements from pivot.

**Time: O(log n) <br> Space: O(1)**

### Go solution
```go
func search(nums []int, target int) int {
    n := len(nums)
    if n == 0 {
        return - 1
    }
    pivot := findPivot(nums)
    
    // use binary search in the proper subarray
    if target > nums[n-1] {
        return binarySearch(nums, target, 0, pivot - 1)
    } else {
        return binarySearch(nums, target, pivot, n - 1)
    }
}

func findPivot(nums []int) int {
    n := len(nums)
    l, r := 0, n - 1
    
    for {
        m := (l + r + 1) / 2
        if m == 0 || nums[m] < nums[m-1] {
            return m
        } else if nums[m] > nums[n-1] {
            l = m + 1
        } else {
            r = m - 1
        }
    }
}

func binarySearch(nums []int, target, l, r int) int {
    for l <= r {
        m := l + (r - l) / 2
        if nums[m] == target {
            return m
        } else if nums[m] < target {
            l = m + 1
        } else {
            r = m - 1
        }
    }
    return -1
}
```
### Java solution
```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length;
        if(n == 0)
            return -1;
        
        int pivot = findPivot(nums);
        
        if(target > nums[n - 1]) {
            return binarySearch(nums, target, 0, pivot - 1);
        } else {
            return binarySearch(nums, target, pivot, n - 1);
        }
    }
    
    private int findPivot(int[] nums) {
        int n = nums.length;
        int l = 0, r = n - 1;
        
        while(true) {
            int m = (r - l + 1) / 2 + l;
            if(m == 0 || nums[m] < nums[m - 1]) {
                return m;
            } else if(nums[m] > nums[n - 1]) {
                l = m + 1;
            } else {
                r = m - 1;
            }
        }
    }
    
    private int binarySearch(int[] nums, int target, int l, int r) {
        while (r >= l) {
            int m = (r - l) / 2 + l;
            if(nums[m] == target) {
                return m;
            } else if(nums[m] > target) {
                r = m - 1;
            } else {
                l = m + 1;
            }
        }
        
        return -1;
    }
}
```