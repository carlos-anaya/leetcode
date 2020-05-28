# 338. Counting Bits (Medium)

Given a non negative integer number `num`. For every number `i` in the range **0 ≤ i ≤ num**
calculate the number of *1's* in their binary representation and return them as an array.

Do it in linear time (*O(n)*) and in a single pass.

- Example 1:
> **Input**: 2 <br>
> **Output**: [0,1,1] <br>
> **Explanation**:
- Example 2:
> **Input**: 5 <br>
> **Output**: [0,1,1,2,1,2] <br>
> **Explanation**:

## Approach I
We can build up re-using previous results. The binary representation from 0 and 1 is '0' and '1'.
Then, we add a one to the right to make 2 and 3, i.e. '10' and '11'. Then, to the last for results
we add a one to the right to make 4-7: '100', '101', '110', '111'. And so on, so we can just add 1
to the previous result in blocks that increase by powers of two.

**Time: O(n) <br> Space: O(n)**

```go
func countBits(num int) []int {
    ans := make([]int, num+1)
    pwr := 1
    for i := 1; i <= num; {
        for j := 0; j < pwr && i <= num; j++ {
            ans[i] = ans[j] + 1
            i++
        }
        pwr *= 2 // increase by powers of two
    }
    
    return ans
}
```

## Approach II
Similar to the approach above but instead of focusing on the MSB, we can focus on the LSB. Let's
consider 6 (110) and 7 (111), if we divide them by two (shift to the right) we have 3 (11) but we
have a remainder for 7!, so the answer for 6 is the same as 3 and the answer for 7 is the same as
3 plus one (remainder). So we have *ans[i] = ans[i/2] + i%2*.

**Time: O(n) <br> Space: O(n)**

```java
class Solution {
    public int[] countBits(int num) {
        int[] ans = new int[num+1];
        
        for(int i = 1; i <= num; i++)
            ans[i] = ans[i/2] + i%2;
        
        return ans;
    }
}
```