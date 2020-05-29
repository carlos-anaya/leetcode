# 128. Longest Consecutive Sequence (Hard)

Given `nums` an *unsorted array of integers*, find the length of the longest consecutive elements sequence.

Your algorithm should run in **O(n) complexity**.

- Example 1:
> **Input**: nums=[100, 4, 200, 1, 3, 2] <br>
> **Output**: 4 <br>
> **Explanation**: The longest consecutive elements sequence is [1, 2, 3, 4] and has length 4.

## Brute Force
Sort the array, then just loop through it and keep track of the longest consecutive sequence.

**Time: O(n\*logn) <br> Space: O(n|1)** (depending on sorting algorithm)

## Optimal
How do we know if a number is part of a *sequence*? 

Considering *nums=[100,4,200,1,3,2]*, how can we check if *nums[0] (100)* is part of a *sequence*?
We check if *99* or *101* are present in the array, and if so, we can check if *98* or *102* are in
the sequence and so on...

Then, we can use a *Set*. We first put all the numbers in the set. Then we loop through the numbers
in the set and remove in an inner loop the elements to the left and right. E.g.

- Example: nums = [100, 4, 200, 1, 3, 2]
  - First process 100, no elements to the left/right. Remove 100.
  - Process 4. No elements to the right, but 3 is to the left, then 2 and then 1. Remove 1,2,3,4.
    and the current max is 4.
  - Process 200, no elements to the left/right. Remove 200.
  - End of loop, set is empty.

Despite the inner loops, the overall time complexity remains *linear* since the elements in the set
will be processed only once.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func longestConsecutive(nums []int) int {
    set := make(map[int]bool)
    maxSeq := 0
    
    // put all numbers in the set
    for _, num := range nums { set[num] = true }
    
    // loop until all the elements in the set have been processed
    for i := 0; len(set) > 0; i++ {
        // skip if the number has been processed
        if _, found := set[nums[i]]; !found { continue }
        
        delete(set, nums[i])
        currSeq := 1
        prev, next := nums[i] - 1, nums[i] + 1
        
        for { // process the sequence to the left
            if _, found := set[prev]; !found { break }
            delete(set, prev)
            currSeq++
            prev--
        }
        for { // process the sequence to the right
            if _, found := set[next]; !found { break }
            delete(set, next)
            currSeq++
            next++
        }
        // update maxSeq if currSeq
        if currSeq > maxSeq {
            maxSeq = currSeq
        }
    }
    return maxSeq
}
```
### Java solution
```java
class Solution {
    public int longestConsecutive(int[] nums) {
        int max = 0;
        Set<Integer> set = new HashSet();
        // put all the numbers in the set
        for(int num : nums)
            set.add(num);
        
        // process the numbers
        // for each number find the sequence to the left and right
        for(int i = 0; !set.isEmpty(); i++) {
            // number has been processed in a sequence, so skip it
            if(!set.contains(nums[i]))
                continue;
            
            set.remove(nums[i]);
            int currLongest = 1;
            int prev = nums[i]-1;
            int next = nums[i]+1;
            
            // find the elements in the sequence to the left
            while(set.contains(prev)) {
                set.remove(prev);
                currLongest++;
                prev--;
            }
            // find the elements in the sequence to the right
            while(set.contains(next)) {
                set.remove(next);
                currLongest++;
                next++;
            }
            // update max if there's a new longest subsequence
            max = Math.max(max, currLongest);
        }
        
        return max;
    }
}
```