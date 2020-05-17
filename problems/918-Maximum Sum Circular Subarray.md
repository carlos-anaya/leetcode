# 918. Maximum Sum Circular Subarray (Medium)

Given a **circular array C** of integers represented by `A`, find the maximum possible sum of a
non-empty subarray of **C**.

Here, a circular array means the end of the array connects to the beginning of the array.  
(Formally, `C[i] = A[i]` when `0 <= i < A.length`, and `C[i+A.length] = C[i]` when `i >= 0`),

Also, a subarray may only include each element of the fixed buffer `A` at most once.  

- Example 1:
> **Input**: [1,-2,3,1,-2] <br>
> **Output**: 4 <br>
> **Explanation**: the maximum subarray is [3,1]
- Example 2:
> **Input**: [5,1,-3,5] <br>
> **Output**: 11 <br>
> **Explanation**: the maximum subarray is [5,5,1]
- Example 3:
> **Input**: [3,-2,2,-3] <br>
> **Output**: 3 <br>
> **Explanation**: the subarray [3] and [3,-2,2] both have maximum sum 3
- Example 4:
> **Input**: [-2,-3,-1] <br>
> **Output**: -1 <br>
> **Explanation**: the maximum subarray is [-1]

## Brute Force
For maximum sum subarray we use `Kadane's algorithm`. However, for circular *subarrays* we need to
do something else. A brute force approach would be to *"concatenate"* the array with itself and then
having a *"sliding window"* of size *lenArr*. We apply kadane every loop. I.e. kadane(arr[0:n-1]),
then kadane(arr[1:n-1] + arr[0:1]) until kadane(arr[n-1:n-1] + arr[0:n-1]).

**Time: O(n²) <br> Space: O(1)**

## Sub-Optimal
If we see closely, we have two choices:
1. The max subarray is within the array, or
2. The max subarray takes part of the *tail* and *head* subarray.

![alt circular array](https://assets.leetcode.com/users/motorix/image_1538888300.png)

So, we have to consider both options. For the first one we can do a regular kadane over the array.
For the second one, we can invert the sign of the elements of the array and do a kadane. This will
give us the *maximum "negative"* sub-array. If we sum all the elements and then substract the 
*"negative" max* we have the value of the *head + tail* subarray. Finally, we return the max of the
two values calculated.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func maxSubarraySumCircular(A []int) int {
    if num := maxInArray(A); num <= 0 {
        return num
    }
    // regular kadane for subarray in the middle
    ans1 := kadane(A)
    
    sum := 0
    for i, num := range A {
        sum += num
        A[i] *= -1
    }
    // ans2 is the sum of the array minus the "negative" max subarray in the middle
    ans2 := sum + kadane(A)
    
    return max(ans1, ans2)
}

func maxInArray(nums []int) int {
    maxNum := math.MinInt32
    for _, num := range nums {
        maxNum = max(maxNum, num)
    }
    return maxNum
}

func kadane(nums []int) int {
    ans, sum := 0, 0
    for _, num := range nums {
        sum = max(sum + num, num)
        ans = max(ans, sum)
    }
    return ans
}
```

## Sub-Optimal
Consider the approach before. Instead of doing two passes over the array we can do a single pass,
applying a *max subarray sum Kadane*, a *min subarray sum Kadane* and a *total sum*. Basically,
instead of a second pass over the array with inverted sign, we do a Kadane for the *min sum* and
then substact it from *total sum*, this would give us the the *max head + tail sum* subarray.

. | 5 | -1 | -3 | 6
--- | --- | --- | --- | ---
totSum | 5 | 4 | 1 | 7
currMinSum | 5 | -1 | -4 | 2
currMaxSum | 5 | 4 | 1 | 7
minSum | 5 | -1 | -4 | -4
maxSum | 5 | 5 | 5 | 7

*totSum - minSum = 7 - (-4) == 11*

**Time: O(n) <br> Space: O(1)**

### Java solution
```java
class Solution {
    // limits of the nums
    final int MAX = 30000;
    final int MIN = -30000;
    
    public int maxSubarraySumCircular(int[] A) {
        // note, we can't use Integer.MAX_VALUE
        // because it will overflow when adding a number
        int minSum = MAX, currMinSum = MAX;
        int maxSum = MIN , currMaxSum = MIN;
        int totSum = 0;
        
        for(int num : A) {
            totSum += num;
            currMinSum = Math.min(currMinSum + num, num);
            currMaxSum = Math.max(currMaxSum + num, num);
            
            minSum = Math.min(minSum, currMinSum);
            maxSum = Math.max(maxSum, currMaxSum);
        }
        // System.out.println(minSum + " " + maxSum + " " + totSum);
        // edge case, all numbers are negative so minSum is the whole array
        // return the max negative number
        if(minSum == totSum)
            return maxSum;
        
        return Math.max(maxSum, totSum - minSum);
    }
}
```