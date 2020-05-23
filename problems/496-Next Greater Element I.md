# 496. Next Greater Element I (Easy)

You are given two arrays **(without duplicates)** `nums1` and `nums2` where `nums1`’s elements are
a *subset* of `nums2`. Find all the next greater numbers for nums1's elements in the corresponding
places of nums2.

The Next Greater Number of a number *x* in `nums1` is the first greater number to its right in 
`nums2`. If it does not exist, output *-1* for this number.

- Example 1:
> **Input**: nums1 = [4,1,2], nums2 = [1,3,4,2] <br>
> **Output**: [-1,3,-1] <br>
> **Explanation**: Numbers 4 and 2 have no next greater element in nums2. The next greater element
> of number 1 is 3.
- Example 2:
> **Input**: nums1 = [3,2,4], nums2 = [1,2,3,4] <br>
> **Output**: [4,3,-1]

## Brute Force
Nested loop: loop over `nums1` and then loop over `nums2` checking if there's a num in `nums2`
greater than *currNum1* after it's been found.

**Time: O(n\*m) <br> Space: O(1)**

## Optimal
We can use a `decreasing monotonic stack`. We loop over `nums2` pushing elements to the stack in
decreasing order. When we find an element that is not decreasing, then it is a greater element for
the *stack.peek()* (and potentially for some other elements in the stack too). Then, we can store
in a map the **stack.pop()** and **currNum**, i.e. a number and its next greater number. We keep
*popping* elements from the stack as long as they're smaller than *currNum*.

Finally, we loop over `nums1` and check if the *currNum* exists in the map. If so, it has a
*next greater element* and if it doesn't exist, then we add -1 to the answer.

E.g.

nums2 | 1 | 3 | 6 | 2 | 4
--- | --- | --- | --- | --- | ---
stack | 1 | 3 | 6 | 6,2 | 6,4
map | [] | [{1:3}] | [{1:3},{3,6}] | [{1:3},{3,6}] | [{1:3},{3,6},{2:4}]

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func nextGreaterElement(nums1 []int, nums2 []int) []int {
    nextGreaterMap := make(map[int]int)
    // monotonic decreasing stack
    stack := make([]int, 0, len(nums2))
    
    for _, num := range nums2 {
        // num is greater element for elements in the stack
        for len(stack) > 0 && stack[len(stack)-1] < num {
            nextGreaterMap[stack[len(stack)-1]] = num
            stack = stack[:len(stack)-1]
        }
        stack = append(stack, num)
    }
    
    // loop over nums1 and check if the nums have a next greater num
    ans := make([]int, len(nums1))
    for i, num := range nums1 {
        if val, found := nextGreaterMap[num]; found {
            ans[i] = val
        } else {
            ans[i] = -1
        }
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Stack<Integer> stack = new Stack();
        Map<Integer, Integer> nextGreaterMap = new HashMap();
        
        for(int num : nums2) {
            while(!stack.empty() && stack.peek() < num) {
                nextGreaterMap.put(stack.pop(), num);
            }
            stack.push(num);
        }
        int[] ans = new int[nums1.length];
        for(int i = 0; i < nums1.length; i++) {
            ans[i] = nextGreaterMap.getOrDefault(nums1[i], -1);
        }
        return ans;
    }
}
```