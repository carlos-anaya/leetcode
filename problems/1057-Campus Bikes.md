# 1057. Campus Bikes (Medium)

On a campus represented as a 2D grid, there are `N` workers and `M` bikes, with *N <= M*. Each 
*worker* and *bike* is a 2D coordinate on this grid.

Our goal is to assign a *bike* to each *worker*. Among the available bikes and workers, we choose
the (worker, bike) pair with the shortest **Manhattan distance** between each other, and assign the
bike to that worker. (If there are multiple (worker, bike) pairs with the same shortest Manhattan
distance, we choose the pair with the smallest worker index; if there are multiple ways to do that,
we choose the pair with the smallest bike index). We repeat this process for all workers.

The Manhattan distance between two points is *Manhattan(p1, p2) = |p1.x - p2.x| + |p1.y - p2.y|*.

Return an array *ans* of length N, where ans[i] is the index (0-indexed) of the bike that the i-th
worker is assigned to.

- Example 1:
> **Input**: workers = [[0,0],[2,1]], bikes = [[1,2],[3,3]] <br>
> **Output**: [1,0] <br>
> **Explanation**: Worker 1 grabs Bike 0 as they are closest (without ties). Worker 0 gets Bike 1.

![grid 1](https://assets.leetcode.com/uploads/2019/03/06/1261_example_1_v2.png)

- Example 2:
> **Input**: workers = [[0,0],[1,1],[2,0]], bikes = [[1,0],[2,2],[2,1]] <br>
> **Output**: [0,2,1] <br>
> **Explanation**: Worker 0 grabs Bike 0 at first. Worker 1 and Worker 2 share the same distance to
> Bike 2, thus Worker 1 is assigned to Bike 2, and Worker 2 will take Bike 1.

![grid 2](https://assets.leetcode.com/uploads/2019/03/06/1261_example_2_v2.png)

**Notes**:
1. 0 <= workers[i][j], bikes[i][j] < 1000
2. All worker and bike locations are distinct.
3. 1 <= workers.length <= bikes.length <= 1000

## Brute Force
We use a `min heap` (`priority queue`) to keep an ordered list of workers/bikes pairs given their
Manhattan distance. Then, we poll elements from the queue until we assign a bike to each worker.

**Time: O(W\*B\*log W\*B) <br> Space: O(W\*B)**

```java
class Solution {
    public int[] assignBikes(int[][] workers, int[][] bikes) {
        // create the minheap and fill it
        PriorityQueue<Pair> minHeap = new PriorityQueue(workers.length);
        for(int i = 0; i < workers.length; i++) {
            for(int j = 0; j < bikes.length; j++) {
                int dist = Math.abs(workers[i][0] - bikes[j][0]) + 
                  Math.abs(workers[i][1] - bikes[j][1]);
                minHeap.offer(new Pair(i, j, dist));
            }
        }
        int workersLeft = workers.length;
        boolean[] takenBikes = new boolean[bikes.length];
        int[] ans = new int[workers.length];
        Arrays.fill(ans, -1); // initialize to -1 so we know if worker has a bike
        
        // pop elements from the heap until we've assigned bikes to all the workers
        while(workersLeft > 0) {
            Pair p = minHeap.poll();
            // System.out.println(" " + p.worker + " " + p.bike + " " + p.distance);
            // worker without bike and bike is free
            if(ans[p.worker] == -1 && !takenBikes[p.bike]) {
                ans[p.worker] = p.bike;
                takenBikes[p.bike] = true;
                workersLeft--;
            }
        }
        return ans;
    }
    // custom class to store the worker, bike and distance
    // implements Comparable for the custom comparator
    class Pair implements Comparable<Pair> {
        int worker;
        int bike;
        int distance;
        Pair(int worker, int bike, int distance) {
            this.worker = worker;
            this.bike = bike;
            this.distance = distance;
        }
        @Override
        public int compareTo(Pair otherPair) {
            // sort by distance, then by worker and finally by bike
            // this comes before, so we sort by min value
            if(this.distance != otherPair.distance)
                return this.distance - otherPair.distance;
            if(this.worker != otherPair.worker)
                return this.worker - otherPair.worker;
            return this.bike - otherPair.bike;
        }
    }
}
```

## Optimal
Since we're guaranteed to have a grid of at most 1000 units, then the maximum Manhattan distance can
be 2000. We can use a `Bucket Sort` where each bucket represents a distance. In each bucket we place
worker/bike pairs. Then, to produce a result we loop over the buckets (distances) from min to max
and check the pairs, assigning workers to bikes. We just need to keep track of "assigned" bikes.

**Time: O(W\*B) <br> Space: O(W\*B)**

### Go solution
```go
type pair struct {
    worker, bike int
}

func assignBikes(workers [][]int, bikes [][]int) []int {
    wLen, bLen := len(workers), len(bikes)
    buckets := make([][]pair, 2000) // max x/y distance is 1000, so max manhattan distance is 2000
    
    // fill the buckets by distance
    for w, wPos := range workers {
        for b, bPos := range bikes {
            dist := manDist(wPos, bPos) // helper to get the Manhattan distance
            buckets[dist] = append(buckets[dist], pair{w, b})
        }
    }
    ans := make([]int, wLen)
    takenBykes := make([]bool, bLen) // track which bikes have been taken already
    
    // fill ans array with -1, so we can identify workers without a bike
    for i := 0; i < wLen; i++ {
        ans[i] = -1
    }
    
    // loop over the buckets, starting with the min distance
    for _, buck := range buckets {
        for _, pair := range buck {
            // worker without a bike and bike available
            if ans[pair.worker] == -1 && !takenBykes[pair.bike] {
                ans[pair.worker] = pair.bike
                takenBykes[pair.bike] = true
                
                wLen--
                // short-circuit when all workers got a bike
                // no need to check remaining buckets (longer distances)
                if wLen == 0 { 
                    return ans
                }
            }
        }
    }
    return ans
}
```
