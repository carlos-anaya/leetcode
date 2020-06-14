# 785. Is Graph Bipartite? (Medium)

Given an undirected `graph`, return true if and only if it is **bipartite**.

Recall that a graph is *bipartite* if we can split it's set of nodes into two independent subsets 
*A* and *B* such that *every edge* in the graph has one node in *A* and another node in *B*.

The graph is given in the following form: `graph[i]` is a list of indexes `j` for which the edge
between nodes `i` and `j` exists.  Each node is an integer between 0 and graph.length - 1.  There
are no self edges or parallel edges: `graph[i]` does not contain `i`, and it doesn't contain any
element twice.

- Example 1:
> **Input**: [[1,3], [0,2], [1,3], [0,2]] <br>
> **Output**: true <br>
> **Explanation**: We can divide the vertices into two groups: {0, 2} and {1, 3} <br>
> 0--1 <br>
> |..| <br>
> |..| <br>
> 3--2
- Example 2:
> **Input**: [[1,2,3], [0,2], [0,1,3], [0,2]] <br>
> **Output**: false <br>
> **Explanation**: We cannot find a way to divide the set of nodes into two independent subsets <br>
> 0--1 <br>
> |\\.| <br>
> |.\\| <br>
> 3--2

## Optimal
We do a `graph coloring` using a `BFS` traversal. We loop over the nodes and assign a color if they
don't already have one. Then, we do a BFS traversal for the neighbors and set the opposite color as
the base node. In case theres a neighbor node already colored and with the same color as base node,
then there's no way to split the graph in two sets.

This problem is similar to [886-Possible Bipartition](886-Possible%20Bipartition.md). 

![alt graph coloring](https://leetcode.com/problems/is-graph-bipartite/Figures/785/color.png)

**Time: O(V+E) <br> Space: O(E)**

### Java solution
```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        int[] colors = new int[graph.length];
        Queue<Integer> q = new LinkedList();
        for(int i = 0; i < graph.length; i++) {
            if(colors[i] != 0) // node already has been colored
                continue;
            colors[i] = 1; // set default color
            q.add(i);
            while(!q.isEmpty()) {
                int curr = q.poll();
                for(int nei : graph[curr]) {
                    // neighbor has same color as parent
                    if(colors[nei] == colors[curr]) 
                        return false;
                    // neighbor don't have a color, so use the opposite as parent
                    if(colors[nei] == 0) {
                        colors[nei] = colors[curr] * -1;
                        q.add(nei);
                    }
                }
            }
        }
        return true;
    }
}
```