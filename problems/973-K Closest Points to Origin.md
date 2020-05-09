# 973. K Closest Points to Origin (Medium)

We have a list of `points` on the plane.  Find the `K` closest points to the `origin` (0, 0).

(Here, the distance between two points on a plane is the Euclidean distance.)

You may return the answer in any order.

- Example 1:
> **Input**: points = [[1,3],[-2,2]], K = 1 <br>
> **Output**: [[-2,2]] <br>
> **Explanation**: The distance between (1, 3) and the origin is sqrt(10). <br>
> The distance between (-2, 2) and the origin is sqrt(8). <br>
> Since sqrt(8) < sqrt(10), (-2, 2) is closer to the origin.
- Example 2:
> **Input**: points = [[8,0],[3,3],[5,-1],[-2,4]], K = 2 <br>
> **Output**: [[3,3],[-2,4]] <br>
> **Explanation**: (The answer [[-2,4],[3,3]] would also be accepted.)

## Brute Force
Create an array of distances and sort the distances, then just take the K first distances and return
those points. This is a quick, easy solution but it only works *off-line*, and not for 
*stream inputs* since we need to know the whole set of points for sorting.

**Time: O(n\*logn) <br> Space: O(n)**

## Approach I - Quick Sort
We can use a *quick-sort* style of sorting. Quick sort is not *stable* and the output array is not
in order, which is fine since it's not a constraint. We need to find the **pivot at K position** to
ensure the K points with min-distance are to the left of the *pivot*.

We can do a classic quick-sort and check the final value of *i*. If *i == K* return. If *i < K*, we
we need more elements, so we try again, starting from *i+1* to *r*. Finally, if *i > K*, we need to
try again from *l* to *i-1*.

```go
type Point struct {
    x, y, dist int
}

func NewPoint(x, y int) Point {
    // distance is sqrt((x2-x1)² + (y2-y1)²)
    // since x1,y1 == 0, we can spare the sqrt
    return Point{x, y, x*x+y*y}
}

func kClosest(points [][]int, K int) [][]int {
    pArr := make([]Point, len(points))
    for i, p := range points {
        pArr[i] = NewPoint(p[0], p[1])
    }
    
    // use quickSort trying to put the K points with
    // smallest distance to the left of the array
    quickSort(&pArr, 0, len(pArr) - 1, K)
    
    // make array of size K and take K points
    ans := make([][]int, K)
    for i := 0; i < K; i++ {
        ans[i] = []int{pArr[i].x, pArr[i].y}
    }
    return ans
}

func quickSort(points *[]Point, l, r, K int) {
    // base case, nothing to sort
    if l >= r {
        return
    }
    // take pivot as last element of sub-array
    i, j, pivot := l-1, l, (*points)[r]
    for ; j <= r; j++ {
        p := (*points)[j]
        if p.dist <= pivot.dist {
            i++
            swap(points, i, j);
        }
    }
    if i == K { // found the K closest points
        return
    } else if i > K { // need to narrow down the points
        quickSort(points, l, i-1, K)
    } else { // need more points
        quickSort(points, i+1, r, K)
    }
}

func swap(points *[]Point, p1, p2 int) {
    (*points)[p1], (*points)[p2] = (*points)[p2], (*points)[p1]
}
```
**Time: O(n²)** (in average this should be closer to O(n\*logn)<br> 
**Space: O(n)** (we can spare the []Points arr by swapping directly the [][]points arr)

## Approach II - MinHeap / Priority Queue
Use a *min heap* of *capacity K* to store the points based on their distance. Then just poll points.
This is a great approach for *"streaming input points"* since the heap guarantees to have the
*K min points* at any given time and it doesn't need to keep track of previous points. Addidionally,
the output array is sorted.

**Time: O(n\*logk) <br> Space: O(k)**

### Java solution
```java
class Solution {
    class Point {
        int x;
        int y;
        int dist;
        
        Point(int x, int y) {
            this.x = x;
            this.y = y;
            this.dist = x * x + y * y;
        }
        
        int getDistToOrigin() {
            return this.dist;
        }
    }
    public int[][] kClosest(int[][] points, int K) {
        PriorityQueue<Point> minHeap = new PriorityQueue<Point>(K,
            (p1, p2) -> Integer.compare(p1.getDistToOrigin(), p2.getDistToOrigin()));
        
        for(int[] p : points) {
            Point point = new Point(p[0], p[1]);
            minHeap.offer(point);
        }
        
        int[][] ans = new int[K][];
        for(int i = 0; i < K; i++) {
            Point point = minHeap.poll();
            ans[i] = new int[] {point.x, point.y};
        }
        return ans;
    }
}
```