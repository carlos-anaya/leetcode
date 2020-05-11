# 733. Flood Fill (Easy)

An `image` is represented by a 2-D array of integers, each integer representing the *pixel value* of
the image (from 0 to 65535).

Given a coordinate `(sr, sc)` representing the starting pixel (row and column) of the flood fill,
and a pixel value `newColor`, *"flood fill"* the image.

To perform a *"flood fill"*, consider the starting pixel, plus any pixels connected 4-directionally
to the starting pixel of the same color as the starting pixel, plus any pixels connected
4-directionally to those pixels (also with the same color as the starting pixel), and so on. Replace
the color of all of the aforementioned pixels with the newColor. Return the *modified image*.

- Example 1:
> **Input**: sr = 1, sc = 1, newColor = 2, image= <br>
> [1,1,1] <br>
> [0,1,0] <br>
> [1,0,1] <br>
> **Output**: image = <br>
> [2,2,2] <br>
> [0,2,0] <br>
> [1,0,1] <br>

## Optimal
We do a regular *DFS* traversal, checking if the next pixel is off-limits of the image, or if it
has already been updated or if it has a different color than the source. Then, we update it and
check it's neighbors.

We can follow the same idea with a *Stack* instead of a recursive approach.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
// use a struct to avoid copies of data and keep the dfs func more readable
type Metadata struct {
    image [][]int
    colorToChange int
    newColor int
    n int
    m int
}
func floodFill(image [][]int, sr int, sc int, newColor int) [][]int {
    metadata := &Metadata{image, image[sr][sc], newColor, len(image), len(image[0])}
    dfs(metadata, sr, sc)
    return image
}

func dfs(metadata *Metadata, x, y int) {
    if(x < 0 || x >= metadata.n || y < 0 || y >= metadata.m || // off limits
       metadata.image[x][y] == metadata.newColor || // already new color (corner case)
       metadata.image[x][y] != metadata.colorToChange) { // different from color to change
        return
    }
    metadata.image[x][y] = metadata.newColor
    dfs(metadata, x-1, y)
    dfs(metadata, x+1, y)
    dfs(metadata, x, y-1)
    dfs(metadata, x, y+1)
}
```
### Java solution
```java
class Solution {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int baseColor = image[sr][sc];
        if(baseColor == newColor) // corner case
            return image;
        
        int n = image.length, m = image[0].length;
        Stack<int[]> pixels = new Stack();
        pixels.push(new int[]{sr, sc});
        
        while(!pixels.empty()) {
            int x = pixels.peek()[0];
            int y = pixels.peek()[1];
            pixels.pop();
            if(image[x][y] != baseColor) // different color or already colored
                continue;
            
            image[x][y] = newColor;
            if(x > 0) pixels.push(new int[]{x-1, y});
            if(x < n-1) pixels.push(new int[]{x+1, y});
            if(y > 0) pixels.push(new int[]{x, y-1});
            if(y < m-1) pixels.push(new int[]{x, y+1});
        }
        
        return image;
    }
}
```