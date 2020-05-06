# 169. Majority Element

Given an array of size *n*, find the majority element. The majority element is the element that
**appears more** than *⌊n/2⌋* times.

You may assume that the array is non-empty and the majority element always exist in the array.

- Example 1:
> **Input**: [3,2,3] <br>
> **Output**: 3
- Example 2:
> **Input**: [2,2,1,1,1,2,2] <br>
> **Output**: 2
- Example 2:
> **Input**: [7,2,5,7,2,7,7,5,7] <br>
> **Output**: 7

## Brute Force
Nested loop: for each element, count it's occurrences and keep track of the *maxCount* and the
*maxElement*.

**Time: O(n²) <br> Space: O(1)**

## Approach I - Sorting
Sort the input, then the majority element will be at position n/2.

**Time: O(n\*logn) <br> Space: O(n) | O(1)** (if sorting in place is allowed, e.g. merge sort)

## Aproach II - HashMap
Use a map with *<element, count>* pairs. Loop over *key,value* pairs and keep track of the
*maxCount* and the *maxElement*.

```go
func majorityElement(nums []int) int {
    numMap := make(map[int]int)
    
    for _, num := range nums {
        if _, found := numMap[num]; found {
            numMap[num] = 0
        }
        numMap[num]++
    }
    
    maxCount, maxValue := 0, 0
    for k, v := range numMap {
        if v > maxCount {
            maxCount = v
            maxValue = k
        }
    }
    
    return maxValue
}
```

**Time: O(n) <br> Space: O(n)** (we can at most store n/2 elements, so space complexity is O(n))

## Optimal - Boyer-Moore Algorithm
By intuition, we could pick a number *candidate* and check if it's a majority by having a counter
and add *1* if next element is the same as *candidate* or *0* otherwise. If after *j* elements the
*count==0*, we found a candidate that appeared *j/2* times (the other *j/2* were other numbers), so
we can continue checking from *j+1*. E.g.

num | 7 | 2 | 5 | 5 | 7 | 7 | 7 | 7 | 5
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
candidate | 7 | 7 | 5 | 5 | 5 | 5 | 7 | 7 | 7
count | 1 | 0 | 1 | 2 | 1 | 0 | 1 | 2 | 1

### Go solution
```go
func majorityElement(nums []int) int {
    candidateCount, candidate := 0, 0
    for _, num := range nums {
        // we need a new candidate
        if candidateCount == 0 {
            candidate, candidateCount = num, 1
            continue
        }
        // check if current element is same as the candidate 
        if num == candidate {
            candidateCount++
        } else {
            candidateCount--
        }
    }
    return candidate
}
```
### Java solution
```java
class Solution {
    public int majorityElement(int[] nums) {
        int candidate = 0, candidateCount = 0;
        for(int num : nums) {
            if(candidateCount == 0) {
                candidate=num;
                candidateCount=1;
                continue;
            }
            if(num == candidate)
                candidateCount++;
            else
                candidateCount--;
        }
        return candidate;
    }
}
```