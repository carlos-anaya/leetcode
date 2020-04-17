# 238. Product of Array Except Self

Given an array `nums` of n integers where *n > 1*,  return an array *output* such that *output[i]* 
is equal to the product of all the elements of nums **except nums[i]**.

- Example 1:
> **Input**: [1,2,3,4] <br>
> **Output**: [24,12,8,6] <br>

**Note**: Please solve it without division and in *O(n)*.

**Follow up**:
Could you solve it with *constant* space complexity? <br>
(The output array does not count as extra space for the purpose of space complexity analysis)

## Brute Force
Nested loop. Set *prod \*= nums[j]* and at the end of the inner loop, set *num[i] = prod*. Skip
updating *prod* when *i == j*.

**Time: O(n²) <br> Space: O(1)**

## Sub-Optimal
We can see that the prods of `[2,3,4,5]` are *[3\*4\*5, 2\*4\*5, 2\*3\*5, 2\*3\*4]*.
We can create two arrays with products as follows 
- *products* from the left (prod[i] = num[i-1]\*prod[i-1]):<br>

| value | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- |
| prodsLeft | 1 | 1\*2 | 1\*2\*3 | 1\*2\*3\*4 |

- *products* from the right (prod[i] = num[i+1]\*prod[i+1]):<br>

| value | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- |
| prodsRight | 1\*5\*4\*3 | 1\*5\*4 | 1\*5 | 1 |

At the end we just need to multiply both *prod* arrays.

**Time: O(n) <br> Space: O(n)**

## Optimal
Instead of two arrays as above, we can use a single array and a `prod` variable as helper.

- First pass (to the right) *prods[i] = num[i-1]\*prod, prod \*= nums[i - 1]*

| value | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- |
| prod | 1 | 1 | 1\*2 | 1\*2\*3 |
| prods | 1 | 1\*2 | 1\*2\*3 | 1\*2\*3\*4 |

- Second pass (to the left) *prods[i] \*= num[i+1]\*prod, prod \*= nums[i + 1]*

| value | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- |
| prod | 1\*5\*4 | 1\*5 | 1 | 1 |
| prods | 1\*5\*4\*3 | 1\*2\*5\*4 | 1\*2\*3*5 | 1\*2\*3\*4 |

**NOTE** the *prod* shown is the value before *prod \*= nums[i - 1]*, i.e. the value used by *prods*

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func productExceptSelf(nums []int) []int {
    n := len(nums)
    prods := make([]int, n)
    prods[0] = 1
    prod := 1
    
    for i := 1; i < n; i++ {
        prods[i] = prod * nums[i - 1]
        prod *= nums[i - 1]
    }
    
    prod = 1
    for i := n - 2; i >= 0; i-- {
        prods[i] *= prod * nums[i + 1]
        prod *= nums[i + 1]
    }
    
    return prods
}
```
### Java solution
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] prods = new int[n];
        prods[0] = 1;
        int prod = 1;
        
        for(int i = 1; i < n; i++) {
            prods[i] = prod * nums[i - 1];
            prod *= nums[i - 1];
        }
        
        prod = 1;
        for(int i = n - 2; i >= 0; i--) {
            prods[i] = prods[i] * prod * nums[i + 1];
            prod *= nums[i + 1];
        }
        
        return prods;
    }
}
```