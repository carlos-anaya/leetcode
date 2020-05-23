# 986. Interval List Intersections (Medium)

Given two lists of **closed** intervals, each list of intervals is pairwise *disjoint* and in
*sorted order*.

Return the **intersection** of these two interval lists.

**Note**

- Formally, a closed interval `[a, b]` (with `a <= b`) denotes the set of real numbers `x` with 
  `a <= x <= b`.  The intersection of two closed intervals is a set of real numbers that is either
  empty, or can be represented as a closed interval.  For example, the intersection of [1, 3] and 
  [2, 4] is [2, 3].)

- Example 1:
> **Input**: <br> A = [[0,2],[5,10],[13,23],[24,25]] <br> B = [[1,5],[8,12],[15,24],[25,26]]
> **Output**: [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]] <br>
> **Explanation**: See image below

![interval example](https://assets.leetcode.com/uploads/2019/01/30/interval1.png)

## Optimal
Use a `two pointer` approach. Each time we we find the interval that **intersects** *interval1* and
*interval2* by getting the **max start** and the **min end** between both intervals. If at any time
we find that *intersectStart > intersectEnd*, then there's no real intersect because the start of
one interval is after the end of the other interval.

Finally, we skip the interval that ends first.

**Time: O(n + m) <br> Space: O(n +M)** (size of the output answer)

### Go solution
```go
func intervalIntersection(A [][]int, B [][]int) [][]int {
    lenA, lenB := len(A), len(B)
    idxA, idxB := 0, 0
    ans := make([][]int, 0, lenA+lenB)
    
    for idxA < lenA && idxB < lenB {
        s1, e1 := A[idxA][0], A[idxA][1]
        s2, e2 := B[idxB][0], B[idxB][1]
        
        // find intersection
        s3, e3 := max(s1, s2), min(e1, e2)
        
        // e3 has to be bigger than s3, otherwise there's no intersection
        if s3 <= e3 {
            ans = append(ans, []int{s3, e3})
        }
        
        if e1 < e2 { // e1 ends first, so skip it
            idxA++
        } else if e1 > e2 { // e2 ends first, so skip it
            idxB++
        } else { //both end at same point, so skip both
            idxB++
            idxA++
        }
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public int[][] intervalIntersection(int[][] A, int[][] B) {
        int lenA = A.length, lenB = B.length;
        int idxA = 0, idxB = 0;
        List<int[]> ans = new ArrayList();
        
        while(idxA < lenA && idxB < lenB) {
            int s1 = A[idxA][0], e1 = A[idxA][1];
            int s2 = B[idxB][0], e2 = B[idxB][1];
            // find intersection
            int s3 = Math.max(s1, s2), e3 = Math.min(e1, e2);
            // if s3 > e3 there's no intersection
            if(s3 <= e3)
                ans.add(new int[]{s3, e3});
            
            // skip interval that ends first
            if(e1 > e2)
                idxB++;
            else if(e2 > e1)
                idxA++;
            else {
                idxA++;
                idxB++;
            }
        }
        return ans.toArray(new int[ans.size()][]);
    }
}
```