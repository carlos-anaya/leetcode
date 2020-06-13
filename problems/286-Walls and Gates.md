# 286. Walls and Gates (Medium)

You are given a *m x n* 2D grid initialized with these three possible values.

- `-1` - A wall or an obstacle.
- `0` - A gate.
- `INF` - Infinity means an empty room. We use the value 231 - 1 = 2147483647 to represent INF as 
  you may assume that the distance to a gate is less than 2147483647.

Fill each empty room with the distance to its **nearest** gate. If it is impossible to reach a gate,
it should be filled with INF.

- Example:
> **Input**: <br>
> ∞ -1  0 ∞ <br>
> ∞  ∞  ∞ -1 <br>
> ∞ -1  ∞ -1 <br>
> 0 -1  ∞ ∞ <br>
> **Output**: <br>
> 3 -1  0  1 <br>
> 2  2  1 -1 <br>
> 1 -1  2 -1 <br>
> 0 -1  3  4

## Brute Force
We can do a `DFS` traversal. In case we find a *gate* we do a recursion over the neighbors, adding
one to the current value. In case the room is out of bounds or it has been visited previously and
has a lower value, we skip the recursion.

**Time: O(m\*n) <br> Space: O(m\*n)**

```go
var directions [][]int
var n, m int

func wallsAndGates(rooms [][]int)  {
    if len(rooms) == 0 || len(rooms[0]) == 0 {
        return
    }
    directions = [][]int{{1,0},{-1,0},{0,1},{0,-1}}
    n = len(rooms)
    m = len(rooms[0])
        
    for i := 0; i < n; i++ {
        for j := 0; j < m; j++ {
            if rooms[i][j] == 0 {
                explore(rooms, i, j, 0);
            }
        }
    }
}

func explore(rooms [][]int, i,j, dist int) {
    // room out of bounds or already visited with a lower value
    if i < 0 || i >= n || j < 0 || j >= m || rooms[i][j] < dist {
        return
    }

    rooms[i][j] = dist;
    for _, dir := range directions {
        explore(rooms, i + dir[0], j + dir[1], dist + 1)
    }
}
```
## Optimal
We can avoid using a DFS traversal and re-visiting previously visited rooms by doing a `BFS`
traversal. We first find all the gates and add them to a **queue**. Then, for each one of those
we will be pushing the neighbors if they're in bounds and if they haven't been visited. Since this
is a BFS, if a node has been visited before, it means they're closer to other gate, so each room
gets processed at most once.

**Time: O(m\*n) <br> Space: O(m\*n)**

### Java solution
```java
class Solution {
    public void wallsAndGates(int[][] rooms) {
        if(rooms.length == 0 || rooms[0].length == 0)
            return;
        
        int[][] directions = new int[][]{{1,0},{-1,0},{0,1},{0,-1}};
        int n = rooms.length, m = rooms[0].length;
        Queue<int[]> q = new LinkedList();
        
        // fill the queue with the gates
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < m; j++) {
                if(rooms[i][j] == 0) {
                    q.add(new int[]{i, j, 0});
                }
            }
        }
        
        while(!q.isEmpty()) {
            int[] curr = q.poll();
            int row = curr[0], col = curr[1], dist = curr[2] + 1;

            for(int[] dir : directions) {
                int newR = row + dir[0], newC = col + dir[1];
                if(newR < 0 || newR >= n || newC < 0 || newC >= m || rooms[newR][newC] <= dist)
                    continue;

                rooms[newR][newC] = dist;
                q.add(new int[]{newR, newC, dist});
            }
        }
    }
}
```