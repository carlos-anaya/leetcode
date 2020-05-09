# 1232. Check If It Is a Straight Line (Easy)

You are given an array of coordinates, `coordinates[i] = [x, y]`, where `[x, y]` represents the
coordinate of a point. Check if these points make a straight line in the *XY* plane.

- Example 1:
> **Input**: [[1,2],[2,3],[3,4],[4,5],[5,6],[6,7]] <br>
> **Output**: true <br>
![alt example1](https://assets.leetcode.com/uploads/2019/10/15/untitled-diagram-2.jpg)
- Example 2:
> **Input**: [[1,1],[2,2],[3,4],[4,5],[5,6],[7,7]] <br>
> **Output**: false <br>
![alt example2](https://assets.leetcode.com/uploads/2019/10/09/untitled-diagram-1.jpg)

## Optimal
We need at least **2 points** to make a *line*, but how do we check if a *third point* is also part
of the line defined by the first two points?

We can use the *slope* (inclination) of the line defined by *(x1, y1)* and *(x2, y2)*, and then
compare the *slope* of the line defined by points *(x1, y1)* and *(x3, y3)*, and so on. All the 
*slope values* should be the same. I.e.
- the slope of a line is defined by **(y2-y1)/(x2-x1)**
- then, to compare a third point we have **(yⁱ-y1)/(xⁱ-x1) == (y2-y1)/(x2-x1)**
- we can define *dx=(x2-x1)* and *dy=(y2-y1)*, so we have **(yⁱ-y1)/(xⁱ-x1) == dy/dx**
- finally, to avoind *division by 0* we have:  **(yⁱ-y1)\*dx == dy\*(xⁱ-x1)**

We just need to loop from *i=2 to n* and verify all the slopes are equal.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func checkStraightLine(coordinates [][]int) bool {
    if len(coordinates) < 2 {
        return false
    }
    
    x1, y1 := coordinates[0][0], coordinates[0][1]
    dx, dy := coordinates[1][0] - x1, coordinates[1][1] - y1
    
    for i := 2; i < len(coordinates); i++ {
        x, y := coordinates[i][0], coordinates[i][1]
        if (y-y1) * dx != dy * (x-x1) {
            return false
        }
    }
    return true
}
```
### Java solution
```java
class Solution {
    public boolean checkStraightLine(int[][] coordinates) {
        if(coordinates.length < 2)
            return false;
        
        int x1 = coordinates[0][0], y1 = coordinates[0][1];
        int dx = coordinates[1][0] - x1, dy = coordinates[1][1] - y1;
        
        for(int i = 2; i < coordinates.length; i++) {
            int x = coordinates[i][0], y = coordinates[i][1];
            if((y-y1)*dx != dy*(x-x1))
                return false;
        }
        return true;
    }
}
```