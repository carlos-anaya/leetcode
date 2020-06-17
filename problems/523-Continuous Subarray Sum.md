# 523. Continuous Subarray Sum (Medium)

Given a list of *non-negative* numbers `nums` and a target integer `k`, write a function to check if
the array has a **continuous subarray** of size at least 2 that sums up to a *multiple* of `k`, that
is, sums up to *n\*k* where *n* is also an integer.

- Example 1:
> **Input**: nums=[23, 2, 4, 6, 7],  k=6 <br>
> **Output**: True <br>
> **Explanation**: [2,4] is a continuous subarray of size 2 and sums up to 6
- Example 2:
> **Input**: nums=[23, 2, 6, 4, 7],  k=6 <br>
> **Output**: True <br>
> **Explanation**: Because [2,6,4] is an continuous subarray of size 3 and sums up to 12.<br>
> Note that [23, 2, 6, 4, 7] also is an continuous subarray of size 5 and sums up to 42.

## Brute Force
Nested loop: We generate subarrays and keep track of a cumulative sum. Then we check if the `sum` is
equal to `k` or if `sum%k == 0`

**Time: O(n²) <br> Space: O(1)**

```go
func checkSubarraySum(nums []int, k int) bool {
    for i := 0; i < len(nums) - 1; i++ {
        sum := nums[i]
        for j := i + 1; j < len(nums); j++ {
            sum += nums[j]
            // sum is already k or k is non zero and the remainder is 0
            if sum == k || (k != 0 && sum % k == 0) {
                return true
            }
        }
    }
    return false
}
```

## Optimal
Similar to the approach in [560-Subarray Sum Equals K](560-Subarray%20Sum%20Equals%20K.md) we use a
`HashMap` to reduce time complexity and to do a single run over the array.

The map will store the **remainders** of the cumulative `sum` mod `k`, this is `sum%k` and the index
of the last number added to the sum, then if at any point we find that that value exists, let's say
*arr[j]*, then we know that from *arr[i+1]* to *arr[j]* we have a subarray that `sum%k` (*i* is the
index of the found value in the map).

- **Example**: (k=6)

idx | 0 | 1 | 2 | 3 | 4
--- | --- | --- | --- | --- | ---
nums | 23 | 2 | 6 | 4 | 7 
sum | 23 | 25 | 31 | 35 | 42 
sum % k | **5** | 1 | 1\* | **5** | 0\*\* 

\* - Here we have found a rem = 1, that previously existed, but this is not valid since they're
  next to each other, meaning there's only one number multiple of `k`, in this case 6.
\*\* - Here we have found rem = 0, which is valid, then we need a base value in the map to consider
  that escenario (map.put(0, -1))
**5** we have found a rem = 5 that previously existed, meaning that we have a subarray from i + 1 to
  j that has a sum multiple of `k`. This is from index 1 to 3 (2,6,4).

**Time: O(n) <br> Space: O(n)**

### Java solution
```java
class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap();
        map.put(0, -1); // base case for initial subarray that sum == k
        int sum = 0;
        
        for(int i = 0; i < nums.length; i++) {
            sum += nums[i];
            if(k != 0) // k can be 0
                sum %= k;
            if(map.containsKey(sum)) { 
                // we have this rem in the map and it has at least two numbers
                if(i - map.get(sum) > 1)
                    return true;
            } else { // we don't have this rem in the map, so add it
                map.put(sum, i);
                
            }
        }
        
        return false; // no subarray sums are multiples of k
    }
}
```