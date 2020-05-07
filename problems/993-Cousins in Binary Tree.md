# 993. Cousins in Binary Tree (Easy)

In a binary tree, the root node is at depth `0`, and children of each depth `k` node are at depth 
`k+1`.

Two nodes of a binary tree are **cousins** if they have the same depth, but have different parents.

We are given the `root` of a binary tree with unique values, and the values `x` and `y` of two
different nodes in the tree.

Return true if the nodes corresponding to the values x and y are cousins.

- Example 1:
> **Input**: root = [1,2,3,null,4,null,5] x = 2, y = 3 <br>
> **Output**: true
- Example 2:
> **Input**: root = [1,2,3,null,4,null,5] x = 2, y = 5 <br>
> **Output**: false

![alt tree](https://assets.leetcode.com/uploads/2019/02/12/q1248-02.png)

## Brute Force
We can do an **pre-order** traversal and store in a *map* each node's depth and parent. Then, we
just need to verify that the nodes exist in the map and they have same depth but different parent.

**Time: O(n) <br> Space: O(n)**

## Optimal
Instead of using a *map* we can just have two pointers/static variables to *tuples* (<level, parent>
pairs) of *X* and *Y*. We do the *pre-order* traversal and check if the current node is *X* or *Y*,
and if it's not, then we continue the traversal.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
type tuple struct {
    level int
    parent *TreeNode
}

func isCousins(root *TreeNode, x int, y int) bool {
    tupleX, tupleY := &tuple{}, &tuple{}
    
    dfs(root, nil, 0, x, y, tupleX, tupleY)
    
    // x and y must have same depth but different parent
    return tupleX.level == tupleY.level &&
        tupleX.parent != tupleY.parent
}

func dfs(root, parent *TreeNode, level, x, y int, tupleX, tupleY *tuple) {
    if root == nil {
        return
    }
    if root.Val == x {
        tupleX.level = level
        tupleX.parent = parent
    } else if root.Val == y {
        tupleY.level = level
        tupleY.parent = parent
    } else {
        level++
        dfs(root.Left, root, level, x, y, tupleX, tupleY)
        dfs(root.Right, root, level, x, y, tupleX, tupleY)
    }
}
```
### Java solution
```java
class Solution {
    class Tuple {
        int level;
        TreeNode parent;
    }
    
    Tuple tx, ty;
    
    public boolean isCousins(TreeNode root, int x, int y) {
        this.tx = new Tuple();
        this.ty = new Tuple();
        
        dfs(root, null, 0, x, y);
        
        return this.tx.parent != this.ty.parent && 
            this.tx.level == this.ty.level;
    }
    
    private void dfs(TreeNode root, TreeNode parent, int level, int x, int y) {
        if(root == null)
            return;
        
        if(root.val == x) {
            this.tx.level = level;
            this.tx.parent = parent;
        }
        if(root.val == y) {
            this.ty.level = level;
            this.ty.parent = parent;
        } else {
            level++;
            dfs(root.left, root, level, x, y);
            dfs(root.right, root, level, x, y);
        }
    }
}
```