# 979. Distribute Coins in Binary Tree (Medium)

Given the `root` of a binary tree with `N` nodes, each node in the tree has *node.val* `coins`, and
there are **N coins total**.

In one *move*, we may choose two adjacent nodes and move one coin from one node to another.  
(The move may be from parent to child, or from child to parent.)

Return the number of moves required to make *every node* have exactly one coin.

- Example 1:
> **Input**: [3,0,0] <br>
> **Output**: 2 <br>
> **Explanation**: From root one move to left child and one move to right child.

![alt tree1](https://assets.leetcode.com/uploads/2019/01/18/tree1.png)

- Example 2:
> **Input**: [0,3,0] <br>
> **Output**: 3 <br>
> **Explanation**: One move from left child to root, two moves from left to root to right child.

![alt tree2](https://assets.leetcode.com/uploads/2019/01/18/tree2.png)

- Example 3:
> **Input**: [1,0,2] <br>
> **Output**: 2

![alt tree3](https://assets.leetcode.com/uploads/2019/01/18/tree3.png)

- Example 4:
> **Input**: [1,0,0,null,3] <br>
> **Output**: 4

![alt tree4](https://assets.leetcode.com/uploads/2019/01/18/tree4.png)

## Optimal
Do a `post-order` traversal. For a given *node* we first get the **coin difference** of its
children, i.e. the coin *surplus* or *shortage*, and then we consider the *node's coins - 1* (we 
take one since the node requires one coin). That is the value passed to the parent, so at the end
the root node will have a balance of 0. 

But we're interested in the number of moves to "distribute" the coins to all the nodes, so we're
interested in the count of *"surpluses" and "shortages"*. We use *abs()* since we're counting the
moves, not the number of coins, that this node has to make to distribute the coins. See image below:

![alt tree example](https://assets.leetcode.com/users/votrubac/image_1548011422.png)


**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func distributeCoins(root *TreeNode) int {
    moves := 0
    postOrder(root, &moves)
    
    return moves
}

func postOrder(root *TreeNode, moves *int) int {
    if root == nil { return 0}
    
    l, r := postOrder(root.Left, moves), postOrder(root.Right, moves)
    // global count of moves
    *moves += abs(l) + abs(r)
    // this node returns it's children coins (l + r)
    // plus the node coins (root.Val - 1)
    // we substract 1 since the node requires one coin
    return root.Val + l + r - 1
}
```
### Java solution
```java
class Solution {
    int moves;
    public int distributeCoins(TreeNode root) {
        this.moves = 0;
        postOrder(root);
        return this.moves;
    }
    
    private int postOrder(TreeNode root) {
        if(root == null)
            return 0;
        int l = postOrder(root.left);
        int r = postOrder(root.right);
        
        this.moves += Math.abs(l) + Math.abs(r);
        
        return root.val + l + r - 1;
    }
}
```