# 743. Network Delay Time (Medium)

There are `N` network nodes, labelled 1 to N.

Given `times`, a list of travel times as *directed* edges `times[i] = (u, v, w)`, where `u` is the
source node, `v` is the target node, and `w` is the time it takes for a signal to travel from 
source to target.

Now, we send a signal from a certain node `K`. How long will it take for all nodes to receive the
signal? If it is impossible, return -1.

- Example 1:
> **Input**: times = [[2,1,1],[2,3,1],[3,4,1]], N = 4, K = 2 <br>
> **Output**: 2 <br>
> **Explanation**: See the image below.

![alt network](https://assets.leetcode.com/uploads/2019/05/23/931_example_1.png)

## Optimal
Since the graph is **weighted**, a simple *BFS traversal* won't work, then we need to apply the 
`Dijkstra algorithm`.
1. Create an *adjacency list*.
2. Use a *min heap* to track the next nodes to be processed. In this case we will be storing pairs:
  *<weight, node>*, and the *min heap* will sort the pairs by *weight*.
3. Use a *visited set* to track the nodes already processed. In case we would need the delay to each
  node, we could use a map instead.
4. At the end, if all the nodes were *visited*, the we have a solution that is the max of all the 
  weights (i.e. the longest it took the furthest node to receive the signal.)
  - In case visited doesn't contain all the nodes, then return -1. I.e. not all nodes are reachable
  from current K.

**Time: O(n) <br> Space: O(n)**

### Java solution
```java
class Solution {
    public int networkDelayTime(int[][] times, int N, int K) {
        Map<Integer, List<int[]>> graph = new HashMap();
        // fill the adj map. for each node take the neighbors and weights/delays
        for(int[] edge : times) {
            int s = edge[0], d = edge[1], w = edge[2];
            if(!graph.containsKey(s))
                graph.put(s, new ArrayList());
            graph.get(s).add(new int[]{d, w});
        }
        
        // keep track of the nodes already processed
        Set<Integer> delays = new HashSet();
        // order the heap by weight
        PriorityQueue<int[]> heap = new PriorityQueue<int[]>(N, (a, b) -> a[0] - b[0]);
        heap.add(new int[]{0, K});
        int maxDelay = 0;
        
        while(!heap.isEmpty()) {
            int delay = heap.peek()[0];
            int curr = heap.poll()[1];

            // skip this node if already visited
            // if already visited, it was a min value
            if(delays.contains(curr))
                continue;
            // save the delay associated to this node
            delays.add(curr);
            maxDelay = Math.max(maxDelay, delay);
            
            // node might not have neighbors
            if(!graph.containsKey(curr))
                continue;
            // add the neighbors and weights/delays to the heap.
            for(int[] nei : graph.get(curr)) {
                // can't add the validation delays.contains(nei[0]) here
                // since this nei[0] could be in the heap with a higher weight
                heap.add(new int[]{delay + nei[1], nei[0]});
            }
        }
        // check if we visited all the nodes
        if(delays.size() == N)
            return maxDelay;
        return -1;
    }
}
```