# 136. Single Number

Given a non-empty array of integers, every element appears **twice** except for one. Find that 
element in **linear time**.

- Example 1:
> **Input**: [2,2,5] <br>
> **Output**: [5]
- Example 2:
> **Input**: [2,1,5,5,2,9,1] <br>
> **Output**: [9]

## Brute Force
Nested loop: for each value traverse until the same number is found. If not the duplicated is not 
found, that is the answer.

**Time: O(n²) <br> Space: O(1)**

## Brute Force II
Use a map/set. If a duplicate exists remove the number from the map. At the end, the remaining key
is the answer.

**Time: O(n) <br> Space: O(n)**

## Optimal
Use **Bit Manipulation**. By using `XOR` this can be solved with constant space. All the duplicates
will be cancelled out, and the remaining value is the answer.
- *a* XOR *0* = *a*
- *a* XOR *a* = *0*

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func singleNumber(nums []int) int {
    ans := 0
    for _, num := range nums{
        ans ^= num
    }
    
    return ans
}
```
### Java solution
```java
class Solution {
  public int singleNumber(int[] nums) {
    int ans = 0;
    for (int num : nums) {
      ans ^= num;
    }
    return ans;
  }
}
```
