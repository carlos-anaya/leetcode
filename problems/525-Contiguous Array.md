# 525. Contiguous Array

Given a binary array, find the maximum length of a contiguous subarray with equal number of 0 and 1.
- Example 1:
> Input: [1,1,0] <br>
> Output: 2 (idx 1,2)
- Example 2:
> Input: [1,0,0,1,0,0,0,1] <br>
> Output: 4 (idx 0,3)
- Example 2:
> Input: [1,1,1] <br>
> Output: 0

## Brute Force
Nested loop: Check for each sub-array `<i,j>`, keep a count of 0's and 1's. When *countZeros == countOnes*, there's a match of length `j - i + 1`.

**Time: O(n²) <br> Space: O(1)**

## Optimal
We can iterate the array just once by keeping a `count` and a `max` variables. For each *0* we add *-1* and for each *1* we add *1*. Use a map with a `<count, idx>` pair. If we haven't seen that *count* before, we add it to the map. If there's a  *count*, it means we have a substring where `count == 0` and it's length is `i - map[count]`, then we update *max* accordingly. 

**Base case**: considering an `arr = [0, 1]` then we'd have `map=[{-1,0}, {0,1}]` and we'd never found a match!
To solve this case, we initialize the map with a pair `{0,-1}`.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func findMaxLength(nums []int) int {
    max, currCount := 0, 0
    countMap := make(map[int]int) // <count, idx> pair
    countMap[0] = -1 // base case
    
    for idx, num := range nums {
        if num == 0 {
            currCount--
        } else {
            currCount++
        }
        
        if val, found := countMap[currCount]; found {
            max = getMax(max, idx - val)
        } else {
            countMap[currCount] = idx
        }
    }
    
    return max
}

func getMax(x, y int) int {
    if x > y {
        return x
    }
    return y
}
```
### Java solution
```java
class Solution {
    public int findMaxLength(int[] nums) {
        int max = 0;
        int currCount = 0;
        Map<Integer, Integer> countMap = new HashMap();
        countMap.put(0, -1);
        
        for(int i = 0; i < nums.length; i++) {
            if(nums[i] == 0) { currCount--; }
            else { currCount++; }
            
            if(countMap.containsKey(currCount)) {
                max = Math.max(max, i - countMap.get(currCount));
            } else {
                countMap.put(currCount, i);
            }
        }
        return max;
    }
}
```