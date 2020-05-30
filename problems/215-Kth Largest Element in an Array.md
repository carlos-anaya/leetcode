# 215. Kth Largest Element in an Array (Medium)

Find the **kth largest** element in an unsorted array. Note that it is the kth largest element in
the *sorted order*, not the kth distinct element.

- Example 1:
> **Input**: nums=[3,2,1,5,6,4], k = 2 <br>
> **Output**: 5 <br>
> **Explanation**: the sorted array is 1,2,3,4,5,6. The 2nd largest element is 5
- Example 2:
> **Input**: nums=[3,2,3,1,2,4,5,5,6], k=4 <br>
> **Output**: 4

## Brute Force
Sort the array, then return the number at position *len(nums) - k*.

**Time: O(n\*logn) <br> Space: O(1)**

## Sub-Optimal
Use a `max heap` of size *K*. Loop over the array and push numbers to the heap. Then, at the end
poll numbers from the heap. The *last polled* number is the kth largest element.

Conversely, we can use a `max heap` of size k+1. Loop over the array and push numbers to the heap,
and once the heap has size k+1, poll firs element, so we ensure the heap always has k elemens.
Return the *first polled* number from the heap.

**Time: O(n\*logk) <br> Space: O(k)**

## Optimal
We use a variation of *quick sort* named `quick select`. We choose a pivot and swap elements smaller
that pivot to the left and elements larger than pivot to the right. The last step will place *pivot*
in its right place, let's say *i*. If *i == len(nums)-k*, we have found the kth, largest number.
The elements to the right/left are greater/smaller and they are not sorted, but we don't care, we
just care about the *kth largest* number. If *i < len(nums)-k* then we search in the right subarray,
otherwise we search in the left subarray.

The trick is using a `random pivot index`. **Why?** If the input is already sorted, and we keep on
taking the first/last number as *pivot*, we'll end up moving one element every loop (all the
elemets would be either smaller or larger than pivot), so this would degrade to an *O(n²)* time
complexity. We instead choose a random pivot index in the range *l..r* and then swap the number to
the end, so we ensure the pivot is the last element to be swapped to its correct position.

**Time: Avg O(n), Worst O(n²) <br> Space: Avg O(n), Worst O(n)**

### Go solution
```go
func findKthLargest(nums []int, k int) int {
    // sort ascending, so we're looking for the (len(nums)-k)th element
    return quickSort(nums, 0, len(nums)-1, len(nums)-k)
}

func quickSort(nums []int, l, r, target int) int {
    if r <= l {
        return nums[l]
    }
    
    // generate random index between l and r
    pivotIdx := rand.Intn(r - l) + l
    // swap rand pivot to the end, so pivot is the last
    // element to be swapped to it's right position
    swap(nums, pivotIdx, r)
    
    i, j, pivot := l-1, l, nums[r]
    
    for ; j <= r; j++ {
        // swap nums smaller than pivot to the left
        if nums[j] <= pivot {
            i++
            swap(nums, i, j)
        }
    }

    // ith number is greater than kth number, so find in left subarray
    if i > target {
        return quickSort(nums, l, i-1, target)
    } else if i < target { // ith number is smaller than kth number, so find in right subarray
        quickSort(nums, i+1, r, target)
    }
    // i == target so we found the kth largest in array
    return nums[target]
}

func swap(arr []int, i1, i2 int) {
    arr[i1], arr[i2] = arr[i2], arr[i1]
}
```
### Java solution
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        return quickSelect(nums, 0, nums.length-1, nums.length-k);
    }
    
    private int quickSelect(int[] nums, int l, int r, int target) {
        if(l >= r)
            return nums[l];
        
        // get a random pivot index in range l.. r
        int pivotIdx = new Random().nextInt(r-l) + l;
        // move pivot to the end so it is the last swapped number
        swap(nums, pivotIdx, r);
        int pivot = nums[r], i = l-1, j = l;
        
        for(;j <= r; j++) {
            if(nums[j] <= pivot) {
                i++;
                swap(nums, i, j);
            }
        }
        // ith number is smaller, so look in the right subarray
        if(i < target)
            return quickSelect(nums, i+1, r, target);
        else if(i > target) // ith number is larger, so look in the left subarray
            return quickSelect(nums, l, i-1, target);
        
        // found the kth largest element
        return nums[i];
    }
    
    private void swap(int[]nums, int i1, int i2) {
        int tmp = nums[i1];
        nums[i1] = nums[i2];
        nums[i2] = tmp;
    }
}
```