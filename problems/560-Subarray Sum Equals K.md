# 560. Subarray Sum Equals K

- Example 1:
> **Input**: arr=[1,1,1], k=2 <br>
> **Output**: 2 <br>
> **Explanation**: the subarrays are [1,1], [1,1]
- Example 2:
> **Input**: arr=[3,1,0,3,-2,1,4,1], k=4 <br>
> **Output**: 5 <br>
> **Explanation**: the subarrays are [3,1], [3,1,0], [1,0,3], [-2,1,4,1], [4]
- Example 2:
> **Input**: arr=[3,1,0,3,-2,1,4,1], k=2 <br>
> **Output**: 3 <br>
> **Explanation**: the subarrays are [1,0,3,-2], [0,3,-2,1], [3,-2,1]

## Brute Force
Nested loop: **for i=0 to n-1** and **for j=i to n-1**, keep track of *current sum* 
(num[i]+..+num[j]) and in case it's equal to **k**, increase **count**.

**Time: O(n²) <br> Space: O(1)**

## Optimal
A classical optimization of *n²* algorithms (with nested loops) is to use a **map**. In this case,
we notice that if there's a *sum[i] = sum[j] - k (j > i)*, then we have a *sum == k from i+1 to j*.

Consider the array below, when *j = 3*, the *sum = 7* and *sum - k = 3*, we can see that when
*i = 0* we have a *sum=3*. We can store the **sums** in a *map*, so we can do quick lookups and
solve the problem in one pass.

Note that we need a map since we need to keep track of the occurrences of a given sum, so we can
count properly. Additionally, we have to add the *key 0*, in case there are occurrences of
*sum[j] == k*.

**k = 4**
idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7
--- | --- | --- | --- | --- | --- | --- | --- | ---
arr | 3 | 1 | 0 | 3 | -2 | 1 | 4 | 1
sum | 3 | 4 | 4 | 7 | 5 | 6 | 10 | 11
sum - k | -1 | **0** | **0** | **3** | 1 | 2 | **6** | **7**

**map**
sum | freq
--- | ---
0 | 1
3 | 1
4 | 2
7 | 1
5 | 1
6 | 1
10 | 1
11 | 1

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func subarraySum(nums []int, k int) int {
    freqMap := make(map[int]int)
    freqMap[0] = 1
    sum, ans := 0, 0
    for _, num := range nums {
        sum += num
        if freq, found := freqMap[sum - k]; found {
            ans += freq
        }
        if freq, found := freqMap[sum]; found {
            freqMap[sum] = freq + 1
        } else {
            freqMap[sum] = 1
        }
    }
    
    return ans
}
```
### Java solution
```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap();
        map.put(0, 1);
        int ans = 0, sum = 0;
        
        for(int num : nums) {
            sum += num;
            if(map.containsKey(sum - k)) {
                ans += map.get(sum - k);
            } 
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        
        return ans;
    }
}
```