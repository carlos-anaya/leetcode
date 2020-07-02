# 441. Arranging Coins (Easy)

You have a total of `n` coins that you want to form in a staircase shape, where every `k-th` row
must have exactly `k` coins.

Given `n`, find the total number of **full** staircase rows that can be formed.

`n` is a non-negative integer and fits within the range of a 32-bit signed integer

- Example 1:
> **Input**: n = 5 <br>
> **Output**: 2 <br>
> **Explanation**: The coins can form the following rows: <br>
> ¤ <br>
> ¤ ¤ <br>
> ¤ ¤
- Example 2:
> **Input**: n = 11 <br>
> **Output**: 4 <br>
> **Explanation**: The coins can form the following rows: <br>
> ¤ <br>
> ¤ ¤ <br>
> ¤ ¤ ¤ <br>
> ¤ ¤ ¤ ¤

## Optimal
Use a `binary search` approach. We know that the total number of elements for `k` rows is *k(k+1)/2*
then, we can try to find the max value of `k` that is smaller than `n`.

**Time: O(log n) <br> Space: O(1)**

### Java solution
```java
class Solution {
    public int arrangeCoins(int n) {
        int l = 0, r = n;
        while(r >= l) {
            int k = (r - l) / 2 + l;
            long res = (long) k * (k + 1) / 2; // use long to avoid int32 overflow
            if(res == n)
                return k;
            else if(res > n)
                r = k - 1;
            else
                l = k + 1;
        }
        return r;
    }
}
```
