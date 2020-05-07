# xxx. Counting Elements
Given an integer array `arr`, count element x such that *x + 1* is also in arr. If there are 
duplicates in arr, count them separately.

- Example 1:
> **Input**: [1,2,3] <br>
> **Output**: 2 <br>
> **Explanation**: 1 and 2 are counted because 2 and 3 are in arr.
- Example 2:
> **Input**: [1,1,3,3,5,5,7,7] <br>
> **Output**: 0 <br>
> **Explanation**: There's no 2, 4, 6 or 8
- Example 3:
> **Input**: [1,2,3,1,2,4,1,6] <br>
> **Output**: 6 <br>
> **Explanation**: 1 appears 3-times, 2 appears twice and 3 appears once 

## Brute Force
Sort the array. Keep a variable for the **curr val** and the **count**. When we find a new *val* we 
check if it's equal to *prev val +1*. If so, we add *count* to answer, update *curr* and continue.

**Time: O(nlogn) <br> Space: O(1)**

## Optimal
Use a map with a pair `<num, count>`, iterate over `arr` and update the map. Then, iterate over
**key/values** from the map, and verify if the **key + 1** exists in the map. If so, add the **val**
to the answer.<br>
Considering *example 3*, the map would be {{1:3},{2:2},{3:1},{4:1},{6:1}}. Then, we iterate over 
each pair, so for *{1:3}* there's a key *1+1*, so we add *3* to answer.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func countElements(arr []int) int {
    nums := make(map[int]int)
    for _, num := range arr {
        if _, found := nums[num]; !found {
            nums[num] = 0
        }
        nums[num]++
    }
    
    ans := 0
    for num, count := range nums {
        if _, found := nums[num+1]; found {
            ans += count
        }
    }
    
    return ans
}
```
### Java solution
```java
class Solution {
    public int countElements(int[] arr) {
        Map<Integer, Integer> nums = new HashMap();
        for(int num : arr) {
            if(!nums.containsKey(num))
                nums.put(num, 0);
            
            nums.put(num, nums.get(num) + 1);
        }
        
        int ans = 0;
        for(int num : nums.keySet()) {
            if(nums.containsKey(num + 1))
                ans += nums.get(num);
        }
        
        return ans;
    }
}
```