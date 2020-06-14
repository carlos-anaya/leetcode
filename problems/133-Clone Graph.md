# 133. Clone Graph (Medium)

Given a reference of a `node` in a *connected undirected graph*. Return a **deep copy (clone)** of
the graph.

Each node in the graph contains a *val (int)* and a *list (List[Node])* of its neighbors.

- Example:
> **Input**: graph = [[2,4],[1,3],[2,4],[1,3]] <br>
> **Output**: [[2,4],[1,3],[2,4],[1,3]] <br>
> **Explanation**: There are 4 nodes in the graph. <br>
> 1st node (val = 1)'s neighbors are 2nd node (val = 2) and 4th node (val = 4). <br>
> 2nd node (val = 2)'s neighbors are 1st node (val = 1) and 3rd node (val = 3). <br>
> 3rd node (val = 3)'s neighbors are 2nd node (val = 2) and 4th node (val = 4). <br>
> 4th node (val = 4)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).

## DFS
Do a `pre order DFS` traversal, first clone the parent, then loop over the parent neighbors and then
clone those and add them to the cloned parent. We need a map to keep track of the node and the 
cloned node.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
var nodes map[int]*Node

func cloneGraph(node *Node) *Node {
    nodes = make(map[int]*Node)
    return cloneNode(node)
}

func cloneNode(root *Node) *Node {
    // base case
    if root == nil {
        return nil
    }
    // already cloned
    if node, found := nodes[root.Val]; found {
        return node
    }
    // make a copy of root and put it in the map
    clonedRoot := new(Node)
    clonedRoot.Val = root.Val
    clonedRoot.Neighbors = make([]*Node, len(root.Neighbors))
    nodes[root.Val] = clonedRoot
    // create clones for the neighbors and add them to the cloned root
    for i, neighbor := range root.Neighbors {
        clonedRoot.Neighbors[i] = cloneNode(neighbor)
    }
    return clonedRoot
}
```

## BFS
We can do a `BFS` traversal, creating a new node for each of the neighbors in case they haven't been
cloned before.

**Time: O(n) <br> Space: O(n)**

### Java solution
```java
class Solution {
    public Node cloneGraph(Node node) {
        if(node == null)
            return null;
        
        Map<Node, Node> map = new HashMap();
        map.put(node, new Node(node.val));
        
        Queue<Node> q = new LinkedList();
        q.add(node);
        while(!q.isEmpty()) {
            Node curr = q.poll();
            for(Node nei : curr.neighbors) {
                if(!map.containsKey(nei)) {
                    map.put(nei, new Node(nei.val));
                    q.add(nei);
                }
                map.get(curr).neighbors.add(map.get(nei));
            }
        }
        
        return map.get(node);
    }
}
```