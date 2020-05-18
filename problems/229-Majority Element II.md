# 229. Majority Element II (Medium)

Given an integer array of size `n`, find all elements that appear more than `n / 3` times.

**Note**: The algorithm should run in linear time and in O(1) space.

- Example 1:
> **Input**: [3,2,3] <br>
> **Output**: [3]
- Example 2:
> **Input**: [1,1,1,3,3,2,2,2] <br>
> **Output**: [1,2]

## Brute Force
Nested loops. Loop over the numbers and for each one count the occurrences; if the number appears
more than *n / 3* times, it is an answer.

**Time: O(n²) <br> Space: O(1)**

## Optimal - Boyer-Moore algorithm
This is similar to the problem [169. Majority Element](169-Majority%20Element.md) but now we have
at most *two* majority elements. *But, why?* Because the elements have to appear more than `n / 3`
times. So, at most two numbers can satisfy that requirement.

The algorithm an *extension* of the *Boyer-Moore algorithm*. This time we take **two candidates**.
Now, an additional step is required, once we have the two candidates we need to ensure they appear
more than `n / 3` times. Consider this sequence: *"111155599"*, we have *[{1:4},{5:3},{9:2}]*.
The candidates will be *1* and *5*, however *5* appears 3 times, so it doesn't meet the condition.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func majorityElement(nums []int) []int {
    // use two candidates, initialize them with different values
    // the values don't matter because the count == 0
    cand1, occCand1 := 1, 0
    cand2, occCand2 := -1, 0
    // find two candidates by Boyer-Moore Majority Vote algorithm
    for _, num := range nums {
        if cand1 == num { 
            occCand1++
        } else if cand2 == num {
            occCand2++
        } else if occCand1 == 0 {
            occCand1 = 1
            cand1 = num
        } else if occCand2 == 0 {
            occCand2 = 1
            cand2 = num
        } else {
            occCand1--
            occCand2--
        }
    }
    // count the total occurrences of each candidate
    occCand1, occCand2 = 0, 0
    for _, num := range nums {
        if cand1 == num { 
            occCand1++
        } else if cand2 == num {
            occCand2++
        }
    }
    // ensure the candidates appear more than n/3 times
    n := len(nums) / 3
    ans := make([]int, 0, 2)
    if occCand1 > n { ans = append(ans, cand1) }
    if occCand2 > n { ans = append(ans, cand2) }
    
    return ans 
}
```
### Java solution
```java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        int cand1 = 1, count1 = 0;
        int cand2 = -1, count2 = 0;
        // find two candidates
        for(int num : nums) {
            if(num == cand1)
                count1++;
            else if(num == cand2)
                count2++;
            else if(count1 == 0) {
                cand1 = num; 
                count1++;
            } else if(count2 == 0) {
                cand2 = num;
                count2++;
            } else {
                count1--;
                count2--;
            }
        }
        
        // count total occurrences of each candidate
        count1 = 0;
        count2 = 0;
        for(int num : nums) {
            if(num == cand1)
                count1++;
            else if(num == cand2)
                count2++;
        }
        
        // ensure the candidates appear more than n/3 times
        int n = nums.length / 3;
        List<Integer> ans = new ArrayList();
        if(count1 > n)
            ans.add(cand1);
        if(count2 > n)
            ans.add(cand2);
        
        return ans;
    }
}
```