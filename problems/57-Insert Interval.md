# 57. Insert Interval (Hard)

Given a set of **non-overlapping** `intervals`, insert a `new interval` into the intervals (merge if
necessary).

You may assume that the intervals were initially sorted according to their start times.

- Example 1:
> **Input**: intervals = [[1,3],[6,9]], newInterval = [2,5] <br>
> **Output**: [[1,5],[6,9]] <br>
> **Explanation**:
- Example 2:
> **Input**: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8] <br>
> **Output**: [[1,2],[3,10],[12,16]] <br>
> **Explanation**: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].

## Optimal
We can follow the same approach as in [56-Merge Intervals](56-Merge%20Intervals.md) for merging
overlapping intervals. Furthermore the array is already sorted in ascending way, so the only concern
is where to put `newInterval`.

We can loop over the *intervals* and when we have an interval that starts before *newInterval* just
merge it as usual. Otherwise, *newInterval* should be merged before the current interval. We can
keep track if the *newInterval* has been already merged with a *boolean*, so we don't try to merge
it multiple times and also we can check at the end if it was merged or not.

**Time: O(n) <br> Space: O(n)** (the size of the output)

### Go solution
```go
func insert(intervals [][]int, newInterval []int) [][]int {
    ans := make([][]int, 0, len(intervals) + 1)
    isNewMerged := false
    
    for _, interval := range intervals {
        // found the place to insert the new interval
        if(!isNewMerged && interval[0] > newInterval[0]) {
            ans = merge(ans, newInterval)
            isNewMerged = true
        }
        ans = merge(ans, interval)
    }
    // new interval wasn't merged so it is at the end
    if !isNewMerged {
        ans = merge(ans, newInterval)
    }
    return ans
}
// merge or append the 'interval' to the ans list
func merge(ans [][]int, interval []int) [][]int {
    a := len(ans)
    // empty list or no overlap
    if a == 0 || ans[a-1][1] < interval[0] {
        ans = append(ans, interval)
    } else if interval[1] > ans[a-1][1] { // overlap, choose the greatest end
        ans[a-1][1] = interval[1]
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public int[][] insert(int[][] intervals, int[] newInterval) {
        List<int[]> ans = new ArrayList();
        boolean newMerged = false;
                
        for(int[] interval : intervals) {
            // found place to insert newInterval (before the current interval)
            if(!newMerged && interval[0] > newInterval[0]) {
                merge(ans, newInterval);
                newMerged = true;
            }
            merge(ans, interval);
        }
        // didn't merge newInterval, so it is the last one
        if(!newMerged)
            merge(ans, newInterval);
        
        return ans.toArray(new int[ans.size()][]);
    }
    // merges or appends interval to the ans list
    private void merge(List<int[]> ans, int[] interval) {
        int a = ans.size();
        // if list is empty or there's no overlap just add the interval
        if(a == 0 || ans.get(a-1)[1] < interval[0]) {
            ans.add(interval);
        } else { // merge last merged interval with the current interval
            ans.get(a-1)[1] = Math.max(ans.get(a-1)[1], interval[1]);
        }
    }
}
```