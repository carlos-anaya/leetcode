# 410. Split Array Largest Sum (Hard)

Given an array which consists of non-negative integers and an integer *m*, you can *split the array*
into *m non-empty continuous subarrays*. Write an algorithm to **minimize the largest sum** among 
these *m subarrays*.

**Note**
If *n* is the length of array, assume the following constraints are satisfied:

- 1 ≤ n ≤ 1000
- 1 ≤ m ≤ min(50, n)

- Example 1:
> **Input**: [7,2,5,10,8] <br>
> **Output**: 18 <br>
> **Explanation**:There are four ways to split nums into two subarrays.
> The best way is to split it into [7,2,5] and [10,8],
> where the largest sum among the two subarrays is only 18.

## Optimal
Use a `binary search` algorithm. We find a *"bucket capacity"* size and loop over the elements,
checking how many *buckets* we can fill, and the max value (sum) for that capacity. If we need *m=3*
buckets, and *bucket_capacity=x* produced 2 buckets, then the size is too big, so we reduce it. On
the other hand, if the output was 4 buckets, then the size is too small, so we increment it. When we
find a *bucket_capacity* that led to *m* buckets, we record the *max size* of those buckets and
update the *min_max_sum*.

Example:
- arr=[7,2,5,10,8], m=2

l | r | mid| buckets | maxBucketSum | subarrays | minMaxSum
--- | --- | --- | --- | --- | --- | ---
0 | 32 | 16 | 3 | 14 | [7,2,5],[10],[8] | ∞
17 | 32 | 24 | 2 | 24 | [7,2,5,10],[8] | 24
17 | 23 | 20 | 2 | 18 | [7,2,5],[10,8] | 18
17 | 19 | 18 | 2 | 18 | [7,2,5],[10,8] | 18
17 | 17 | 17 | 3 | 14 | [7,2,5],[10],[8] | 18


- arr=[4,7,5,4,10,8,11,3], m=4

l | r | mid| buckets | maxBucketSum | subarrays | minMaxSum
--- | --- | --- | --- | --- | --- | ---
0 | 52 | 26 | 3 | 20 | [4,7,5,4],[10,8],[11,3] | ∞
0 | 25 | 12 | 6 | 11 | [4,7],[5,4],[10],[8],[11],[3] | ∞
13 | 25 | 19 | 4 | 19 | [4,7,5],[4,10],[8,11],[3] | 19
13 | 18 | 15 | 5 | 14 | [4,7],[5,4],[10],[8],[11,3] | 19
16 | 18 | 17 | 4 | 16 | [4,7,5],[4,10],[8],[11,3] | 16
16 | 16 | 16 | 4 | 16 | [4,7,5],[4,10],[8],[11,3] | 16

**Time: O(n\*log(sum n)) <br> Space: O(1)**

### Go solution
```go
func splitArray(nums []int, m int) int {
    l, r, minMaxSum := 0, 0, math.MaxInt32
    for _, num := range nums {
        r += num
    }
    
    for l <= r {
        mid := (r - l) / 2 + l
        buckets := 0
        currSum, maxBucketSum := 0, 0
        for _, num := range nums {
            if currSum + num > mid { // adding the num overflows the bucket
                maxBucketSum = max(maxBucketSum, currSum)
                buckets++
                currSum = num
            } else {
                currSum += num
            }
        }
        // count last sum in bucket
        maxBucketSum = max(maxBucketSum, currSum)
        buckets++
        
        // found m buckets, get the min of largest sum of bucket
        if buckets == m {
            minMaxSum = min(minMaxSum, maxBucketSum)
        }
        if buckets > m { // bucket is too short, need to increase it
            l = mid + 1
        } else { // bucket is too large
            r = mid - 1
        }
    }
    
    return minMaxSum
}
```
### Java solution
```java
class Solution {
    public int splitArray(int[] nums, int m) {
        int l = 0, r = 0, minMaxSum = Integer.MAX_VALUE;
        for(int num : nums) {
            r += num;
        }
        
        while(r >= l) {
            int mid = (r-l)/2 + l;
            int currSum = 0, maxBucketSum = 0, buckets = 0;
            for(int num : nums) {
                if(currSum + num > mid) { // adding the num overflows the bucket
                    buckets++;
                    maxBucketSum = Math.max(maxBucketSum, currSum);
                    currSum = num;
                } else {
                    currSum += num;
                }
            }
            // count the last bucket and bucket sum
            buckets++;
            maxBucketSum = Math.max(maxBucketSum, currSum);
            if(buckets == m) {
                minMaxSum = Math.min(minMaxSum, maxBucketSum);
            }
            if(buckets > m) // bucket is too short
                l = mid + 1;
            else    // bucket is too big
                r = mid - 1;
        }
        
        return minMaxSum;
    }
}
```