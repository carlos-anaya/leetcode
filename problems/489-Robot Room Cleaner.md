# 489. Robot Room Cleaner (Hard)

Given a robot cleaner in a room modeled as a grid. Each cell in the grid can be empty or blocked.

The robot cleaner with 4 given APIs can `move` forward, `turn left` or `turn right`. Each turn it
made is *90 degrees*.

When it tries to move into a blocked cell, its bumper sensor detects the obstacle and it stays on 
the current cell.

Design an algorithm to clean the entire room using only the 4 given APIs shown below.

``` java
interface Robot {
  // returns true if next cell is open and robot moves into the cell.
  // returns false if next cell is obstacle and robot stays on the current cell.
  boolean move();

  // Robot will stay on the same cell after calling turnLeft/turnRight.
  // Each turn will be 90 degrees.
  void turnLeft();
  void turnRight();

  // Clean the current cell.
  void clean();
}
```

- Example:
**Input**: row = 1, col = 3, room = 

1 | 1 | 1 | 1 | 1 | 0 | 1 | 1
1 | 1 | 1 | 1 | 1 | 0 | 1 | 1
1 | 0 | 1 | 1 | 1 | 1 | 1 | 1
0 | 0 | 0 | 1 | 0 | 0 | 0 | 0
1 | 1 | 1 | 1 | 1 | 1 | 1 | 1

**Explanation**: <br>
All grids in the room are marked by either 0 or 1. <br>
0 means the cell is blocked, while 1 means the cell is accessible. <br>
The robot initially starts at the position of row=1, col=3. <br>
From the top left corner, its position is one row below and three columns right.

## Optimal
Use a `backtracking DFS` approach. Move the robot in a *clockwise* manner, and marking cells as
*visited* as we clean them. Whenever there's an obstacle (robot can't move) or a cell has been
visited then move to the right and try that path. 

We need to keep track of the row, column and direction in each backtrack call, so we can maintain
the same direction and in case there are no more movements we can backtrack. But how do we
backtrack? We need to return the robot to the previous cell and leave it in the same direction, thus
we rotate 180 degrees, move and then rotate again 180 degrees.

![alt room](https://leetcode.com/problems/robot-room-cleaner/Figures/489/489_implementation.png)

**Time: O(n) <brSpace: O(n)**

### Go solution
```go
/**
 * // This is the robot's control interface.
 * // You should not implement it, or speculate about its implementation
 * type Robot struct {
 * }
 * 
 * // Returns true if the cell in front is open and robot moves into the cell.
 * // Returns false if the cell in front is blocked and robot stays in the current cell.
 * func (robot *Robot) Move() bool {}
 *
 * // Robot will stay in the same cell after calling TurnLeft/TurnRight.
 * // Each turn will be 90 degrees.
 * func (robot *Robot) TurnLeft() {}
 * func (robot *Robot) TurnRight() {}
 *
 * // Clean the current cell.
 * func (robot *Robot) Clean() {}
 */

// going clockwise, 0-up, 1-right, 2-down, 3-left
var dirs [][]int = [][]int{{-1,0},{0,1},{1,0},{0,-1}}
var visited map[int]bool
var roboto *Robot

func cleanRoom(robot *Robot) {
    roboto = robot
    visited = make(map[int]bool)
    backtrack(0,0,0)
}

func backtrack(row, col, dir int) {
    // mark this row/col as visited and clean the cell
    visited[getKey(row, col)] = true
    roboto.Clean()
    
    // check 4 paths for backtrack
    // need to start given the current direction, so use (dir + i) % 4
    for i := 0; i < 4; i++ {
        newD := (dir + i) % 4
        newR := row + dirs[newD][0]
        newC := col + dirs[newD][1]
        
        // next cell is not visited and there's no obstacle
        if _, found := visited[getKey(newR, newC)]; !found && roboto.Move() {
            backtrack(newR, newC, newD)
            goBack()
        }
        // move clockwise
        roboto.TurnRight()
    }
}

// robot already moved once to the right, then we rotate 180 degrees,
// move back and then rotate 180 degrees to start in same position/direction
func goBack() {
    roboto.TurnRight()
    roboto.TurnRight()
    roboto.Move()
    roboto.TurnLeft()
    roboto.TurnLeft()
}

func getKey(a, b int) int {
    return a * 10000 + b
    // can use a string as well for key, but is faster with an int
    // return strconv.Itoa(a) + "," + strconv.Itoa(b)
}
```
