# 1066. Campus Bikes II (Medium)

On a campus represented as a 2D grid, there are `N` *workers* and `M` *bikes*, with `N` <= `M`. 
Each worker and bike is a 2D coordinate on this grid.

We assign one unique bike to each worker so that the sum of the Manhattan distances between each 
worker and their assigned bike is minimized.

The Manhattan distance between two points `p1` and `p2` is 
`Manhattan(p1, p2) = |p1.x - p2.x| + |p1.y - p2.y|`.

Return the **minimum** possible sum of Manhattan distances between each worker and their assigned
bike.

- Example 1:
> **Input**: workers = [[0,0],[2,1]], bikes = [[1,2],[3,3]] <br>
> **Output**: 6 <br>
> **Explanation**: We assign bike 0 to worker 0, bike 1 to worker 1. The Manhattan distance of both
> assignments is 3, so the output is 6.

![alt grid1](https://assets.leetcode.com/uploads/2019/03/06/1261_example_1_v2.png)

- Example 2:
> **Input**: workers = [[0,0],[1,1],[2,0]], bikes = [[1,0],[2,2],[2,1]] <br>
> **Output**: 4 <br>
> **Explanation**: We first assign bike 0 to worker 0, then assign bike 1 to worker 1 or worker 2,
> bike 2 to worker 2 or worker 1. Both assignments lead to sum of the Manhattan distances as 4.

![alt grid2](https://assets.leetcode.com/uploads/2019/03/06/1261_example_2_v2.png)

## Brute Force
We do a plain recursive approach with backtracking. For each of the *workers* we try assigning the
*bikes* and we keep track of the minimum value once all the workers have a bike. 

To optimize a bit we add a validation to skip combinations if they have a distance higher than the
min distance so far.

**Time: O(n²) <br> Space: O(1)**

## Java solution
```java
class Solution {
    int min;
    public int assignBikes(int[][] workers, int[][] bikes) {
        min = Integer.MAX_VALUE;
        dfs(workers, 0, bikes, new boolean[bikes.length + 1], 0);
        
        return min;
    }
    
    private void dfs(int[][] workers, int w, int[][] bikes, boolean[] taken, int dist) {
        // base case, all the workers have been assigned a bike
        if(w >= workers.length) {
            min = Math.min(min, dist);
            return;
        }
        
        // skip the path if the distance is already greater than minimum
        if(dist > min)
            return;
        
        // assign bikes to the worker and backtrack to check if this combination generates a min
        for(int i = 0; i < bikes.length; i++) {
            if(taken[i]) // bike already assigned to another worker
                continue;
            taken[i] = true;
            dfs(workers, w+1, bikes, taken, dist + getDist(workers[w], bikes[i]));
            taken[i] = false;
        }
    }
    
    private int getDist(int[] w, int[] b) {
        return Math.abs(w[0]-b[0]) + Math.abs(w[1]-b[1]);
    }
}
```