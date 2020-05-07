# 124. Binary Tree Maximum Path Sum

Given a *non-empty* binary tree, find the maximum path sum.

For this problem, a path is defined as any sequence of nodes from some starting node to any node in
the tree along the parent-child connections. The path must contain **at least one node** and 
**does not** need to go through the root.

- Example 1:
> **Input**: [1,2,3] <br>
> **Output**: 6 <br>
> **Explanation**: All the nodes are make a path
- Example 2:
> **Input**: [-10,9,20,null,null,15,7] <br>
> **Output**: 42 <br>
> **Explanation**: -10 and 9 are ignored, then the path is 15 -> 20 -> 7
- Example 3:
> **Input**: [-10, -1, -5] <br>
> **Output**: -1 <br>
> **Explanation**: The left child of root (-1) is the best path

## Optimal
Consider the tree below. If *9* were the only node, then the maxPathSum is that node. Now let's
consider that the root is *-10* and *9* is the only child. It's obvious that *-10* should be ignored
and *9* should be the solution. But if instead of *9* it were a *-19*, we should ignore the child
and just keep the root.

If we do a **post-order** traversal, and use an *instance variable* or a *pointer* we can keep track
best max path so far, ignoring the left/right paths if they're negative. To update `maxSum` we have
4 scenarios:
1. pick only root
2. pick root and left branch (if left branch has a positive gain)
3. pick root and right branch (if right branch has a positive gain)
4. pick root and both child branches (both have a positive gain).

The recursive function has to return the best path so far, containing the root value and either
the best of the left or right paths (if they exist and have a positive gain).

    -10
    /  \
    9  _20_
      /   \
    _15_  _7_

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func maxPathSum(root *TreeNode) int {
    // start with MinInt32 so it always chooses at least 1 node
    maxSum := math.MinInt32
    pathSum(root, &maxSum)
    
    return maxSum
}

func pathSum(root *TreeNode, maxSum *int) int {
    if root == nil {
        return 0
    }
    
    // the four choices are root, root + l, root + r or root + l + r
    // if left or right childs are negative, we ignore them by using 0
    l := max(0, pathSum(root.Left, maxSum))
    r := max(0, pathSum(root.Right, maxSum))
    
    // if the curr node + any children makes a better sum, we take it
    *maxSum = max(*maxSum, l + r + root.Val)
    
    // return the current root + the best path, so the parent can decide
    // which children makes a better path
    return max(l, r) + root.Val
}
```
### Java solution
```java
class Solution {
    int maxPathSum;
    public int maxPathSum(TreeNode root) {
        maxPathSum = Integer.MIN_VALUE;
        maxPath(root);
        
        return maxPathSum;
    }
    
    private int maxPath(TreeNode root) {
        if(root == null)
            return 0;
        
        int l = Math.max(0, maxPath(root.left));
        int r = Math.max(0, maxPath(root.right));
        // using max(0, maxPath(child)) is equivalent to:
        // if(root.left != null) {
        //     l = maxPath(root.left);
        //     if(l < 0) // if negative, ignore the branch
        //         l = 0;
        // }

        maxPathSum = Math.max(maxPathSum, root.val + l + r);
        
        return Math.max(r, l) + root.val;
    }
}
```