# 317. Shortest Distance from All Buildings (Hard)

You want to build a house on an *empty* land which reaches *all buildings* in the *shortest* amount
of distance. You can only move up, down, left and right. You are given a 2D grid of values
**0, 1 or 2**, where:

- Each **0** marks an empty land which you can pass by freely.
- Each **1** marks a building which you cannot pass through.
- Each **2** marks an obstacle which you cannot pass through.

- Example:
> **Input**: [[1,0,2,0,1],[0,0,0,0,0],[0,0,1,0,0]] <br>
> **Output**: 7 <br>
> **Explanation**: Given three buildings at (0,0), (0,4), (2,2), and an obstacle at (0,2), the point
> (1,2) is an ideal empty land to build a house, as the total travel distance of 3+3+1=7 is minimal.

    1 - 0 - 2 - 0 - 1
    |   |   |   |   |
    0 - 0 - 0 - 0 - 0
    |   |   |   |   |
    0 - 0 - 1 - 0 - 0

## Optimal
For this problem we need to do a `BFS` traversal from each building to the "*empty lands*", updating
the distances taken from this building. We use a `dp matrix` to keep track of the cumulative 
distance to each land from all the buildings. E.g., starting from building {0,0}, the `dp matrix` 
looks like that after the BFS:

    B - 1 - O - 5 - B
    |   |   |   |   |
    1 - 2 - 3 - 4 - 5
    |   |   |   |   |
    2 - 3 - B - 5 - 6

After processing the three buildings we have the following `dp matrix`:

     B - 9 - O - 9 - B
     |   |   |   |   |
     9 - 8 - 7 - 8 - 9
     |   |   |   |   |
    10 - 9 - B - 9 - 10

Finally, we can find the minimum value in the `dp matrix`. One point to consider is that we need to
keep track of the number of buildings reachable from each space, that is a `reach matrix` so we can
ensure that the space at *{i,j}* is reachable by from all the buildings. (there might be some
obstacles that would not allow to reach a given building, so the `dp[i][j]` will be minimal, but the
`reach[i][j] < total_buildings`, so that is an invalid value).

**Time: O(n²\*m²) <br> Space: O(n\*m)**

### Java solution
```java
class Solution {
    class Data {
        int[][] dirs  = new int[][]{{1,0},{-1,0},{0,1},{0,-1}};
        int[][] grid, dp, reach;
        int n, m;
        
        Data(int[][] grid) {
            this.grid = grid;
            this.n = grid.length;
            this.m = grid[0].length;
            this.dp = new int[this.n][this.m]; // hold the sum of distances of empty land
            this.reach = new int[this.n][this.m];// hold the number of buildings reachable from here
        }
    }
    
    public int shortestDistance(int[][] grid) {
        if(grid.length == 0 || grid[0].length == 0)
            return -1;
        // fill data with required fields, so we avoid multiple parameters in function
        Data data = new Data(grid);
        
        int buildings = 0; // total number of buildings
        // loop over the grid, for each building we do a BFS traversal
        for(int i = 0; i < data.n; i++) {
            for(int j = 0; j < data.m; j++) {
                if(grid[i][j] == 1) {
                    bfs(data, i, j);
                    buildings++;
                }
            }
        }
        // loop over dp matrix. solution is minimal distance that
        // can be reached by all the buildings
        int min = Integer.MAX_VALUE;
        for(int i = 0; i < data.n; i++) {
            for(int j = 0; j < data.m; j++) {
                if(data.reach[i][j] == buildings)
                    min = Math.min(min, data.dp[i][j]);
            }
        }
        return min == Integer.MAX_VALUE ? -1 : min;
    }
    
    private void bfs(Data data, int r, int c) {
        // keep track of previously visited lands
        boolean[][] visited= new boolean[data.n][data.m];
        Queue<int[]> q = new LinkedList();
        q.add(new int[]{r, c});
        visited[r][c] = true;
        int dist = 0;
        
        // BFS traversal
        while(!q.isEmpty()) {
            int size = q.size();
            // poll the neighbors that have the same distance from previous
            for(int i = 0; i < size; i++) {
                int[] curr = q.poll();
                for(int j = 0; j < 4; j++) {
                    int newR = curr[0] + data.dirs[j][0];
                    int newC = curr[1] + data.dirs[j][1];
                    
                    // coordinates should be in boundaries, the spot should not have been
                    // visited and it has to be an empty land 
                    if(newR < 0 || newR >= data.n || newC < 0 || newC >= data.m 
                       || visited[newR][newC] || data.grid[newR][newC] != 0)
                        continue;
                    // add the new distance to dp
                    data.dp[newR][newC] += dist + 1;
                    // update reach, because a new building is reachable from here
                    data.reach[newR][newC]++;
                    // mark this spot as visited and add it to the queue
                    visited[newR][newC] = true;
                    q.add(new int[]{newR, newC});
                }
            }
            dist++;
        }
    }
}
```