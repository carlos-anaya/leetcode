# 56. Merge Intervals (Medium)

Given a collection of `intervals`, **merge** all overlapping intervals.

- Example:
> **Input**: [[1,3],[2,5],[6,8],[9,15],[11,14],[15,18]] <br>
> **Output**: [[1,5],[6,8],[9,18]] <br>
> **Explanation**: [1,3] overlaps with [2,5]. Then [6,8] doesn't overlap. Last 3 intervals overlap.

## Optimal
Sort the intervals by start time and then loop over them. Use an *answer list* to keep track of the
merged intervals. For each interval check if it *"overlaps"* with the last merged *interval* by
checking if the *curr_interval.startTime < last_merged_interval.endTime*, if so, we need to merge
them, otherwise we just add *curr_interval* to the *answer list*.

**Time: O(nlogn) <br> Space: O(1)/O(n)** (Depending on the sorting algorithm)

### Go solution
```go
func merge(intervals [][]int) [][]int {
    ans := make([][]int, 0, len(intervals))
    
    // sort the intervals so earlier comes first
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][0] < intervals[j][0]
    })
    
    for _, interval := range intervals {
        lenAns := len(ans)
        // ans is empty or it ends before the start of next interval
        // add the interval to ans
        if lenAns == 0 || ans[lenAns-1][1] < interval[0] {
            ans = append(ans, interval)
        } else { 
            // next interval starts before/equal to last merged interval
            // merge it, by picking the latest end
            ans[lenAns-1][1] = max(ans[lenAns-1][1], interval[1])
        }
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public int[][] merge(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        List<int[]> ans = new ArrayList();
        
        for(int[] interval : intervals) {
            int a = ans.size();
            if(a == 0 || ans.get(a-1)[1] < interval[0]) {
                ans.add(interval);
            } else {
                ans.get(a-1)[1] = Math.max(ans.get(a-1)[1], interval[1]);
            }
        }
        return ans.toArray(new int[ans.size()][]);
    }
}
```