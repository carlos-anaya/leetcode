# 1. Two Sum (Easy)

Given an array of integers, return **indices** of the two numbers such that they add up to a 
specific target.

You may assume that each input would have exactly one solution, and you may not use the same
element twice.

- Example 1:
> **Input**: nums = [2, 7, 11, 15], target = 9 <br>
> **Output**: [0, 1] <br>
> **Explanation**: nums[0] + nums[1] = 2 + 7 = 9

## Brute Force
Nested loop: for each number loop over the array and check if *num[i] + num[j] == target*.

**Time: O(n²) <br> Space: O(1)**

## Optimal
Use a `hashmap`. Check if *target - num[i]* exists in the map, if not, then insert *<num[i],i>* in
the map.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func twoSum(nums []int, target int) []int {
    numMap := make(map[int]int)
    
    for i,num := range nums {
        if j, ok := numMap[target - num]; ok {
            return []int {j, i}
        }
        numMap[num] = i
    }
    
    return []int{}
}
```
