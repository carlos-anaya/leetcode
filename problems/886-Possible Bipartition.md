# 886. Possible Bipartition (Medium)

Given a set of `N `people (numbered *1, 2, ..., N*), we would like to split everyone into two groups
of any size. Each person may dislike some other people, and they should not go into the same group. 

Formally, if `dislikes[i] = [a, b]`, it means it is not allowed to put the people numbered `a` and
`b` into the same group.

Return true if and only if it is possible to split everyone into **two groups** in this way.

- Example 1:
> **Input**: N = 4, dislikes = [[1,2],[1,3],[2,4]] <br>
> **Output**: true <br>
> **Explanation**: group1 [1,4], group2 [2,3]
- Example 2:
> **Input**: N = 3, dislikes = [[1,2],[1,3],[2,3]] <br>
> **Output**: false
- Example 2:
> **Input**: N = 5, dislikes = [[1,2],[2,3],[3,4],[4,5],[1,5]] <br>
> **Output**: false

## Optimal I - DFS
This problem is about a `bipartite graph`. *Bipartition* is the property of a graph so the nodes can
be splitted into two different sets such that there are no edges between nodes in the same set.

In other words, we can do a `graph coloring` setting a node of color *A* and all the neighbors (via
the edges) are of color *B* and so on. Then, any *A* node can only be connected by *B* nodes and 
viceversa.

![bipartite graph](https://upload.wikimedia.org/wikipedia/commons/d/d6/Biclique_K_3_5.svg)

We can do a `depth-first traversal`. First we create an *adjacency list*, then we loop over the
people and in case the person has no group assigned we do a traversal. For each person we check
his/her neighbors. If the neighbor doesn't have a group, we assign the parent's opposite group. If
any of the neighbors already has a group and it is the same as the "parent" group, we return false.

**Time: O(V+E)** (loop over people (P) and neighbors (E)) <br> 
**Space: O(V+E)** (the adjacency list has size V+E)

### Go solution
```go
var adjList [][]int
var groups []int // use 3 statuses: 0 - not assigned, 1 group A, -1 group B

func possibleBipartition(N int, dislikes [][]int) bool {
    buildAdjList(N, dislikes)
    groups = make([]int, N+1)
    
    // loop over the people, check if its not assigned and if it yields a conflict
    // need to loop over all the people since the graph may be disconnected
    for i := 1; i <= N; i++ {
        if groups[i] == 0 && !dfs(i, 1) {
            return false
        }
    }
    return true
}

func dfs(curr, group int) bool {
    // if already assigned, check if it's the same prev group
    // if it is not the same group it is a conflict, so return false
    if groups[curr] != 0 {
        return groups[curr] == group
    }
    groups[curr] = group;
    
    // check all the disliked people of curr person and assign them to the other group
    for _, n := range adjList[curr] {
        if !dfs(n, group * -1) {
            return false
        }
    }
    return true
}

// builds an adjacency list, every row are the dislikes for person[i]
func buildAdjList(N int, dislikes [][]int) {
    adjList = make([][]int, N+1)
    for i := 1; i <= N; i++ {
        adjList[i] = make([]int, 0, N)
    }
    for _, d := range dislikes {
        adjList[d[0]] = append(adjList[d[0]], d[1])
        adjList[d[1]] = append(adjList[d[1]], d[0])
    }
}
```

## Optimal II - BFS
We can also do a `bread-first traversal`. We first create an *adjacency list* and then loop over the
nodes (people). The graph might be disconnected, so the need for the loop for each person. If the
person is in a group already we just skip him.

Then, we assign the current person to the *group1* and use a **queue** to keep track of the
neighbors to be processed. We loop over the neighbors and assign the "parent's" opposite group. If
any of the neighbors already has a color and it's the same as the parent, there's a conflict so we
return false.

**Time: O(V+E) <br> Space: O(E)**

### Java solution
```java
class Solution {
    public boolean possibleBipartition(int N, int[][] dislikes) {
        List<List<Integer>> adjList = new ArrayList();
        for(int i = 0; i <= N; i++) {
            adjList.add(new ArrayList());
        }
        // initialize the adj list with the edges (dislikes)
        for(int[] d : dislikes) {
            adjList.get(d[0]).add(d[1]);
            adjList.get(d[1]).add(d[0]);
        }
        
        int[] groups = new int[N+1];
        Queue<Integer> q = new LinkedList();
        
        // loop over all the persons, since it might not be fully connected
        for(int i = 1; i <= N; i++) {
            // person already assigned to a group so skip the person
            if(groups[i] != 0)
                continue;
            
            q.add(i); // add current person to the queue and set the group 1
            groups[i] = 1;
            // process the neighbors of curr person
            while(!q.isEmpty()) {
                int curr = q.remove();
                int currGroup = groups[curr];
                
                // find the neighbors 
                // if neighbor has no group, put them in the opposite group
                // if neighbor has the same group as curr person, it's a conflict
                for(int nei : adjList.get(curr)) {
                    if(groups[nei] == 0) {
                        q.add(nei);
                        groups[nei] = currGroup * -1;
                    } else if (groups[nei] == currGroup) {
                        return false;
                    }
                }
            }
        }
        // no conflict
        return true;
    }
}
```