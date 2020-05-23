# 556. Next Greater Element III (Medium)

Given a positive **32-bit integer** `n`, you need to find the **smallest** 32-bit integer which has
exactly the same digits existing in the integer `n` and is greater in value than `n`. If no such 
positive 32-bit integer exists, you need to return *-1*.

- Example 1:
> **Input**: 12 <br>
> **Output**: 21
- Example 2:
> **Input**: 21 <br>
> **Output**: -1

## Optimal
Lets consider some other examples first:

- 1**2**3 -> 132
- **1**32 -> 213
- 2**1**3 -> 231
- **2**31 -> 312
- 3**1**2 -> 321
- 321 -> -1
- 18**3**421 -> 184123 
- **1**999999999 -> -1 (this produces 9199999999 which overflows int32)

Some observations:

- If we have a strictly decreasing number from left to right there's no possible solution. E.g. 321
- When an increasing number is found, we can swap at that index to get a bigger number. E.g. 1**2**3
  (and other **bold** numbers in the examples)
- We need to find the last *idx* to swap, from left to right. In other words, the first from right
  to left that is a decreasing char. E.g. 18**3**421.
- When we find the *idx* to swap, we need to find the smallest digit bigger than *num[idx]*.
- Subsequent numbers have to be "sorted" from min to max.
- Digits to the left of *idx* (if any) remain the same.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func nextGreaterElement(n int) int {
    num := []rune(strconv.Itoa(n))
    lenN, breakIdx := len(num), -1
    ans := make([]rune, lenN)
    
    // find breakIdx
    for i := lenN - 2; i >= 0; i-- {
        if num[i] < num[i+1] {
            breakIdx = i
            break
        }
    }
    // no solution - monotonic decreasing number
    if breakIdx == -1 { 
        return -1
    }
    // keep elements to the left of breakIdx in same order
    for i := 0; i < breakIdx; i++ {
        ans[i] = rune(num[i])
    }
    // fill buckets and find the next greater min
    buckets := make([]int, 10)
    min := '9'
    for i := breakIdx; i < lenN; i++ {
        curr := num[i]
        if num[i] > num[breakIdx] && rune(curr) < min {
            min = rune(curr)
        }
        buckets[curr-'0']++
    }
    // add the next greater min element at breakIdx
    ans[breakIdx] = min
    buckets[min-'0']--
    breakIdx++
    // loop over the buckets and append the nums to the ans
    // from smaller to bigger
    for i := 0; i <= 9; i++ {
        for j := 0; j < buckets[i]; j++ {
            ans[breakIdx] = rune(i + '0')
            breakIdx++
        }
    } 
    // try to parse into an "int", return -1 if it overflows
    if i, err := strconv.ParseInt(string(ans), 10, 32); err == nil {
        return int(i)
    } else {
        return -1
    }
}
```
### Java solution
```java
class Solution {
    public int nextGreaterElement(int n) {
        String num = Integer.toString(n);
        Stack<Character> stack = new Stack();
        int breakIdx = -1;
        // find break index from right to left, first num not increasing
        for(int i = num.length() - 2; i >= 0 ; i--) {
            if(num.charAt(i+1) > num.charAt(i)) {
                breakIdx = i;
                break;
            }
        }
        // monotonic decreasing number so no solution
        if(breakIdx == -1)
            return -1;
        // elements to the left of breakIdx remain the same
        StringBuilder sb = new StringBuilder();
        for(int i = 0; i < breakIdx; i++) {
            sb.append(num.charAt(i));
        }
        // bucket sort the elements to the right of breakIdx
        // find the min digit greater than num[idx]
        int[] numCount = new int[10];
        char min = '9';
        for(int i = breakIdx; i < num.length(); i++) {
            char c = num.charAt(i);
            numCount[c-'0']++;
            
            if(c > num.charAt(breakIdx) && c < min)
                min = c;
        }
        // take digits from the buckets to build answer
        numCount[min-'0']--;
        sb.append(min);
        for(int i = 0; i < 10; i++) {
            for(int j = 0; j < numCount[i]; j++) {
                sb.append((char)(i+'0'));
            }
        }
        // res might overflow the result
        long res = Long.parseLong(sb.toString());
        if(res > Integer.MAX_VALUE)
            return -1;
        return (int)res;
    }
}
```